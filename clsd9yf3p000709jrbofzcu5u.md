---
title: "Qt oddness within Unity"
seoTitle: "Qt oddness within Unity"
seoDescription: "Qt oddness within Unity"
datePublished: Tue Aug 09 2016 12:57:00 GMT+0000 (Coordinated Universal Time)
cuid: clsd9yf3p000709jrbofzcu5u
slug: qt-oddness-within-unity-1
canonical: https://roderickkennedy.com/dbgdiary/qt-oddness-within-unity

---

Qt oddness within Unity
=======================

9 Aug

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

I've noticed some very strange behaviour when launching a Qt-based UI from Unity recently. Possibly from updating Qt, but the UI would fail to respond to mouse clicks or other signals, until I dragged or resized the window, at which point, all the inputs I had made would then replay very quickly and get up to date.  
The only solution I've found is to create a QTimer outside of the main window context (the QApplication is its owner), and set it to call QCoreApplication::processEvents every 100ms or so.  
  
`QTimer *timer=new QTimer(pApp);   timer->setSingleShot(false);   timer->start(100);   CONNECT_AUTO(timer,SIGNAL(timeout()),w,SLOT(Idle()));`

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)