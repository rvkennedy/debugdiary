---
title: "Cubemap Texture Arrays"
seoTitle: "Cubemap Texture Arrays"
datePublished: Sat Jun 18 2016 12:49:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdde5zn000209l5a4cv09t0
slug: cubemap-texture-arrays
canonical: https://roderickkennedy.com/dbgdiary/cubemap-texture-arrays

---

In modern graphics API's: Direct3D 11 and 12, OpenGL 4.0 and so on, we can create 2D textures, textures with multiple mipmaps, arrays of textures with multiple mipmaps, and so on. Most don't yet support arrays of 3D textures.

But one little-used combination that is supported, is a texture cube array. That's a single texture, which is an array of cubemaps, which optionally have mip levels as well.

In Direct3D 11, this involves creating a 2D texture using D3D11\_TEXTURE2D\_DESC struct, where arraySize is six times the number of cubemaps.

Then, when you create the shaderResourceView, you'll use a D3D11\_SHADER\_RESOURCE\_VIEW\_DESC with ViewDimension equal to D3D11\_SRV\_DIMENSION\_TEXTURECUBEARRAY.

You'll fill in the TextureCubeArray member of that struct's union, e.g.

SRVDesc.ViewDimension =D3D11\_SRV\_DIMENSION\_TEXTURECUBEARRAY; SRVDesc.TextureCubeArray.MipLevels =numMips; SRVDesc.TextureCubeArray.MostDetailedMip =0; SRVDesc.TextureCubeArray.First2DArrayFace =0; SRVDesc.TextureCubeArray.NumCubes =numLevels;