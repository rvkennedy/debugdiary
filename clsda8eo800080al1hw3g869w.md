---
title: "Sfx: a generic effect compiler for shaders"
seoTitle: "Sfx: a generic effect compiler for shaders"
datePublished: Sat May 20 2017 13:03:00 GMT+0000 (Coordinated Universal Time)
cuid: clsda8eo800080al1hw3g869w
slug: sfx-a-generic-effect-compiler-for-shaders-1
canonical: https://roderickkennedy.com/dbgdiary/sfx-generic-effect-compiler-for-shaders

---

# Sfx: a generic effect compiler for shaders

20 May

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Update (19/9/2021): source code is at [https://github.com/simul/Platform](https://github.com/simul/Platform), see Applications/Sfx.

[So Microsoft, Nvidia and t](https://github.com/simul/Platform)he rest used to support *effect files*: a text source file that contained multiple shaders, but also "techniques" and "passes", where each pass can have state specified: blending, rasterization etc.

Some time ago, for reasons I guess of supply and demand, fx fell out of fashion. Microsoft still provides the D3D11 version of its Effects library as open source [here](https://github.com/Microsoft/FX11), and this reads binary output that the fxc tool can create. But Fxc is being replaced by by [this](https://github.com/Microsoft/DirectXShaderCompiler), which doesn't support effects. Nvidia's Tristan Lorach proposed a new framework, nvFX ([pdf](https://developer.nvidia.com/sites/default/files/akamai/gamedev/docs/nvFX%20A%20New%20Shader-Effect%20Framework.pdf)), but I don't think it's under active development.

D3D12 has no effect support. So we need to add it.

My approach at Simul is called Sfx. The idea is to take an initial file that's compatible with Microsoft's HLSL effect format, with the code written in HLSL. Sfx will extract the passes and compile all the relevant shaders by building smaller individual shader source files and calling an external compiler. A small json file will specify which compiler to use, and various other parameters to allow translation from HLSL to whatever language the compiler expects.

For example, HLSL.json looks like:

{ "compiler": "C:/Program Files (x86)/Windows Kits/10/bin/x64/fxc.exe", "defaultOptions": "/T {shader\_model} /nologo", "sourceExtension": "hlsl", "outputExtension": "cso", "outputOption": "/Fo", "entryPointOption": "/E{name}", "multiplePixelOutputFormats": false }

So Sfx should be compiler-independent. It outputs two things: a text file with the .sfxo extension, and a number of platform-specific shader binaries. The sfxo looks like this:

SFX texture fontTexture 2d read\_only 0 single SamplerState clampSamplerState 9,LINEAR,CLAMP,CLAMP,CLAMP, SamplerState cmcNearestSamplerState 13,POINT,CLAMP,MIRROR,CLAMP, RasterizerState RenderNoCull (false,CULL\_NONE,0,0,false,FILL\_SOLID,true,false,false,0) RasterizerState wireframeRasterizer (true,CULL\_NONE,0,0,false,FILL\_WIREFRAME,false,false,false,0) BlendState AlphaBlendRGB false,(true),1,1,4,5,0,0,(7) DepthStencilState DisableDepth false,0,4 group { technique backg { pass p0 { rasterizer: RenderNoCull depthstencil: DisableDepth 0 blend: AlphaBlendRGB (0,0,0,0) 4294967295 vertex: font\_FontVertexShader\_vv.cso,(),(),() pixel: font\_FontPixelShader.cso,(),(),() } } }

This stands to improve over time. In this case, we've taken an sfx file containing this definition:

VertexShader vs = CompileShader(vs\_4\_0, FontVertexShader());

technique text { pass p0 { SetRasterizerState( RenderNoCull ); SetDepthStencilState( DisableDepth, 0 ); SetBlendState(AddBlendRGB,vec4( 0.0, 0.0, 0.0, 0.0), 0xFFFFFFFF ); SetGeometryShader(NULL); SetVertexShader(vs); SetPixelShader(CompileShader(ps\_4\_0,FontPixelShader())); } }

so we've compiled FontPixelShader() and FontVertexShader() into the cso files: this example is for HLSL. As we extend Sfx to other languages, the json definition in particular will become more complex - possibly using regexes to specify how HLSL is translated into other C-style shader languages.

[Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

# Sfx: a generic effect compiler for shaders

20 May

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Update (19/9/2021): source code is at [https://github.com/simul/Platform](https://github.com/simul/Platform), see Applications/Sfx.

[So Microsoft, Nvidia and t](https://github.com/simul/Platform)he rest used to support *effect files*: a text source file that contained multiple shaders, but also "techniques" and "passes", where each pass can have state specified: blending, rasterization etc.

Some time ago, for reasons I guess of supply and demand, fx fell out of fashion. Microsoft still provides the D3D11 version of its Effects library as open source [here](https://github.com/Microsoft/FX11), and this reads binary output that the fxc tool can create. But Fxc is being replaced by by [this](https://github.com/Microsoft/DirectXShaderCompiler), which doesn't support effects. Nvidia's Tristan Lorach proposed a new framework, nvFX ([pdf](https://developer.nvidia.com/sites/default/files/akamai/gamedev/docs/nvFX%20A%20New%20Shader-Effect%20Framework.pdf)), but I don't think it's under active development.

D3D12 has no effect support. So we need to add it.

My approach at Simul is called Sfx. The idea is to take an initial file that's compatible with Microsoft's HLSL effect format, with the code written in HLSL. Sfx will extract the passes and compile all the relevant shaders by building smaller individual shader source files and calling an external compiler. A small json file will specify which compiler to use, and various other parameters to allow translation from HLSL to whatever language the compiler expects.

For example, HLSL.json looks like:

{ "compiler": "C:/Program Files (x86)/Windows Kits/10/bin/x64/fxc.exe", "defaultOptions": "/T {shader\_model} /nologo", "sourceExtension": "hlsl", "outputExtension": "cso", "outputOption": "/Fo", "entryPointOption": "/E{name}", "multiplePixelOutputFormats": false }

So Sfx should be compiler-independent. It outputs two things: a text file with the .sfxo extension, and a number of platform-specific shader binaries. The sfxo looks like this:

SFX texture fontTexture 2d read\_only 0 single SamplerState clampSamplerState 9,LINEAR,CLAMP,CLAMP,CLAMP, SamplerState cmcNearestSamplerState 13,POINT,CLAMP,MIRROR,CLAMP, RasterizerState RenderNoCull (false,CULL\_NONE,0,0,false,FILL\_SOLID,true,false,false,0) RasterizerState wireframeRasterizer (true,CULL\_NONE,0,0,false,FILL\_WIREFRAME,false,false,false,0) BlendState AlphaBlendRGB false,(true),1,1,4,5,0,0,(7) DepthStencilState DisableDepth false,0,4 group { technique backg { pass p0 { rasterizer: RenderNoCull depthstencil: DisableDepth 0 blend: AlphaBlendRGB (0,0,0,0) 4294967295 vertex: font\_FontVertexShader\_vv.cso,(),(),() pixel: font\_FontPixelShader.cso,(),(),() } } }

This stands to improve over time. In this case, we've taken an sfx file containing this definition:

VertexShader vs = CompileShader(vs\_4\_0, FontVertexShader());

technique text { pass p0 { SetRasterizerState( RenderNoCull ); SetDepthStencilState( DisableDepth, 0 ); SetBlendState(AddBlendRGB,vec4( 0.0, 0.0, 0.0, 0.0), 0xFFFFFFFF ); SetGeometryShader(NULL); SetVertexShader(vs); SetPixelShader(CompileShader(ps\_4\_0,FontPixelShader())); } }

so we've compiled FontPixelShader() and FontVertexShader() into the cso files: this example is for HLSL. As we extend Sfx to other languages, the json definition in particular will become more complex - possibly using regexes to specify how HLSL is translated into other C-style shader languages.

[Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)