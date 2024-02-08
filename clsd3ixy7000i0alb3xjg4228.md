---
title: "Don't step into STL function in Visual Studio 2022"
seoTitle: "Don't step into STL function in Visual Studio 2022"
seoDescription: "Debugging: don't step into STL function in Visual Studio 2022: nostepinto"
datePublished: Thu Feb 08 2024 10:48:54 GMT+0000 (Coordinated Universal Time)
cuid: clsd3ixy7000i0alb3xjg4228
slug: dont-step-into-stl-function-in-visual-studio-2022
tags: cpp, debugging, visual-studio, nostepinto

---

Find your Visual Studio install directory. It is probably something like "C:\\Program Files\\Microsoft Visual Studio\\2022\\Community"

Navigate to the subfolder "Common7\\Packages\\Debugger\\Visualizers".

As administrator, edit the file default.natstepfilter.

Insert this in betwen the <mark>&lt;StepFilter&gt;&lt;/StepFilter&gt;</mark> xml tags:

```xml
<Function><Name>std\:\:.* </Name><Action>NoStepInto</Action></Function>
```