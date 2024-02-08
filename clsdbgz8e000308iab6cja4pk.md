---
title: "OpenXR Interaction Profiles"
seoTitle: "OpenXR Interaction Profiles"
datePublished: Sat Dec 18 2021 16:58:04 GMT+0000 (Coordinated Universal Time)
cuid: clsdbgz8e000308iab6cja4pk
slug: openxr-interaction-profiles-1
canonical: https://roderickkennedy.com/dbgdiary/openxr-interaction-profiles
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707397838057/62161d9c-8bf2-4af8-95e7-63c4e34a5174.png

---

I’ve tabulated the standard OpenXR interaction profiles from the Khronos OpenXR spec at [https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#semantic-path-interaction-profiles](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#semantic-path-interaction-profiles).

A given Teleport server will have a control set it supports. This must be sent to any connecting client, to say “these are the controls I need for interaction to work”. The client must then match these controls to its hardware.

Unfortunately, there seems to be nothing in OpenXR that allows us to query the XR device for what paths it provides - you just have to suggest bindings and that will either succeed or fail.

And we need to handle the case where an essential control is missing, and the case where the suggested binding for two required inputs is the same.

This is the table:

![OpenXR Interaction Profiles](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397835626/9f9f978b-fcd4-4806-a034-33961e9da919.png align="left")

https://docs.google.com/spreadsheets/d/1w4Me9\_yG\_TNho4Gmelrc9ILYtG1TELoxCXc622xUiVM/edit?usp=sharing

Most hardware should support at least the Khronos Simple Controller, plus its own profile if present. There is also the option of extension paths which include “\_ext” in them. But again, no way to query them…

#### User paths

Only the HTC Vive Pro implements the “head” path, and only for a system button and sound controls. OpenXR *does not* treat the headset as a controller the way it does for the hands; you have to use xrLocateSpace to get the head pose.

So unless we’re using a gamepad, in most cases we’ll have a user path “/user/hand/left” and “/user/hand/right”, to which we’ll append the input path. For example “/user/hand/left/input/grip/pose” to get the position+orientation (the “pose” in XR jargon) of the left hand. And all of the hand controllers provide two poses per hand: the “grip” which is supposed to be the centre of the palm with the Z-axis pointing down from index finger to pinkie; and the “aim”, where the negative Z-axis points in the “aiming” direction of the controller. These two poses are locked together in all current controllers I know of, they move in sync and the offsets from “grip” to “aim” simply represent the slightly different geometries of the various handsets.