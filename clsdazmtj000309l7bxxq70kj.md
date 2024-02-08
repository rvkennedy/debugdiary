---
title: "Introducing WebRTC to Teleport VR"
datePublished: Fri Mar 17 2023 23:06:33 GMT+0000 (Coordinated Universal Time)
cuid: clsdazmtj000309l7bxxq70kj
slug: introducing-webrtc-to-teleport-vr
canonical: https://roderickkennedy.com/virtual-reality/introducing-webrtc

---

Introducing WebRTC to Teleport VR
=================================

17 Mar

Written By [Roderick Kennedy](https://roderickkennedy.com/virtual-reality?author=5f08d2770b281846bf04ee3b)

#### Getting Teleport running on Linux

It's long been a goal to get a Teleport server running on Linux. The reason to do this is that I want to offer effectively "VR hosting in-a-box", and the way to do that is containerization: we make it possible to create a Teleport server instance in a Docker container, then it can be uploaded to a cloud service like AWS with a minimum of fuss.

If we try to do this with Windows, we come up against licensing problems: if you're running a Windows server, you need a Windows licence. Best to use Linux.

But apart from the Android/Quest client, all Teleport work so far has been on Windows.  
  
So for the past few months I've tried to get this resolved: removing obvious Win32 API dependencies, getting the necessary libraries built. An early issue was we were passing strings to and from Unity as \_bstr\_t, a Microsoft class that isn't supported in clang/Unix. So I switched over to plain old char\* for Unity to communicate with the Teleport dll.

Debugging was also a challenge. I've not yet found a way to debug C# code in Unity on Linux, but C++ works, after some deep dives into the gdb settings (I haven’t yet got lldb working, but gdb works fine for clang code).

So finally we can now connect from a Teleport client to the Linux server in Unity. But the data streams are not working. I could try to fix our UDP-based data stream code for Linux, which uses the 3rd party libraries SRT and EFP.

#### Streaming Transport

We need streaming transport because in Teleport we stream video, audio and geometry. In the hybrid rendering mode (shown above), far objects are streamed as video in the background. Near objects are streamed as geometry. In this video I've added a grey highlight to the video stream, otherwise it might be difficult to see the join.  
  
As we move through the scene, any geometry that we approach is streamed into the local device. In this way, Teleport enables:

*   Higher visual quality than an installed application or WebXR
    
*   Larger scenes than an installed application or WebXR
    
*   Multi-platform support without multi-platform code
    
*   Instant deployment to any device.  
    
    So under the Teleport protocol we need two network transport layers: one for messaging, one for data streaming. Plus HTTPS for static file downloads.
    

#### Introducing WebRTC

To build Teleport, needed a data streaming system. We used UDP packets, a fast way of sending data - much faster than the TCP/IP that browsers use to download web content. UDP is "unreliable": the packets aren't guaranteed to get there the way TCP/IP packets are. So we needed a couple of extra layers to ensure reliable streaming: we used SRT for ordering and EFP to manage "frames" or data chunks. Both good systems. And when we started building Teleport there wasn't a good alternative.  
  
But WebRTC is mature now, and does exactly what UDP+SRT/EFP does. So once it's integrated, that standardizes the Teleport stack around something that's in widespread use.

So if I can, I'm going to switch the transport layer over to WebRTC, which is well-supported, supports arbitrary data streams, and is at least nominally available in C++.

WebRTC was originally intended as a peer-to-peer multimedia protocol, a way to enable (for example) video calls on web pages. But it’s pretty flexible: it supports two-way video tracks, audio tracks, and crucially for us: data channels - arbitrary text or binary data, flowing in either direction, either reliably or not. WebRTC offers us a number of advantages.

*   Web-compatible: when we implement the web version of the Teleport client, we won’t be able to use UDP because browsers don’t support raw packet streaming. But they do support WebRTC.
    
*   Firewall-friendly: the implementations of WebRTC include support for STUN/TURN, which helps navigate restrictive firewalls, simplifying the process of finding the right port and underlying transport.
    
*   Well-supported: compared to the UDP-based systems we’ve been using, WebRTC is used a lot more, so it’s easier to find expertise and solutions.
    

So here we have it: streaming the geometry via WebRTC. I've added a green shader at the front to highlight each object as it streams in.

There's no background because I'd only implemented geometry streaming so far: video/audio streaming is handled differently in WebRTC. But it worked well: very reliable, and more robust to network changes+firewalls than the UDP approach.

And it should work fine in Linux, though that remains to be tested.

As it turned out, it was pretty simple to activate WebRTC video and audio support by just using data channels - rather than WebRTC's native video/audio tracks. This is most likely less efficient, and will need to be reviewed.

  
Here as well I show the attractive, minimal 2D user-interface menus I've implemented for desktop use: the 3D menus will now only appear if you're in VR. So the app in desktop now looks... a lot like a browser! Which is intentional.

The back half of the video shows more clearly the transition between the video-streamed background and the geometry-streamed foreground - again with a grey cast to highlight the join - this will enable VR apps of truly vast scope to be accessed instantly via Teleport. While still permitting the option of geometry-only streams, which will be comparable in price to running a web server - as against the $dollar+ per user per hour that Unreal or Unity pixel-streaming costs!

Because there's no chance of a true Metaverse developing at those prices. It needs to be cheap enough that anyone can build a site, host content, create services - just as they can on the web. And that's what Teleport delivers.

 [Roderick Kennedy](https://roderickkennedy.com/virtual-reality?author=5f08d2770b281846bf04ee3b)

Introducing WebRTC to Teleport VR
=================================

17 Mar

Written By [Roderick Kennedy](https://roderickkennedy.com/virtual-reality?author=5f08d2770b281846bf04ee3b)

#### Getting Teleport running on Linux

It's long been a goal to get a Teleport server running on Linux. The reason to do this is that I want to offer effectively "VR hosting in-a-box", and the way to do that is containerization: we make it possible to create a Teleport server instance in a Docker container, then it can be uploaded to a cloud service like AWS with a minimum of fuss.

If we try to do this with Windows, we come up against licensing problems: if you're running a Windows server, you need a Windows licence. Best to use Linux.

But apart from the Android/Quest client, all Teleport work so far has been on Windows.  
  
So for the past few months I've tried to get this resolved: removing obvious Win32 API dependencies, getting the necessary libraries built. An early issue was we were passing strings to and from Unity as \_bstr\_t, a Microsoft class that isn't supported in clang/Unix. So I switched over to plain old char\* for Unity to communicate with the Teleport dll.

Debugging was also a challenge. I've not yet found a way to debug C# code in Unity on Linux, but C++ works, after some deep dives into the gdb settings (I haven’t yet got lldb working, but gdb works fine for clang code).

So finally we can now connect from a Teleport client to the Linux server in Unity. But the data streams are not working. I could try to fix our UDP-based data stream code for Linux, which uses the 3rd party libraries SRT and EFP.

#### Streaming Transport

We need streaming transport because in Teleport we stream video, audio and geometry. In the hybrid rendering mode (shown above), far objects are streamed as video in the background. Near objects are streamed as geometry. In this video I've added a grey highlight to the video stream, otherwise it might be difficult to see the join.  
  
As we move through the scene, any geometry that we approach is streamed into the local device. In this way, Teleport enables:

*   Higher visual quality than an installed application or WebXR
    
*   Larger scenes than an installed application or WebXR
    
*   Multi-platform support without multi-platform code
    
*   Instant deployment to any device.  
    
    So under the Teleport protocol we need two network transport layers: one for messaging, one for data streaming. Plus HTTPS for static file downloads.
    

#### Introducing WebRTC

To build Teleport, needed a data streaming system. We used UDP packets, a fast way of sending data - much faster than the TCP/IP that browsers use to download web content. UDP is "unreliable": the packets aren't guaranteed to get there the way TCP/IP packets are. So we needed a couple of extra layers to ensure reliable streaming: we used SRT for ordering and EFP to manage "frames" or data chunks. Both good systems. And when we started building Teleport there wasn't a good alternative.  
  
But WebRTC is mature now, and does exactly what UDP+SRT/EFP does. So once it's integrated, that standardizes the Teleport stack around something that's in widespread use.

So if I can, I'm going to switch the transport layer over to WebRTC, which is well-supported, supports arbitrary data streams, and is at least nominally available in C++.

WebRTC was originally intended as a peer-to-peer multimedia protocol, a way to enable (for example) video calls on web pages. But it’s pretty flexible: it supports two-way video tracks, audio tracks, and crucially for us: data channels - arbitrary text or binary data, flowing in either direction, either reliably or not. WebRTC offers us a number of advantages.

*   Web-compatible: when we implement the web version of the Teleport client, we won’t be able to use UDP because browsers don’t support raw packet streaming. But they do support WebRTC.
    
*   Firewall-friendly: the implementations of WebRTC include support for STUN/TURN, which helps navigate restrictive firewalls, simplifying the process of finding the right port and underlying transport.
    
*   Well-supported: compared to the UDP-based systems we’ve been using, WebRTC is used a lot more, so it’s easier to find expertise and solutions.
    

So here we have it: streaming the geometry via WebRTC. I've added a green shader at the front to highlight each object as it streams in.

There's no background because I'd only implemented geometry streaming so far: video/audio streaming is handled differently in WebRTC. But it worked well: very reliable, and more robust to network changes+firewalls than the UDP approach.

And it should work fine in Linux, though that remains to be tested.

As it turned out, it was pretty simple to activate WebRTC video and audio support by just using data channels - rather than WebRTC's native video/audio tracks. This is most likely less efficient, and will need to be reviewed.

  
Here as well I show the attractive, minimal 2D user-interface menus I've implemented for desktop use: the 3D menus will now only appear if you're in VR. So the app in desktop now looks... a lot like a browser! Which is intentional.

The back half of the video shows more clearly the transition between the video-streamed background and the geometry-streamed foreground - again with a grey cast to highlight the join - this will enable VR apps of truly vast scope to be accessed instantly via Teleport. While still permitting the option of geometry-only streams, which will be comparable in price to running a web server - as against the $dollar+ per user per hour that Unreal or Unity pixel-streaming costs!

Because there's no chance of a true Metaverse developing at those prices. It needs to be cheap enough that anyone can build a site, host content, create services - just as they can on the web. And that's what Teleport delivers.

 [Roderick Kennedy](https://roderickkennedy.com/virtual-reality?author=5f08d2770b281846bf04ee3b)