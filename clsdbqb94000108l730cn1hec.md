---
title: "Lambdas, or functions as function parameters, in Sfx"
seoTitle: "Lambdas, or functions as function parameters, in Sfx"
datePublished: Tue Sep 26 2023 09:41:58 GMT+0000 (Coordinated Universal Time)
cuid: clsdbqb94000108l730cn1hec
slug: lambdas-or-functions-as-function-parameters-in-sfx-1
canonical: https://roderickkennedy.com/dbgdiary/lambdas-or-functions-as-function-parameters-in-sfx
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707397829906/bbce9c20-7049-4332-a48e-c87441d51fd2.png

---

Following the work in [my previous post](https://roderickkennedy.com/dbgdiary/the-sfx-shader-language-and-shader-variants), I happened across [this](https://mastodon.gamedev.place/@meuns/111119173437770101) Mastodon post about how good it would be to use lambdas, or function objects, as inputs in shaders.

\[

![Image of Sylvain Meunier's Mastodon post](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397827279/e9644b67-13fa-4a9d-bbe8-e41be039e1ec.png align="left")

\](https://mastodon.gamedev.place/@meuns/111119173437770101)

It occurred to me that in our [Sfx](https://github.com/simul/Platform/tree/main/Applications/Sfx) framework this might not be difficult to implement.

So I’ve implemented it. Because Sfx is essentially just a big text-processing system with semantic knowledge of shader syntax, it wasn’t that difficult. You declare a function input as follows.

shader vec4 PS\_Variants(posTexVertexOutput IN,variant function&lt;vec4(posTexVertexOutput)&gt; RenderFunction) : SV\_TARGET { return RenderFunction(IN); }

For now, we actually discard the function object’s template parameters, but later on this will be used for error-checking. Then, in the pass declaration we would do, e.g.

SetPixelShaderVariants(ps\_variants({PSNeon, PSWhite}));

And this would generate two variants, calling the function PSNeon(posTexVertexOutput IN) or PSWhite(posTexVertexOutput IN).

In the variant-generation stage, where we assemble the source for compilation to your specified language, variant parameters that are functions are replaced in the function content with the specific function for that variant, e.g. for GLSL this generates:

layout(location =0) in Block { posTexVertexOutput BlockData0; } ioblock; layout(location = 0) out vec4 returnObject\_vec4;

void PS\_Variants() { posTexVertexOutput BlockData0=ioblock.BlockData0; #line 1366 "C:/Teleport/client/Shaders/test.sfx" {returnObject\_vec4=PSWhite(BlockData0);return;} }

At the moment, this only works for shader functions, we can’t pass lambdas down to the functions it calls. But I think it can easily be extended - the trick will be that when calling a function that itself has variants, we’ll need an intermediate syntax which eliminates the lambdas from the parameter list, but indicates to the shader constructor that a specialized instance of the called-function must be created.