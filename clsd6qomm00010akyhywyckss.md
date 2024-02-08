---
title: "Lambdas, or functions as function parameters, in Sfx"
datePublished: Tue Sep 26 2023 09:41:58 GMT+0000 (Coordinated Universal Time)
cuid: clsd6qomm00010akyhywyckss
slug: lambdas-or-functions-as-function-parameters-in-sfx
canonical: https://roderickkennedy.com/dbgdiary/lambdas-or-functions-as-function-parameters-in-sfx
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707394733104/a94b1760-9cff-4e6a-a84f-c00af4f24236.png

---

Lambdas, or functions as function parameters, in Sfx
====================================================

26 Sep

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Following the work in [my previous post](https://roderickkennedy.com/dbgdiary/the-sfx-shader-language-and-shader-variants), I happened across [this](https://mastodon.gamedev.place/@meuns/111119173437770101) Mastodon post about how good it would be to use lambdas, or function objects, as inputs in shaders.

[

![Image of Sylvain Meunier's Mastodon post](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394730626/3b7b57b2-022a-451d-8ce5-cbe0e73178cf.png)



](https://mastodon.gamedev.place/@meuns/111119173437770101)

It occurred to me that in our [Sfx](https://github.com/simul/Platform/tree/main/Applications/Sfx) framework this might not be difficult to implement.

So I’ve implemented it. Because Sfx is essentially just a big text-processing system with semantic knowledge of shader syntax, it wasn’t that difficult. You declare a function input as follows.

    shader vec4 PS_Variants(posTexVertexOutput IN,variant function<vec4(posTexVertexOutput)> RenderFunction) : SV_TARGET
    {
        return RenderFunction(IN);
    }

For now, we actually discard the function object’s template parameters, but later on this will be used for error-checking. Then, in the pass declaration we would do, e.g.

    SetPixelShaderVariants(ps_variants({PSNeon, PSWhite}));

And this would generate two variants, calling the function PSNeon(posTexVertexOutput IN) or PSWhite(posTexVertexOutput IN).

In the variant-generation stage, where we assemble the source for compilation to your specified language, variant parameters that are functions are replaced in the function content with the specific function for that variant, e.g. for GLSL this generates:

    layout(location =0) in Block
    {
    posTexVertexOutput BlockData0;
    } ioblock;
    layout(location = 0) out vec4 returnObject_vec4;
    
    void PS_Variants()
    {
    posTexVertexOutput BlockData0=ioblock.BlockData0;
    #line 1366 "C:/Teleport/client/Shaders/test.sfx"
    {returnObject_vec4=PSWhite(BlockData0);return;}
    }

At the moment, this only works for shader functions, we can’t pass lambdas down to the functions it calls. But I think it can easily be extended - the trick will be that when calling a function that itself has variants, we’ll need an intermediate syntax which eliminates the lambdas from the parameter list, but indicates to the shader constructor that a specialized instance of the called-function must be created.

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Lambdas, or functions as function parameters, in Sfx
====================================================

26 Sep

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Following the work in [my previous post](https://roderickkennedy.com/dbgdiary/the-sfx-shader-language-and-shader-variants), I happened across [this](https://mastodon.gamedev.place/@meuns/111119173437770101) Mastodon post about how good it would be to use lambdas, or function objects, as inputs in shaders.

[

![Image of Sylvain Meunier's Mastodon post](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394731665/41e8fd30-6f22-43db-ae85-4d85d3c95a16.png)



](https://mastodon.gamedev.place/@meuns/111119173437770101)

It occurred to me that in our [Sfx](https://github.com/simul/Platform/tree/main/Applications/Sfx) framework this might not be difficult to implement.

So I’ve implemented it. Because Sfx is essentially just a big text-processing system with semantic knowledge of shader syntax, it wasn’t that difficult. You declare a function input as follows.

    shader vec4 PS_Variants(posTexVertexOutput IN,variant function<vec4(posTexVertexOutput)> RenderFunction) : SV_TARGET
    {
        return RenderFunction(IN);
    }

For now, we actually discard the function object’s template parameters, but later on this will be used for error-checking. Then, in the pass declaration we would do, e.g.

    SetPixelShaderVariants(ps_variants({PSNeon, PSWhite}));

And this would generate two variants, calling the function PSNeon(posTexVertexOutput IN) or PSWhite(posTexVertexOutput IN).

In the variant-generation stage, where we assemble the source for compilation to your specified language, variant parameters that are functions are replaced in the function content with the specific function for that variant, e.g. for GLSL this generates:

    layout(location =0) in Block
    {
    posTexVertexOutput BlockData0;
    } ioblock;
    layout(location = 0) out vec4 returnObject_vec4;
    
    void PS_Variants()
    {
    posTexVertexOutput BlockData0=ioblock.BlockData0;
    #line 1366 "C:/Teleport/client/Shaders/test.sfx"
    {returnObject_vec4=PSWhite(BlockData0);return;}
    }

At the moment, this only works for shader functions, we can’t pass lambdas down to the functions it calls. But I think it can easily be extended - the trick will be that when calling a function that itself has variants, we’ll need an intermediate syntax which eliminates the lambdas from the parameter list, but indicates to the shader constructor that a specialized instance of the called-function must be created.

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)