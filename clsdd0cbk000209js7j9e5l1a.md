---
title: "Visual Studio-style mouse controls for Sublime Text"
seoTitle: "Visual Studio-style mouse controls for Sublime Text"
datePublished: Fri Jan 08 2016 12:52:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdd0cbk000209js7j9e5l1a
slug: visual-studio-style-mouse-controls-for-sublime-text
canonical: https://roderickkennedy.com/dbgdiary/visual-studio-style-mouse-controls-for-sublime-text

---

Create C:\\Users\\(YOURNAME)\\AppData\\Roaming\\Sublime Text 3\\Packages\\User\\Default (Windows).sublime-mousemap, and fill it with:

\[  
// Ctrl-click word select  
{  
"button": "button1", "count": 1, "modifiers": \["ctrl"\],  
"press\_command": "drag\_select",  
"press\_args": {"by": "words"}  
}  
// Column select  
,{  
"button": "button1",  
"count": 1,  
"modifiers": \["alt"\],  
"press\_command": "drag\_select",  
"press\_args": {"by": "columns"}  
}  
\]