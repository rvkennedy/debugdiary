---
title: "UnorderedAccessView of a vertex buffer"
datePublished: Tue Nov 26 2013 19:51:00 GMT+0000 (Coordinated Universal Time)
cuid: clsd7tfuz000i0ajrdtlq6t2t
slug: unorderedaccessview-of-a-vertex-buffer
canonical: https://roderickkennedy.com/dbgdiary/unorderedaccessview-of-vertex-buffer

---

Here's how to create a vertex buffer that you can write with a compute shader:

```cpp
D3D11_BUFFER_DESC desc =
    {
        40000*sizeof(vec3)
        ,D3D11_USAGE_DEFAULT
        ,D3D11_BIND_VERTEX_BUFFER|D3D11_BIND_UNORDERED_ACCESS
        ,0  // NoCPU access
 ,0  // not D3D11_RESOURCE_MISC_BUFFER_STRUCTURED - why?
 ,sizeof(vec3)   //StructureByteStride
 };
    m_pd3dDevice->CreateBuffer(&desc,NULL,&m_pVertexBuffer);
    D3D11_UNORDERED_ACCESS_VIEW_DESC uav_desc;
    ZeroMemory(&uav_desc, sizeof(D3D11_UNORDERED_ACCESS_VIEW_DESC));
    uav_desc.Format   =DXGI_FORMAT_R32_FLOAT;    // Must be this format, or INVALID_ARG will occur
    uav_desc.ViewDimension  =D3D11_UAV_DIMENSION_BUFFER;
    uav_desc.Buffer.NumElements  =40000;
    V_CHECK(m_pd3dDevice->CreateUnorderedAccessView(m_pVertexBuffer, &uav_desc, &unorderedAccessView));
```