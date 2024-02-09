---
title: "Generating mipmaps for 3D textures"
seoTitle: "Generating mipmaps for 3D textures"
datePublished: Tue Oct 27 2015 12:46:00 GMT+0000 (Coordinated Universal Time)
cuid: clses2fkf000808ju4ivwb5iq
slug: generating-mipmaps-for-3d-textures
canonical: https://roderickkennedy.com/dbgdiary/generating-mipmaps-for-3d-textures

---

Mip-mapping is very useful, and sometimes we want to generate the mips of a texture in a shader. In DirectX 11 hlsl this is tricky because you can't read from the resource that you're writing to.

For example, you might have a 3D texture that you've filled in with a compute shader. You then want to generate its mipmaps from the original texture. But the ShaderResourceView you created can't be read while you're writing to. It will silently fail - unless you have Direct3D debug output enabled, in which case you'll get a warning.

You probably created the SRV like this:

D3D11\_SHADER\_RESOURCE\_VIEW\_DESC srv\_desc; ZeroMemory(&srv\_desc, sizeof(D3D11\_SHADER\_RESOURCE\_VIEW\_DESC)); srv\_desc.Format           = f; srv\_desc.ViewDimension        = D3D11\_SRV\_DIMENSION\_TEXTURE3D; srv\_desc.Texture3D.MipLevels     = m; srv\_desc.Texture3D.MostDetailedMip = 0; d3D11Device-&gt;CreateShaderResourceView(texture, &srv\_desc,&shaderResourceView);

which means that when you pass it to the hlsl shader, it contains all of the mips from 0 to m-1. What you want instead (or rather, as well), is a SRV for each individual mip:

D3D11\_SHADER\_RESOURCE\_VIEW\_DESC mip\_srv\_desc; ZeroMemory(&mip\_srv\_desc, sizeof(D3D11\_SHADER\_RESOURCE\_VIEW\_DESC)); mip\_srv\_desc.Format         = f; mip\_srv\_desc.ViewDimension       = D3D11\_SRV\_DIMENSION\_TEXTURE3D; mip\_srv\_desc.Texture3D.MipLevels   = 1; mip\_srv\_desc.Texture3D.MostDetailedMip = i; d3D11Device-&gt;CreateShaderResourceView(texture, &mip\_srv\_desc, &mipShaderResourceViews\[i\]);

Then use these as input to the compute shader.