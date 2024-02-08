---
title: "Subtle problems linking dll's built with different compilers"
seoTitle: "Subtle problems linking dll's built with different compilers"
datePublished: Thu Jul 02 2015 10:15:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdbjgy800040albgfu39dc2
slug: subtle-problems-linking-dlls-built-with-different-compilers
canonical: https://roderickkennedy.com/dbgdiary/subtle-problems-linking-dlls-built-with-different-compilers

---

You should generally try to link DLL's and exe's built with the same compiler. I recently had an issue where I redirected C++ std::cout and cerr in my exe, but got no apparent output from these functions in my linked DLL.

Turns out, the exe, being new, was built with the Visual Studio 2013 (v120) toolset, while my DLL's, for backward compatibility, were using v110\_xp - the Visual Studio 2012/Windows XP-compatible toolset. And because they used the MD dynamically linked runtimes, they were silently, and happily, loading two different versions of the runtime libraries.

Therefore, my redirects (std::cout.rdbuf() etc) were working on the exe's copy of the runtime, and of course, not on the DLL's, entirely different, runtime library.