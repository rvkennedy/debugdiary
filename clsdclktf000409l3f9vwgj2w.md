---
title: "Using Sublime Text with FASTBuild"
seoTitle: "Using Sublime Text with FASTBuild"
datePublished: Tue Nov 08 2016 13:58:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdclktf000409l3f9vwgj2w
slug: using-sublime-text-with-fastbuild-1
canonical: https://roderickkennedy.com/dbgdiary/using-sublime-text-with-fastbuild

---

Sublime text can be used to run [FASTBuild](http://www.fastbuild.org/), with this build script. Save this as "FASTBuild.sublime-build", in your user AppData\\Roaming\\Sublime Text 3\\Packages\\User directory (or Mac/Linux equivalent location).

{ "cmd": \["C:/Simul/master/Simul/External/FASTBuild/FBuild.exe","-config","$file"\] ,"file\_regex": "(.*)\\((.*),(.*)\\): FASTBuild (Error .*)$" ,"selector": "source.bff" }

By using the selector "source.bff", it should match up automatically with the syntax (below), but I've not quite figured this part out yet. Here's a preliminary Sublime Text syntax for FASTBuild. Call it "FASTBuild.sublime-syntax" and save it in the same directory.

%YAML 1.2 --- name: FASTBuild file\_extensions: \[bff\] scope: source.bff

contexts: comments: - include: scope:source.c#comments

instring: - match: "\[^"\]" scope: string - match: " pop: true scope: string strings: - match: " push: instring scope: string

inquotes: - match: "\[^'\]" scope: string - match: "'" pop: true scope: string quotes: - match: "'" push: inquotes scope: string

variables: - match: "\\.(\\w\*)" scope: keyword preprocessor-includes: - match: "^\\s\*(#\\s\*\\binclude)\\b" captures: 1: keyword.control.include.c++ preprocessor-import: - match: "^\\s\*(#)\\s\*\\b(import)\\b" scope: keyword.control.c

preprocessor: - include: scope:source.c#incomplete-inc - include: preprocessor-macro-define - include: scope:source.c#pragma-mark - include: preprocessor-includes - include: preprocessor-import global: - include: comments - include: preprocessor - include: strings - include: quotes - include: variables main: - include: global - match: \\b(if|else|for|while)\\b scope: keyword.control.c