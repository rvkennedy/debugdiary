---
title: "Jenkins CI on Windows - Git can't access remote submodules - even public ones."
seoTitle: "Jenkins CI on Windows - Git can't access remote submodules"
datePublished: Mon Jul 13 2015 11:34:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdbkikk00010albe2b5axwt
slug: jenkins-ci-on-windows-git-cant-access-remote-submodules-even-public-ones
canonical: https://roderickkennedy.com/dbgdiary/jenkins-ci-on-windows-git-cant-access-remote-submodules-even-public-ones

---

I'm setting up Jenkins on a windows machines, looks great. But after setting up all the credentials, projects with Git submodules cause a problem - we get git authentication errors.

These errors go away if you set the environment variable HOME to the parent of your .ssh directory. There's also a JENKINS\_HOME variable. Possibly setting this and copying the .ssh directory to Program Files (x86)/Jenkins might have the same effect, but I've not tested that yet.