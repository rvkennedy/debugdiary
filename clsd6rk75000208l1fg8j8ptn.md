---
title: "HDR output in Unreal Engine"
datePublished: Thu May 18 2017 13:01:00 GMT+0000 (Coordinated Universal Time)
cuid: clsd6rk75000208l1fg8j8ptn
slug: hdr-output-in-unreal-engine
canonical: https://roderickkennedy.com/dbgdiary/hdr-output-in-unreal-engine
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707394774248/b6568fc2-83ae-4972-bef7-4c852aec8f95.png

---

HDR output in Unreal Engine
===========================

18 May

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

To get Unreal Engine to output from consoles in HDR format (i.e. to HDR TV's), there are a few settings. according to [this post](https://udn.unrealengine.com/questions/323020/wide-colour-gamut-high-dynamic-range.html), there are variables that can go in the .ini files. But in my experience, setting EnableHDROutput to 1 in Engine.ini, causes UE to crash on initialization. As of May 2017, the solution seems to be to either enter the hdr settings every time via the console, or use the Blueprint function EnableHDRDisplayOutput:

![EnableHDROutput.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394771892/dceef1b2-dc62-485a-9e28-7eb20722c00e.png)

This seems to also cover the r.HDR.Display.OutputDevice and r.HDR.Display,ColorGamut settings, so one call will do it.

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

HDR output in Unreal Engine
===========================

18 May

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

To get Unreal Engine to output from consoles in HDR format (i.e. to HDR TV's), there are a few settings. according to [this post](https://udn.unrealengine.com/questions/323020/wide-colour-gamut-high-dynamic-range.html), there are variables that can go in the .ini files. But in my experience, setting EnableHDROutput to 1 in Engine.ini, causes UE to crash on initialization. As of May 2017, the solution seems to be to either enter the hdr settings every time via the console, or use the Blueprint function EnableHDRDisplayOutput:

![EnableHDROutput.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1707394772913/baf7a17d-d3da-4df4-9800-ed80a52f801c.png)

This seems to also cover the r.HDR.Display.OutputDevice and r.HDR.Display,ColorGamut settings, so one call will do it.

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)