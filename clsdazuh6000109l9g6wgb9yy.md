---
title: "The HTTP of VR"
datePublished: Sat Nov 27 2021 17:41:52 GMT+0000 (Coordinated Universal Time)
cuid: clsdazuh6000109l9g6wgb9yy
slug: the-http-of-vr
canonical: https://roderickkennedy.com/virtual-reality/http-for-vr
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707401877924/ff28c146-b249-40de-ab7b-f0a852a9d29e.png

---

The HTTP of VR
==============

27 Nov

Written By [Roderick Kennedy](https://roderickkennedy.com/virtual-reality?author=5f08d2770b281846bf04ee3b)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707401874831/54a0355d-b576-4b9f-908c-4981ed310aac.png)

There’s been a lot of interest lately in the [Metaverse](https://www.matthewball.vc/all/themetaverse) and its potential. To get to that potential, we’re missing a vital piece of the puzzle, without which I think the whole project will never really get off the ground.

The Metaverse needs a network protocol: it needs its own “HTTP”.

#### In the Beginning

In 1989-90, Tim Berners Lee created the Web. He did this by specifying the two technologies that we still use today: the document format HTML, and the Hypertext Transfer Protocol (HTTP) - the language that web browsers and servers use to communicate. It’s crucial at this distance in time to remember what these technologies were designed for. As a document format, HTML is intended to represent text and associated content: images, tables and so on, but principally, _human-readable_ text. And it’s a linear, one-dimensional format - for the non-linear hopping around that characterizes the Web, we need the other side of the coin, HTTP. And HTTP was designed as a request-response system - the browser or client sends a URI, along with a command (usually “GET” as in, get me this webpage, but also “PUT”, “POST” and so on). It’s a document-retrieval language, intended originally to help scientific institutions freely share data.

*   It’s discontinuous: each request returns a specific, finite response.
    
*   It’s stateless: the server is never required to retain information about a client between requests.
    
*   It’s unidirectional: links are one-way, and you don’t need permission from the target server to link to it.
    

So much of the history of the Web over the last thirty years has been of how tech companies have tried to get around all of these properties to turn a system intended for the free sharing of information into one suited for commerce: how to feed things like ads to clients that didn’t request them; how to record “state” - i.e. track the user and observe their behaviour; how to restrict access so that you can charge for your content. But it’s a testament to the power of the _original_ design that the Web became so important that companies felt they had to work around its restrictions. No-one has seriously proposed returning to the walled-garden online services of 90’s AOL, CompuServe and so on; instead, they’ve tried to recreate miniature walled-gardens within the wider web.

**But in VR it’s a different story: every app is a silo, barely connected with the wider Metaverse.**

#### VR on the Web

One of the ways people have tried to extend the Web is to enable content that isn’t a 2D view of a 1D document. Inspired in part by science-fiction visions of a network you could “experience” from the inside, they tried to make it capable of representing 3D spaces.

At the very first World Wide Web Conference, as far back as 1994 - VRML was proposed: a text file format for 3D graphics, sure, but its name, and the paper by David Raggett of HP that proposed it ([Extending WWW to support Platform Independent Virtual Reality](https://www.w3.org/People/Raggett/vrml/vrml.html)) revealed its ambition: the Web was to incorporate VR environments, “allowing users to "walk" around and push through doors to follow hyperlinks to other parts of the Web.” The protocol that would deliver these environments? HTTP.

Alongside such document-format technologies, the capability of the Web to support VR rendering has developed. WebXR (formerly WebVR) is a set of Javascript libraries that communicate with a web browser to interact with any VR hardware that the device might have attached. Combined with the 3D rendering capabilities of WebGL (again, Javascript), WebXR gets close to Raggett’s original vision - you can indeed explore parts of the web in VR, and thanks to the capabilities of Javascript, you can even navigate and follow links. Libraries like three.js further extend the power of this approach.

But to get to the level of functionality WebXR provides, you’ve pretty much left the Web behind in terms of its design and intention. A WebXR app is more a Javascript app you’ve downloaded via a web page. By relying so much on the Web to deliver what we need, we’ve lost touch with many of its benefits. In a WebXR app, there are no standards for what a 3D URI looks like, for how the app responds to different hardware capabilities, for how we interact or the relationships between the user and the data model. The DOM, a hugely powerful element of the Web’s design, has little bearing on WebGL, for which only one element, the canvas, is relevant to the 3D environment. Behind the scenes, in Javascript, we are in the realms of code alone: by trying to use the Web for too much, we lose many of its advantages.

The modern inheritor of VRML is perhaps [A-Frame](https://aframe.io/), which does away with even the need for a separate file format, and extends HTML itself to support 3D objects. A-Frame is amazing, allowing the hosting of entire 3D scenes in a web page or part of one. Still: the scene is a _document_. A linear, finite chunk of data returned by a GET request.

There is tremendous power and potential in these technologies. And they will play a vital role in joining the Web with the spatial internet. They cannot deliver the entire vision of the Metaverse. By far the greatest reason to look beyond HTML and HTTP for spatial computing is simply this: these technologies will continue to develop, and will always be driven by their primary purpose: to deliver webpages, websites and static, or marginally dynamic content.

**Spatial computing will remain a secondary consideration.**

If the Metaverse is to achieve its full potential, it needs its own protocol.

#### The HTTP of VR

A virtual space is _not_ a document, and its fundamental characteristics are quite different. Much as the Web has become dynamic over the years, an html page is a stationary format subject to step changes when acted on via the DOM - but while a virtual space can be stationary, it could equally be continuously evolving, more akin to an online game than something that can be retrieved and observed in steady-state. While there is no limit on the size of an html document, it is the nature of text that only a finite contiguous segment is viewed at any time in a browser. So it can be viewed without difficulty on minimal hardware. In 3D, it is possible for vastly distant elements of a scene to be visible from any point. This introduces great performance challenges, particularly when we want the viewing device to be light enough to wear.

**So let’s propose an outline of an alternative technology that would fit these requirements.**

Imagine an application-layer protocol for VR with the following characteristics:

*   A real-time, dynamic, stateful two-way client-server protocol. As such, it will be if not fully RTP then close to it.
    
*   It will carry 3D geometry, 2D textures, materials, audio, video and volumetric data mainly from server to client.
    
*   It will carry control inputs, including spatial, binary and analogue inputs; as well as video, audio and other data from client to server.
    
*   Almost all of the application logic will be processed at the server.
    
*   Parts of the app logic that are latency-sensitive will occur at the client.
    
*   Less latency-sensitive parts of the rendering may occur at the server.
    
*   The final rendering and compositing will occur at the client.
    

We split the workload: let the client handle the near stuff, and the server everything else. The whole system of client and server will form essentially two “loops”:

*   The far loop between client input and server logic, which has network latency.
    
*   The near loop within the client, which has local latency.
    

I think that this system could handle almost every current application of XR nicely, and enable a wide range of apps that would struggle in a standalone, installable format. I speak of “the HTTP of VR” rather than the “HTML” because as a real-time system, most of the data will be in binary form; we’re less interested in a human-readable data format, and more in the language of transmission.

#### An Open Protocol

Much of the above could be considered a fair description of a few VR apps already: particularly the more generic “social network” applications that download a large amount of content on the fly to represent avatars, scenery and so on. But these apps are over-specified for what I have in mind, the definition of a “thick client”. And crucially, each client is proprietary - the apps don’t talk to each other.

**The key is that the protocol must be open.**

Imagine a world where every XR device has a lightweight client application, a “VR browser”. Where VR experiences of all types - games, social networks, education, simulation - are all accessible by simply launching the browser and connecting to the appropriate server. Where it doesn’t matter what headset you have, because all the best apps are online via the protocol: no need to download and install, just connect and go. Where as a developer you don’t need to worry about fitting your entire application in a few gigabytes, because it lives on your server/s, not taking up space on a heavy, head-mounted hard-drive. And neither do you have to deploy updates to hundreds or thousands of users - you mostly just update the server-side.

It seems to me that in this way, the Metaverse could really get going: with network effects, discoverability, openness. Low barriers to entry and a free marketplace of content. These are the factors that allowed the Web to grow and thrive.

**By moving beyond the Web with its own open, native protocol, the Metaverse can do the same.**

So at Simul, for the past few years we’ve been building this protocol: it’s called [Teleport VR](https://teleportvr.io/). Let’s see what we can make with it!

 [Roderick Kennedy](https://roderickkennedy.com/virtual-reality?author=5f08d2770b281846bf04ee3b)

The HTTP of VR
==============

27 Nov

Written By [Roderick Kennedy](https://roderickkennedy.com/virtual-reality?author=5f08d2770b281846bf04ee3b)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1707401876317/83695d53-2870-4998-97db-d47e7ca1977d.png)

There’s been a lot of interest lately in the [Metaverse](https://www.matthewball.vc/all/themetaverse) and its potential. To get to that potential, we’re missing a vital piece of the puzzle, without which I think the whole project will never really get off the ground.

The Metaverse needs a network protocol: it needs its own “HTTP”.

#### In the Beginning

In 1989-90, Tim Berners Lee created the Web. He did this by specifying the two technologies that we still use today: the document format HTML, and the Hypertext Transfer Protocol (HTTP) - the language that web browsers and servers use to communicate. It’s crucial at this distance in time to remember what these technologies were designed for. As a document format, HTML is intended to represent text and associated content: images, tables and so on, but principally, _human-readable_ text. And it’s a linear, one-dimensional format - for the non-linear hopping around that characterizes the Web, we need the other side of the coin, HTTP. And HTTP was designed as a request-response system - the browser or client sends a URI, along with a command (usually “GET” as in, get me this webpage, but also “PUT”, “POST” and so on). It’s a document-retrieval language, intended originally to help scientific institutions freely share data.

*   It’s discontinuous: each request returns a specific, finite response.
    
*   It’s stateless: the server is never required to retain information about a client between requests.
    
*   It’s unidirectional: links are one-way, and you don’t need permission from the target server to link to it.
    

So much of the history of the Web over the last thirty years has been of how tech companies have tried to get around all of these properties to turn a system intended for the free sharing of information into one suited for commerce: how to feed things like ads to clients that didn’t request them; how to record “state” - i.e. track the user and observe their behaviour; how to restrict access so that you can charge for your content. But it’s a testament to the power of the _original_ design that the Web became so important that companies felt they had to work around its restrictions. No-one has seriously proposed returning to the walled-garden online services of 90’s AOL, CompuServe and so on; instead, they’ve tried to recreate miniature walled-gardens within the wider web.

**But in VR it’s a different story: every app is a silo, barely connected with the wider Metaverse.**

#### VR on the Web

One of the ways people have tried to extend the Web is to enable content that isn’t a 2D view of a 1D document. Inspired in part by science-fiction visions of a network you could “experience” from the inside, they tried to make it capable of representing 3D spaces.

At the very first World Wide Web Conference, as far back as 1994 - VRML was proposed: a text file format for 3D graphics, sure, but its name, and the paper by David Raggett of HP that proposed it ([Extending WWW to support Platform Independent Virtual Reality](https://www.w3.org/People/Raggett/vrml/vrml.html)) revealed its ambition: the Web was to incorporate VR environments, “allowing users to "walk" around and push through doors to follow hyperlinks to other parts of the Web.” The protocol that would deliver these environments? HTTP.

Alongside such document-format technologies, the capability of the Web to support VR rendering has developed. WebXR (formerly WebVR) is a set of Javascript libraries that communicate with a web browser to interact with any VR hardware that the device might have attached. Combined with the 3D rendering capabilities of WebGL (again, Javascript), WebXR gets close to Raggett’s original vision - you can indeed explore parts of the web in VR, and thanks to the capabilities of Javascript, you can even navigate and follow links. Libraries like three.js further extend the power of this approach.

But to get to the level of functionality WebXR provides, you’ve pretty much left the Web behind in terms of its design and intention. A WebXR app is more a Javascript app you’ve downloaded via a web page. By relying so much on the Web to deliver what we need, we’ve lost touch with many of its benefits. In a WebXR app, there are no standards for what a 3D URI looks like, for how the app responds to different hardware capabilities, for how we interact or the relationships between the user and the data model. The DOM, a hugely powerful element of the Web’s design, has little bearing on WebGL, for which only one element, the canvas, is relevant to the 3D environment. Behind the scenes, in Javascript, we are in the realms of code alone: by trying to use the Web for too much, we lose many of its advantages.

The modern inheritor of VRML is perhaps [A-Frame](https://aframe.io/), which does away with even the need for a separate file format, and extends HTML itself to support 3D objects. A-Frame is amazing, allowing the hosting of entire 3D scenes in a web page or part of one. Still: the scene is a _document_. A linear, finite chunk of data returned by a GET request.

There is tremendous power and potential in these technologies. And they will play a vital role in joining the Web with the spatial internet. They cannot deliver the entire vision of the Metaverse. By far the greatest reason to look beyond HTML and HTTP for spatial computing is simply this: these technologies will continue to develop, and will always be driven by their primary purpose: to deliver webpages, websites and static, or marginally dynamic content.

**Spatial computing will remain a secondary consideration.**

If the Metaverse is to achieve its full potential, it needs its own protocol.

#### The HTTP of VR

A virtual space is _not_ a document, and its fundamental characteristics are quite different. Much as the Web has become dynamic over the years, an html page is a stationary format subject to step changes when acted on via the DOM - but while a virtual space can be stationary, it could equally be continuously evolving, more akin to an online game than something that can be retrieved and observed in steady-state. While there is no limit on the size of an html document, it is the nature of text that only a finite contiguous segment is viewed at any time in a browser. So it can be viewed without difficulty on minimal hardware. In 3D, it is possible for vastly distant elements of a scene to be visible from any point. This introduces great performance challenges, particularly when we want the viewing device to be light enough to wear.

**So let’s propose an outline of an alternative technology that would fit these requirements.**

Imagine an application-layer protocol for VR with the following characteristics:

*   A real-time, dynamic, stateful two-way client-server protocol. As such, it will be if not fully RTP then close to it.
    
*   It will carry 3D geometry, 2D textures, materials, audio, video and volumetric data mainly from server to client.
    
*   It will carry control inputs, including spatial, binary and analogue inputs; as well as video, audio and other data from client to server.
    
*   Almost all of the application logic will be processed at the server.
    
*   Parts of the app logic that are latency-sensitive will occur at the client.
    
*   Less latency-sensitive parts of the rendering may occur at the server.
    
*   The final rendering and compositing will occur at the client.
    

We split the workload: let the client handle the near stuff, and the server everything else. The whole system of client and server will form essentially two “loops”:

*   The far loop between client input and server logic, which has network latency.
    
*   The near loop within the client, which has local latency.
    

I think that this system could handle almost every current application of XR nicely, and enable a wide range of apps that would struggle in a standalone, installable format. I speak of “the HTTP of VR” rather than the “HTML” because as a real-time system, most of the data will be in binary form; we’re less interested in a human-readable data format, and more in the language of transmission.

#### An Open Protocol

Much of the above could be considered a fair description of a few VR apps already: particularly the more generic “social network” applications that download a large amount of content on the fly to represent avatars, scenery and so on. But these apps are over-specified for what I have in mind, the definition of a “thick client”. And crucially, each client is proprietary - the apps don’t talk to each other.

**The key is that the protocol must be open.**

Imagine a world where every XR device has a lightweight client application, a “VR browser”. Where VR experiences of all types - games, social networks, education, simulation - are all accessible by simply launching the browser and connecting to the appropriate server. Where it doesn’t matter what headset you have, because all the best apps are online via the protocol: no need to download and install, just connect and go. Where as a developer you don’t need to worry about fitting your entire application in a few gigabytes, because it lives on your server/s, not taking up space on a heavy, head-mounted hard-drive. And neither do you have to deploy updates to hundreds or thousands of users - you mostly just update the server-side.

It seems to me that in this way, the Metaverse could really get going: with network effects, discoverability, openness. Low barriers to entry and a free marketplace of content. These are the factors that allowed the Web to grow and thrive.

**By moving beyond the Web with its own open, native protocol, the Metaverse can do the same.**

So at Simul, for the past few years we’ve been building this protocol: it’s called [Teleport VR](https://teleportvr.io/). Let’s see what we can make with it!

 [Roderick Kennedy](https://roderickkennedy.com/virtual-reality?author=5f08d2770b281846bf04ee3b)