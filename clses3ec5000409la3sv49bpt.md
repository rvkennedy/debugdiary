---
title: "Dual-source blending in DirectX 11"
seoTitle: "Dual-source blending in DirectX 11"
datePublished: Mon Jul 21 2014 23:50:00 GMT+0000 (Coordinated Universal Time)
cuid: clses3ec5000409la3sv49bpt
slug: dual-source-blending-in-directx-11
canonical: https://roderickkennedy.com/dbgdiary/dual-source-blending-in-directx-11

---

It's not described in the documentation, but dual-source blending in DirectX 11 D3D11\_BLEND\_SRC1\_COLOR is achieved using SV\_TARGET1. You would have a BlendState like this (all in fx format):

BlendState CompositeBlend { BlendEnable\[0\] =TRUE; SrcBlend =ONE; DestBlend =SRC1\_COLOR; BlendOp =ADD; };

And an output structure that looks like it's writing to two rendertargets. But you only have one rendertarget!

struct TwoColourOutput { float4 add :SV\_TARGET0; float4 multiply :SV\_TARGET1; };

And then the pixel shader does:

TwoColourOutput PSMain(v2f IN) : SV\_TARGET { TwoColourOutput result =... return result; }

The result - the value in the rendertarget is multiplied by result.multiply, and then result.add is added to it. Viola!