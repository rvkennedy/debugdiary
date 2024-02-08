---
title: "How to create a plugin for Lumberyard"
seoTitle: "How to create a plugin for Lumberyard"
datePublished: Thu Feb 25 2016 12:58:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdcglg7000009jr95r47h9d
slug: how-to-create-a-plugin-for-lumberyard
canonical: https://roderickkennedy.com/dbgdiary/how-to-create-a-plugin-for-lumberyard

---

Lumberyard/CryEngine uses [waf](https://en.wikipedia.org/wiki/WAF), a build configuration system similar to CMake.

To add a plugin project, create a subdirectory NewPlugin in dev/Code (a plugin might go in dev/Code/Sandbox/Plugins for example).

Add a wscript file to this directory, looking like this:

def build(bld):

bld.CryPlugin( target = 'NewPlugin', vs\_filter = 'Sandbox/Plugins', file\_list = 'newplugin.waf\_files', features = \['qt'\], includes = \[ '.', '..' \], use='EditorCommon' )

And add a file newplugin.waf\_files, looking like:

{ "NoUberFile": { "Root": \[ "NewPlugin.cpp", "NewPlugin.h" \] } }

Then go to dev/\_WAF\_/specs, and edit all.json. Add "NewPlugin" to the win\_profile\_modules and win\_debug\_modules list. Finally, recreate the solution by opening a command prompt in dev/ and running "lmbr\_waf configure"