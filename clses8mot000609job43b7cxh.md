---
title: "Qt sizePolicy"
seoTitle: "Qt sizePolicy"
datePublished: Tue Jan 07 2014 16:11:00 GMT+0000 (Coordinated Universal Time)
cuid: clses8mot000609job43b7cxh
slug: qt-sizepolicy
canonical: https://dbgdiary.blogspot.com/2014/01/in-qt-sizepolicy-for-containers-should.html

---

In Qt, the sizePolicy for containers should be Expanding. That's the only policy that respects the minimum sizes of its contents. The policy "MinimumExpanding" ignores the contents and only looks at the widget's own minimum size value.

[Permalink](https://roderickkennedy.com/dbgdiary/in-qt-sizepolicy-for-containers-should)