---
title: "Finding and removing files added to git by accident"
seoTitle: "Finding and removing files added to git by accident"
datePublished: Thu Aug 02 2018 13:14:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdadvht000c09jrbqi40mxq
slug: finding-and-removing-files-added-to-git-by-accident-1
canonical: https://roderickkennedy.com/dbgdiary/finding-and-removing-files-added-to-git-by-accident

---

2 Aug

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

If for example, you've added lib files by mistake to a large git repo, and want to remove them, but don't know the exact paths, use this:

git ls-files \*.lib&gt;lib.bat

Then in lib.bat you may have e.g.:  
Plugins/Media/Intermediate/Build/Win64/DebugMediaEditor/MediaEditor-Win64-Debug.lib

Add git rm --cached to the front of each line, then run the batch file and commit the result.

[Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)