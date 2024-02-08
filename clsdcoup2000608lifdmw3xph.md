---
title: "Cygwin connecting by ssh to remote Linux"
seoTitle: "Cygwin connecting by ssh to remote Linux"
datePublished: Thu Aug 20 2015 11:39:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdcoup2000608lifdmw3xph
slug: cygwin-connecting-by-ssh-to-remote-linux
canonical: https://roderickkennedy.com/dbgdiary/cygwin-connecting-by-ssh-to-remote-linux

---

If ssh can't add your server to its list of known hosts, you may need to create a "passwd" file in C:\\cygwin\\etc. Use:

`mkpasswd -l -p "$(cygpath -H)" > /etc/passwd`