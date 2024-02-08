---
title: "Minimal Hello World for FASTBuild"
seoTitle: "Minimal Hello World for FASTBuild"
datePublished: Sun Jan 24 2016 12:55:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdc3bt200000ajw06gxf2it
slug: minimal-hello-world-for-fastbuild
canonical: https://roderickkennedy.com/dbgdiary/minimal-hello-world-for-fastbuild

---

[FASTBuild](http://fastbuild.org/docs/home.html) is an open source build program with amazing potential. It apparently allows distributed compilation out-of-the-box, which is something Incredibuild charge a lot of money for. But the documentation is minimal so far, and it lacks a functional example. So I put together a "Hello World" for FASTBuild. The HelloWorld.cpp is:

#include

int main(int , char \* \[\]) { std::cout &lt;&lt; "hello world!\\n"; return 0; }

And the project config file is build.bff:

;------------------------------------------------------------------------------- ; Windows Platform ;------------------------------------------------------------------------------- .VSBasePath = 'C:/Program Files (x86)/Microsoft Visual Studio 12.0' .WindowsSDKBasePath = 'C:/Program Files (x86)/Windows Kits/8.1' .ClangBasePath = 'C:/Program Files/LLVM' .WindowsLibPaths = '$WindowsSDKBasePath$/lib/winv6.3/um'

.BaseIncludePaths = ' /I"./"' + ' /I"$VSBasePath$/VC/include/"' + ' /I"$WindowsSDKBasePath$/include/um"' + ' /I"$WindowsSDKBasePath$/include/shared"'

Compiler( 'Compiler-x86' ) { .Root = '$VSBasePath$/VC/bin' .Executable = '$Root$/cl.exe' } ; MSVC Toolchain ;--------------- .ToolsBasePath = '$VSBasePath$/VC/bin' .Compiler = 'Compiler-x86' .Librarian = '$ToolsBasePath$/lib.exe' .Linker = '$ToolsBasePath$/link.exe' .CompilerOptions = '%1 /Fo%2 /c /Z7' .CompilerOptions + .BaseIncludePaths .LibrarianOptions = '/NODEFAULTLIB /OUT:%2 %1' .LinkerOptions = ' /OUT:%2 %1 /SUBSYSTEM:CONSOLE' .LinkerOptions + ' /LIBPATH:"$WindowsLibPaths$/x86" /LIBPATH:"$VSBasePath$/VC/lib"'

; Libraries ;---------- Library( 'exelib' ) { .CompilerInputPath = '.\\' .CompilerOutputPath= 'Out' .LibrarianOutput = 'Out\\exelib.lib' }

; Executables ;------------ Executable( 'myExe' ) { .CompilerInputPath = '.' .CompilerInputPattern ='\*.cpp' .CompilerOutputPath= 'Out/' .Libraries = { 'exelib' } .LinkerOutput = 'HelloWorld.exe' .LinkerOptions + ' /SUBSYSTEM:CONSOLE' + ' LIBCMT.LIB' }

; 'all' ;------- Alias( 'all' ) { .Targets = { 'myExe' } }

To build it, go to the directory in a command prompt and run FBuild.exe.  
This is for Visual Studio 2013 (12.0) - for different versions just change the VSBasePath variable.

One really interesting thing is that you can't seem to compile source files into Executables directly: you have to specify a library (e.g. "exelib" above), and use .CompilerInputPath to specify a directory. It will scan the path for all files of interest (.cpp by default) - you can specify them individually if you want. But the Executable command doesn't compile cpp's and if you don't include a library, it will complain of having no Object files to link.

#### Update 27/1/2016

FASTBuild author Franta Fulin has posted an official Hello World example: you can find it [here](http://www.fastbuild.org/docs/examples/helloworld_windows.html).