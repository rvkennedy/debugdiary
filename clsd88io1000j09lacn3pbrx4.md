---
title: "Regex to convert JavaScript to C#"
datePublished: Thu Nov 13 2014 12:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clsd88io1000j09lacn3pbrx4
slug: regex-to-convert-javascript-to-c

---

Variable declarations:  
Search: var\\s\*(\\S\*)\\s\*:\\s\*(\\S\*)  
Replace: $2 $1