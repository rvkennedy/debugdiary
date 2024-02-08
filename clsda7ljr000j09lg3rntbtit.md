---
title: "HDR output in Unreal Engine"
seoTitle: "HDR output in Unreal Engine"
seoDescription: "HDR output in Unreal Engine"
datePublished: Thu May 18 2017 13:01:00 GMT+0000 (Coordinated Universal Time)
cuid: clsda7ljr000j09lg3rntbtit
slug: hdr-output-in-unreal-engine-1
canonical: https://roderickkennedy.com/dbgdiary/hdr-output-in-unreal-engine
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707397871080/07eb3f8b-711a-403a-abb5-8dc965f268e1.png

---

18 May

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

To get Unreal Engine to output from consoles in HDR format (i.e. to HDR TV's), there are a few settings. according to [this post](https://udn.unrealengine.com/questions/323020/wide-colour-gamut-high-dynamic-range.html), there are variables that can go in the .ini files. But in my experience, setting EnableHDROutput to 1 in Engine.ini, causes UE to crash on initialization. As of May 2017, the solution seems to be to either enter the hdr settings every time via the console, or use the Blueprint function EnableHDRDisplayOutput:

![EnableHDROutput.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397869287/2261db6d-3712-40ae-b424-3a60a18ebb32.png align="left")

This seems to also cover the r.HDR.Display.OutputDevice and r.HDR.Display,ColorGamut settings, so one call will do it.

[Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

# HDR output in Unreal Engine

18 May

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

To get Unreal Engine to output from consoles in HDR format (i.e. to HDR TV's), there are a few settings. according to [this post](https://udn.unrealengine.com/questions/323020/wide-colour-gamut-high-dynamic-range.html), there are variables that can go in the .ini files. But in my experience, setting EnableHDROutput to 1 in Engine.ini, causes UE to crash on initialization. As of May 2017, the solution seems to be to either enter the hdr settings every time via the console, or use the Blueprint function EnableHDRDisplayOutput:

![EnableHDROutput.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397870284/996429ca-a490-4f1e-8ca3-bac266bf80f5.png align="left")

This seems to also cover the r.HDR.Display.OutputDevice and r.HDR.Display,ColorGamut settings, so one call will do it.

[Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)