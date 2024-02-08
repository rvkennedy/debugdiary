---
title: "Resolving conflicts between Qt versions"
seoTitle: "Resolving conflicts between Qt versions"
datePublished: Thu Nov 14 2019 14:16:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdaeozp000209l4d81x95p7
slug: resolving-conflicts-between-qt-versions-1
canonical: https://roderickkennedy.com/dbgdiary/resolving-conflicts-between-qt-versions

---

The trueSKY plugin for Unreal uses Qt dll's for UI. Unfortunately so do some other plugins. Because Windows just uses whichever version of a dll was loaded first, this leads to (for example) crashes in trueSKY UI because it tries to access the wrong parts of a dll loaded by Quixel Megascans.

To solve this we recompile Qt using the switch -qtlibinfix to modify the output filenames. Thus instead of Qt5Core.dll we get Qt5Core\_simul.dll etc.

No more conflicts!

UPDATE: To compile Qt, follow the instructions at [https://wiki.qt.io/Building\_Qt\_5\_from\_Git](https://wiki.qt.io/Building_Qt_5_from_Git). For example, for me on Windows, I must git-clone the repo, install perl (!) and call perl init-repository.

Then, I create a subdirectory BUILD\_DIR at subdirectory "build/x64". From there, I call:

call ../../configure.bat -qtlibinfix %QT\_INFIX% -prefix %BUILD\_DIR%\\qtbase -skip qtwebengine -developer-build -%reldeb% -force-debug-info -no-warnings-are-errors -L kernel32 -opengl desktop -opensource -make libs -make tools -nomake examples -nomake tests -platform win32-msvc -confirm-license -no-compile-examples -qt-zlib -plugin-manifests -no-angle -qt-freetype -qt-libjpeg -qt-libpng -D U\_STATIC\_IMPLEMENTATION %INC% %LIBDIRS% %IC%

QT\_INFIX is \_simul, while INC LIBDIRS and IC are extra compile options.

Finally, we run nMake to build Qt.