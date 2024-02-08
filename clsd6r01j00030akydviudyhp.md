---
title: "Changing the name of a variable in CMake"
datePublished: Fri Nov 27 2020 14:18:00 GMT+0000 (Coordinated Universal Time)
cuid: clsd6r01j00030akydviudyhp
slug: changing-the-name-of-a-variable-in-cmake
canonical: https://roderickkennedy.com/dbgdiary/changing-the-name-of-a-variable-in-cmake

---

Changing the name of a variable in CMake
========================================

27 Nov

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Here's a neat trick in CMake: you want to change the name of a variable, but worry that anyone you've distributed the code to already will lose the option they've selected.

Use the old variable as the default value for the new one:

`option(OLD_VARIABLE "Some variable" ON)   option(NEW_VARIABLE "Some variable" ${OLD_VARIABLE})`

or...

`set( OLD_STRING_VARIABLE "Old default" CACHE STRING "Help text" )`

`set( NEW_STRING_VARIABLE "${OLD_STRING_VARIABLE}" "Help text" )`

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)