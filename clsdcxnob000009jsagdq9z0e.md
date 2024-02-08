---
title: "ostringstream is the perfect C++ replacement for sprintf, except..."
seoTitle: "ostringstream is the perfect C++ replacement for sprintf, except..."
datePublished: Sun Jan 18 2015 10:32:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdcxnob000009jsagdq9z0e
slug: ostringstream-is-the-perfect-c-replacement-for-sprintf-except
canonical: https://roderickkennedy.com/dbgdiary/ostringstream-is-the-perfect-c-replacement-for-sprintf-except

---

[ostringstream is the perfect C++ replacement for sprintf](http://www.codeproject.com/Articles/1992/OStringStream-or-how-to-stop-worrying-and-never-us), except for trying to debug its contents in Visual Studio.Add this to Microsoft Visual Studio 12.0\\Common7\\Packages\\Debugger\\Visualizers\\stl.natvis:

&lt;Type Name="std::basic\_ostringstream&lt;char,\*&gt;"&gt; &lt;DisplayString&gt;{\_Mystrbuf-&gt;\_Gfirst},s&lt;/DisplayString&gt; &lt;StringView&gt;\_Mystrbuf-&gt;\_Gfirst,s&lt;/StringView&gt; &lt;Expand&gt; &lt;Item Name="\[state\]"&gt;\_Mystate&lt;/Item&gt; &lt;ExpandedItem&gt;\_Mystrbuf-&gt;\_Gfirst&lt;/ExpandedItem&gt; &lt;/Expand&gt; &lt;/Type&gt;