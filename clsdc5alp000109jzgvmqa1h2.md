---
title: "The Three Hierarchies of C++"
seoTitle: "The Three Hierarchies of C++"
datePublished: Sun Feb 14 2016 12:58:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdc5alp000109jzgvmqa1h2
slug: the-three-hierarchies-of-c
canonical: https://roderickkennedy.com/dbgdiary/the-three-hierarchies-of-c

---

Lest anyone ever tell you programming isn't complicated, it occurred to me that a C++ program has three interlocking, but independent hierarchies.

* Namespace: The name of the function, within its parent namespace and/or the class that owns it.
    
* Call Stack: The parent is the calling function, the child is the callee.
    
* Inheritance: The class/struct is a child of the base class.
    

Could we stand to lose one or two of these?