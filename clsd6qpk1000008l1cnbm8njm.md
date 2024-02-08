---
title: "The Sfx shader language and shader variants"
datePublished: Tue Sep 19 2023 17:57:03 GMT+0000 (Coordinated Universal Time)
cuid: clsd6qpk1000008l1cnbm8njm
slug: the-sfx-shader-language-and-shader-variants
canonical: https://roderickkennedy.com/dbgdiary/the-sfx-shader-language-and-shader-variants

---

The Sfx shader language and shader variants
===========================================

19 Sep

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

For some reason there seems to be a lot of interest in custom and alternative game engine development now.

Whatever the cause of this mysterious uptick, I thought I'd post a bit about what we do at [Simul](https://simul.co/).

Handling shaders can be a vexed issue in native development. If you're writing a single shader, or a matched pair of vertex and pixel shaders, things start very simply, whether you're using GLSL or HLSL. You'll have a vertex shader file, like this:

>     cbuffer CameraConstants
>     {
>         float4x4 viewProj;
>     };
>     struct VS_IN
>     {
>         float4 a_Positions : POSITION;
>     };
>     struct VS_OUT
>     {
>         float4 o_Position : SV_Position;
>     };
>     VS_OUT main(VS_IN IN)
>     {
>         VS_OUT OUT;
>         OUT.o_Position = mul(viewProj,IN.a_Positions);
>         return OUT;
>     }

And a pixel shader file:

>     struct PS_IN
>     {
>         float4 i_Position : SV_Position;
>     };
>     struct PS_OUT
>     {
>         float4 o_Colour : SV_Target0;
>     };
>     
>     PS_OUT main(PS_IN IN)
>     {
>         PS_OUT OUT;
>         OUT.o_Colour = float4(1.0,0,0,0);
>         return OUT;
>     }

This above is HLSL. GLSL looks similar inside the functions, but the surrounding declarations are different, e.g.

>     #version 450
>     layout(std140, binding = 0) uniform CameraConstants {
>         mat4 modelViewProj;
>     };
>     layout(location = 0) in vec4 a_Positions;
>     void main() {
>         gl_Position = modelViewProj * a_Positions;
>     }
> 
>     #version 450
>     layout(location = 0) in flat uvec2 i_TexCoord;
>     layout(location = 0) out vec4 o_Colour;
>     void main() {
>         o_Colour = vec4(1.0,0,0,0);
>     }

The trick comes as your number of shaders starts to increase, and you start needing multiple combinations - different materials might need shaders with different inputs. 3D models may have different vertex setups, so you need a vertex shader that exactly matches the vertex layout you're sending. And to combine a vertex and pixel shader, you must ensure that the vertex shader output matches the pixel shader's input.

It can get unwieldy.

