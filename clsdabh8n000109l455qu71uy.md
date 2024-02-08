---
title: "Signing installers with certificates"
seoTitle: "Signing installers with certificates"
datePublished: Tue Apr 10 2018 13:09:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdabh8n000109l455qu71uy
slug: signing-installers-with-certificates-1
canonical: https://roderickkennedy.com/dbgdiary/signing-installers-with-certificates

---

10 Apr

Windows Defender has recently decided to falsely mark all of our installers as containing some virus or other.

It'll be a long long while before they get around to questioning whether their algorithms are in fact, "full of it", as they say, so let's see what happens if we sign our executables using a root certificate.

First, get a certificate, from Comodo. This takes weeks while they check whether an arbitrary non-governmental organization, Dun and Bradstreet, regards your company as genuine. Just check with Companies House? Way too simple!

So you need to get a DUNS number from D&B, then buy a certificate from tucows/Comodo.

After jumping through their hoops (which don't seem to be very secure to me, just cumbersome), you'll get a .crt file.

Then [https://support.citrix.com/article/CTX221295](https://support.citrix.com/article/CTX221295) will tell you how to convert your crt to a pfx.

Finally, use the pfx and signtool.exe (in the Windows SDK) to sign your executable.