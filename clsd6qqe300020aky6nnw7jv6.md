---
title: "Updating the PHP version on Wordpress"
datePublished: Fri Jul 21 2023 08:11:22 GMT+0000 (Coordinated Universal Time)
cuid: clsd6qqe300020aky6nnw7jv6
slug: updating-the-php-version-on-wordpress
canonical: https://roderickkennedy.com/dbgdiary/updating-the-php-version-on-wordpress

---

Updating the PHP version on Wordpress
=====================================

21 Jul

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Wordpress sometimes needs you to update the php version. Outside of a managed hosting, if you’re working with raw Linux, you’re pretty much stuck. Ignoring the official, unhelpful page at [https://wordpress.org/support/update-php,](https://wordpress.org/support/update-php,) what actually has to be done is this:

Using the example of updating from PHP 7.2 to 7.4, and assuming you have installed 7.4 from command-line already, add the line:

    AddHandler application/x-httpd-php74 .php

to the .htaccess file in the web root. Now call:

    sudo a2dismod php7.2

    sudo a2enmod php7.4

    sudo systemctl restart apache2

And done.

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)