At Simul, we developed [Sfx](https://github.com/simul/Platform/tree/main/Applications/Sfx), a sort of wrapper or meta- language to help manage shaders. The simple vertex-pixel combo above, in Sfx, looks like this:

>     cbuffer CameraConstants
>     {
>         float4x4 viewProj;
>     };
>     struct VS_IN
>     {
>         float4 a_Positions : POSITION;
>     };
>     struct VS_TO_PS
>     {
>         float4 o_Position : SV_Position;
>     };
>     struct PS_OUT
>     {
>         float4 o_Colour : SV_Target0;
>     };
>     
>     VS_TO_PS VS_Main(VS_IN IN)
>     {
>         VS_TO_PS OUT;
>         OUT.o_Position = mul(viewProj,IN.a_Positions);
>         return OUT;
>     }
>     
>     PS_OUT PS_Main(VS_TO_PS IN)
>     {
>         PS_OUT OUT;
>         OUT.o_Colour = float4(1.0,0,0,0);
>         return OUT;
>     }
>     
>     VertexShader vs_main	= CompileShader(vs_5_0,VS_Main());
>     PixelShader ps_main	= CompileShader(ps_5_0, PS_Main());
>     
>     technique simpledraw
>     {
>         pass p0
>         {
>             SetRasterizerState( wireframeRasterizer );
>             SetDepthStencilState( DisableDepth, 0 );
>             SetBlendState(AddBlend, vec4(0.0,0.0,0.0,0.0), 0xFFFFFFFF );
>             SetVertexShader(vs_main);
>             SetPixelShader(ps_main);
>         }
>     }

Nice and simple: one file instead of two. And we've added some useful renderstate setup. But how is this used?

Sfx is a _transpiler_. It doesn't compile binary shaders, rather it translates its shaders to a target language. We might invoke it with

    Sfx.exe simple.sfx -I"shader_includes" -O"C:/Teleport/build_pc_client/shaderbin/$PLATFORM_NAME" -P"Sfx/DirectX12.json" -P"Sfx/Vulkan.json" 

This means, transpile the effect file "simple.sfx" into shaders, using the profiles "DirectX12.json" and "Vulkan.json". These setup files tell Sfx how to generate shader code. For DirectX 12, it will generate hlsl shaders for Microsoft's dxc compiler. For Vulkan, it will create glsl shaders for glslangvalidator.exe. In both cases, it will invoke the relevant compiler, and bundle the output into an effect binary, simple.sfxb, and an effect setup file simple.sfxo. The former contains the shaders, the latter contains instructions on how they can be used in passes.

Then at runtime, a small library (specific to your chosen graphics API) loads up the binaries and applies the shaders, and renderstate when needed.

I called "simple.sfx" an _effect_ file. This unfashionable concept was all the rage when shaders were first in widespread use, but fell out of favour as more developers started to use commercial game engines, which handled that sort of thing internally. But I think it still has a lot of value. Although each shader language is essentially a special-purpose version of C, the effect file has a meta-language that surrounds it which helps us build and manage collections of shaders.

Originally Sfx was modelled on the effect file format of Direct3D, which was mothballed some years ago. But it's grown into its own thing now. For example, we can declare shader _variants_ as follows:

>     shader vec4 PS_Var(vertexOutput IN,variant bool textured) : SV_TARGET
>     {
>         vec4 colour;
>         if(textured)
>         {
>             colour=texture.Sample(wrapSamplerState, IN.texCoords.xy);
>         }
>         else
>         {
>             colour=globalColour;
>         }
>         return colour;
>     }
>     

This shader has two variants, one with a texture lookup and one without. In the pass declaration, we invoke this with:

>     PixelShader ps_var	= CompileShader(ps_5_0, PS_Var());
>     technique test
>     {
>         pass p0
>         {
>             SetVertexShader(vs_main);
>             SetPixelShaderVariants(ps_var({false,true}));
>         }
>     }

Sfx will now generate two versions of the pixel shader, one with texture=true, one with false. Although _if(textured)_ looks above like runtime code, each version will be compiled with the value of _textured_ constant: it will be optimized down to static code. At runtime though, we can choose which version we want. We could also specify two or more different shaders, e.g.

            SetPixelShaderVariants(ps_var1,ps_var2,ps_debug);

It would have been easy enough to just write two versions of the pass, one with textures and one without. The power of the variant system comes as shaders multiply. Combining, say, three vertex shaders with two pixel shaders:

            SetVertexShader(vs_main,vs_alternative);
            SetPixelShaderVariants(ps_var({false,true}),ps_test);

Now, assuming they are mutually compatible, we would have six possible passes. Consider for example, creating specialized debug views of your 3D objects: for each view, you can just add a pixel shader variant to the pass.

So for larger projects, a system like Sfx can be invaluable in quickly writing efficient GPU code. If you do find yourself programming natively, without the comforts of a game engine, it may be just the ticket.

As of today, Sfx supports not only Vulkan and OpenGL flavours of GLSL and D3D11 and 12 flavours of HLSL, but also certain proprietary platforms that I'm not permitted to name. We codenamed them Spectrum and Commodore, for convenience.

All the source for Sfx is at [https://github.com/simul/Platform/tree/main/Applications/Sfx](https://github.com/simul/Platform/tree/main/Applications/Sfx).

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

The Sfx shader language and shader variants
===========================================

19 Sep

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

For some reason there seems to be a lot of interest in custom and alternative game engine development now.

Whatever the cause of this mysterious uptick, I thought I'd post a bit about what we do at [Simul](https://simul.co/).

Handling shaders can be a vexed issue in native development. If you're writing a single shader, or a matched pair of vertex and pixel shaders, things start very simply, whether you're using GLSL or HLSL. You'll have a vertex shader file, like this:

>     cbuffer CameraConstants
>     {
>         float4x4 viewProj;
>     };
>     struct VS_IN
>     {
>         float4 a_Positions : POSITION;
>     };
>     struct VS_OUT
>     {
>         float4 o_Position : SV_Position;
>     };
>     VS_OUT main(VS_IN IN)
>     {
>         VS_OUT OUT;
>         OUT.o_Position = mul(viewProj,IN.a_Positions);
>         return OUT;
>     }

And a pixel shader file:

>     struct PS_IN
>     {
>         float4 i_Position : SV_Position;
>     };
>     struct PS_OUT
>     {
>         float4 o_Colour : SV_Target0;
>     };
>     
>     PS_OUT main(PS_IN IN)
>     {
>         PS_OUT OUT;
>         OUT.o_Colour = float4(1.0,0,0,0);
>         return OUT;
>     }

This above is HLSL. GLSL looks similar inside the functions, but the surrounding declarations are different, e.g.

>     #version 450
>     layout(std140, binding = 0) uniform CameraConstants {
>         mat4 modelViewProj;
>     };
>     layout(location = 0) in vec4 a_Positions;
>     void main() {
>         gl_Position = modelViewProj * a_Positions;
>     }
> 
>     #version 450
>     layout(location = 0) in flat uvec2 i_TexCoord;
>     layout(location = 0) out vec4 o_Colour;
>     void main() {
>         o_Colour = vec4(1.0,0,0,0);
>     }

The trick comes as your number of shaders starts to increase, and you start needing multiple combinations - different materials might need shaders with different inputs. 3D models may have different vertex setups, so you need a vertex shader that exactly matches the vertex layout you're sending. And to combine a vertex and pixel shader, you must ensure that the vertex shader output matches the pixel shader's input.

It can get unwieldy.

At Simul, we developed [Sfx](https://github.com/simul/Platform/tree/main/Applications/Sfx), a sort of wrapper or meta- language to help manage shaders. The simple vertex-pixel combo above, in Sfx, looks like this:

>     cbuffer CameraConstants
>     {
>         float4x4 viewProj;
>     };
>     struct VS_IN
>     {
>         float4 a_Positions : POSITION;
>     };
>     struct VS_TO_PS
>     {
>         float4 o_Position : SV_Position;
>     };
>     struct PS_OUT
>     {
>         float4 o_Colour : SV_Target0;
>     };
>     
>     VS_TO_PS VS_Main(VS_IN IN)
>     {
>         VS_TO_PS OUT;
>         OUT.o_Position = mul(viewProj,IN.a_Positions);
>         return OUT;
>     }
>     
>     PS_OUT PS_Main(VS_TO_PS IN)
>     {
>         PS_OUT OUT;
>         OUT.o_Colour = float4(1.0,0,0,0);
>         return OUT;
>     }
>     
>     VertexShader vs_main	= CompileShader(vs_5_0,VS_Main());
>     PixelShader ps_main	= CompileShader(ps_5_0, PS_Main());
>     
>     technique simpledraw
>     {
>         pass p0
>         {
>             SetRasterizerState( wireframeRasterizer );
>             SetDepthStencilState( DisableDepth, 0 );
>             SetBlendState(AddBlend, vec4(0.0,0.0,0.0,0.0), 0xFFFFFFFF );
>             SetVertexShader(vs_main);
>             SetPixelShader(ps_main);
>         }
>     }

Nice and simple: one file instead of two. And we've added some useful renderstate setup. But how is this used?

Sfx is a _transpiler_. It doesn't compile binary shaders, rather it translates its shaders to a target language. We might invoke it with

    Sfx.exe simple.sfx -I"shader_includes" -O"C:/Teleport/build_pc_client/shaderbin/$PLATFORM_NAME" -P"Sfx/DirectX12.json" -P"Sfx/Vulkan.json" 

This means, transpile the effect file "simple.sfx" into shaders, using the profiles "DirectX12.json" and "Vulkan.json". These setup files tell Sfx how to generate shader code. For DirectX 12, it will generate hlsl shaders for Microsoft's dxc compiler. For Vulkan, it will create glsl shaders for glslangvalidator.exe. In both cases, it will invoke the relevant compiler, and bundle the output into an effect binary, simple.sfxb, and an effect setup file simple.sfxo. The former contains the shaders, the latter contains instructions on how they can be used in passes.

Then at runtime, a small library (specific to your chosen graphics API) loads up the binaries and applies the shaders, and renderstate when needed.

I called "simple.sfx" an _effect_ file. This unfashionable concept was all the rage when shaders were first in widespread use, but fell out of favour as more developers started to use commercial game engines, which handled that sort of thing internally. But I think it still has a lot of value. Although each shader language is essentially a special-purpose version of C, the effect file has a meta-language that surrounds it which helps us build and manage collections of shaders.

Originally Sfx was modelled on the effect file format of Direct3D, which was mothballed some years ago. But it's grown into its own thing now. For example, we can declare shader _variants_ as follows:

>     shader vec4 PS_Var(vertexOutput IN,variant bool textured) : SV_TARGET
>     {
>         vec4 colour;
>         if(textured)
>         {
>             colour=texture.Sample(wrapSamplerState, IN.texCoords.xy);
>         }
>         else
>         {
>             colour=globalColour;
>         }
>         return colour;
>     }
>     

This shader has two variants, one with a texture lookup and one without. In the pass declaration, we invoke this with:

>     PixelShader ps_var	= CompileShader(ps_5_0, PS_Var());
>     technique test
>     {
>         pass p0
>         {
>             SetVertexShader(vs_main);
>             SetPixelShaderVariants(ps_var({false,true}));
>         }
>     }

Sfx will now generate two versions of the pixel shader, one with texture=true, one with false. Although _if(textured)_ looks above like runtime code, each version will be compiled with the value of _textured_ constant: it will be optimized down to static code. At runtime though, we can choose which version we want. We could also specify two or more different shaders, e.g.

            SetPixelShaderVariants(ps_var1,ps_var2,ps_debug);

It would have been easy enough to just write two versions of the pass, one with textures and one without. The power of the variant system comes as shaders multiply. Combining, say, three vertex shaders with two pixel shaders:

            SetVertexShader(vs_main,vs_alternative);
            SetPixelShaderVariants(ps_var({false,true}),ps_test);

Now, assuming they are mutually compatible, we would have six possible passes. Consider for example, creating specialized debug views of your 3D objects: for each view, you can just add a pixel shader variant to the pass.

So for larger projects, a system like Sfx can be invaluable in quickly writing efficient GPU code. If you do find yourself programming natively, without the comforts of a game engine, it may be just the ticket.

As of today, Sfx supports not only Vulkan and OpenGL flavours of GLSL and D3D11 and 12 flavours of HLSL, but also certain proprietary platforms that I'm not permitted to name. We codenamed them Spectrum and Commodore, for convenience.

All the source for Sfx is at [https://github.com/simul/Platform/tree/main/Applications/Sfx](https://github.com/simul/Platform/tree/main/Applications/Sfx).

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)