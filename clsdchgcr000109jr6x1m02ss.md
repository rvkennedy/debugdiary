---
title: "Using curl in Windows to download files"
seoTitle: "Using curl in Windows to download files"
datePublished: Tue Apr 26 2016 12:45:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdchgcr000109jr6x1m02ss
slug: using-curl-in-windows-to-download-files
canonical: https://roderickkennedy.com/dbgdiary/using-curl-in-windows-to-download-files

---

This downloads a file if it's newer than the one that's on your local drive:

curl -z "path\\filename.lib" ftp://ftp.yoursite.com//ftppath/filename.lib -o "path\\filename.lib"

The "-z" means only copy if it's newer than a specified date. But then you just put the filename, meaning that curl should use the date of that file. If the file doesn't exist locally, you'll get a warning about "invalid date or not a file". This can be ignored. The "-o" means download it to the local file specified (same filename as for -z), instead of just outputting it to the console.