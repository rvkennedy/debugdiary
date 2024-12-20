---
title: "Embedding v8 Javascript in an MSVC project"
datePublished: Fri Dec 20 2024 12:15:33 GMT+0000 (Coordinated Universal Time)
cuid: cm4wprjwt000d09mjbvd8bcjx
slug: embedding-v8-javascript-in-an-msvc-project

---

```bash
rem building v8
rem following https://medium.com/angular-in-depth/how-to-build-v8-on-windows-and-not-go-mad-6347c69aacd4

echo off
set Path=%cd%\depot_tools;%Path%
set DEPOT_TOOLS_WIN_TOOLCHAIN=0
set GYP_MSVS_VERSION=2022

rem curl -O https://storage.googleapis.com/chrome-infra/depot_tools.zip
rem mkdir depot_tools
rem powershell -command "Expand-Archive -Force '%~dp0depot_tools.zip' '%~dp0depot_tools'"


rem fetch v8
set Path=%cd%\depot_tools\.cipd_bin\3.8\bin\Lib\venv\scripts\nt;%Path%

echo on

rem to activate the venv, we need a pyvenv.cfg in the scripts\nt directory e.g.
rem home = C:/Code/Teleport/Javascript/depot_tools/.cipd_bin/3.8/bin/Lib/venv/scripts/nt
rem include-system-site-packages = false
rem version = 3.8

call %cd%\depot_tools\.cipd_bin\3.8\bin\Lib\venv\scripts\nt\activate.bat

rem activate.bat calls echo off, but we want to see these commands:
echo on

where python

cd v8

rem gclient sync
rem python tools/dev/v8gen.py list
rem python tools/dev/v8gen.py x64.release

rem To embed V8 into our application we need to build it as a static library. To do that, we need to modify default build configuration and add these two flags to args.gn file:

rem is_component_build = false
rem v8_static_library = true

rem  now to out.gn\x64.release/args.gn add:
rem is_debug = false
rem target_cpu = "x64"
rem is_component_build = false
rem v8_static_library = true
rem # disable thin lib shenanigans
rem  thin_lto_enable_cache = false

rem GOT to have Windows SDK 10.0.22621.2428, install by hand if necessary
rem see also https://chromium.googlesource.com/chromium/src/+/HEAD/docs/windows_build_instructions.md
rem for building on windows with msvc rather than clang

call "C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build\vcvars64.bat" x86_amd64
set WINDOWSSDKDIR=C:\Program Files (x86)\Windows Kits\10
rem gn args out.gn/x64.release --list
rem build/config/win/BUILD.GN: cflags += [ "-fms-extensions","-fms-compatibility"]
gn gen --ide=vs --ninja-executable=%cd%\third_party\ninja\ninja.exe out.gn/x64.vs.release
rem cd out.gn/x64.release
rem ninja -C out.gn/x64.release
rem -t msvc 

rem python tools/run-tests.py --gn
```