---
title: "Cross-API Rendering: An Aside"
seoTitle: "Cross-API Rendering: An Aside"
datePublished: Tue Oct 14 2014 09:22:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdcuzhk000a0albek0tddn6
slug: cross-api-rendering-an-aside
canonical: https://roderickkennedy.com/dbgdiary/cross-api-rendering-an-aside

---

14 Oct

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Just as an aside, an insight into why I need to wrapper-up some perfectly serviceable rendering API's. Suppose I want to create a structured buffer to be used in a compute shader, for both read-only and read-write usage.  
Here's how I want to declare it:

simul::crossplatform::StructuredBuffer tempBuffer;

Here's how I'd like to initialize it:

tempBuffer.RestoreDeviceObjects(renderPlatform,num\_elements,true);

\- where passing "true" in the third element means "make it writeable by compute shaders".

Now in DirectX 11, I'd declare it like this:

ID3D11Buffer \*pBuffer\_Tmp; ID3D11UnorderedAccessView \*pUAV\_Tmp; ID3D11ShaderResourceView \*pSRV\_Tmp;

And initialize it like so (get ready):

D3D11\_BUFFER\_DESC buf\_desc; buf\_desc.ByteWidth = sizeof(float) \* 2 \* num\_elements; buf\_desc.Usage = D3D11\_USAGE\_DEFAULT; buf\_desc.BindFlags = D3D11\_BIND\_UNORDERED\_ACCESS | D3D11\_BIND\_SHADER\_RESOURCE; buf\_desc.CPUAccessFlags = 0; buf\_desc.MiscFlags = D3D11\_RESOURCE\_MISC\_BUFFER\_STRUCTURED; buf\_desc.StructureByteStride = sizeof(float) \* 2;

renderPlatform-&gt;AsD3D11Device()-&gt;CreateBuffer(&buf\_desc, NULL, &pBuffer\_Tmp); assert(pBuffer\_Tmp);

// Temp undordered access view D3D11\_UNORDERED\_ACCESS\_VIEW\_DESC uav\_desc; uav\_desc.Format = DXGI\_FORMAT\_UNKNOWN; uav\_desc.ViewDimension = D3D11\_UAV\_DIMENSION\_BUFFER; uav\_desc.Buffer.FirstElement = 0; uav\_desc.Buffer.NumElements =num\_elements; uav\_desc.Buffer.Flags = 0;

renderPlatform-&gt;AsD3D11Device()-&gt;CreateUnorderedAccessView(pBuffer\_Tmp, &uav\_desc, &pUAV\_Tmp);

// Temp shader resource view D3D11\_SHADER\_RESOURCE\_VIEW\_DESC srv\_desc; srv\_desc.Format = DXGI\_FORMAT\_UNKNOWN; srv\_desc.ViewDimension = D3D11\_SRV\_DIMENSION\_BUFFER; srv\_desc.Buffer.FirstElement = 0; srv\_desc.Buffer.NumElements =num\_elements;

renderPlatform-&gt;AsD3D11Device()-&gt;CreateShaderResourceView(pBuffer\_Tmp, &srv\_desc, &pSRV\_Tmp);

So, that was a bit longer; with a *lot* of redundant "match-up" values - you can't create an unordered access view of pBuffer\_Tmp unless it was created with BindFlags containing D3D11\_BIND\_UNORDERED\_ACCESS, for example. Now I know you can do clever things like create a view into a specific part of a buffer, and so on, but let's at least make a neat interface for the 99% case.

Update (19/9/2021): see [https://github.com/simul/Platform](https://github.com/simul/Platform) for full source code.