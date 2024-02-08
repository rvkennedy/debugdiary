---
title: "Changing the name of a variable in CMake"
seoDescription: "Changing the name of a variable in CMake"
datePublished: Fri Nov 27 2020 14:18:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdajjx9000209ie0n0ucput
slug: changing-the-name-of-a-variable-in-cmake-1
canonical: https://roderickkennedy.com/dbgdiary/changing-the-name-of-a-variable-in-cmake

---

Here's a neat trick in CMake: you want to change the name of a variable, but worry that anyone you've distributed the code to already will lose the option they've selected.

Use the old variable as the default value for the new one:

`option(OLD_VARIABLE "Some variable" ON) option(NEW_VARIABLE "Some variable" ${OLD_VARIABLE})`

or...

`set( OLD_STRING_VARIABLE "Old default" CACHE STRING "Help text" )`

`set( NEW_STRING_VARIABLE "${OLD_STRING_VARIABLE}" "Help text" )`