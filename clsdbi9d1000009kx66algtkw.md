---
title: "Updating the PHP version on Wordpress"
seoTitle: "Updating the PHP version on Wordpress
"
datePublished: Fri Jul 21 2023 08:11:22 GMT+0000 (Coordinated Universal Time)
cuid: clsdbi9d1000009kx66algtkw
slug: updating-the-php-version-on-wordpress-1
canonical: https://roderickkennedy.com/dbgdiary/updating-the-php-version-on-wordpress

---

Wordpress sometimes needs you to update the php version. Outside of a managed hosting, if you’re working with raw Linux, you’re pretty much stuck. Ignoring the official, unhelpful page at [https://wordpress.org/support/update-php,](https://wordpress.org/support/update-php,) what actually has to be done is this:

Using the example of updating from PHP 7.2 to 7.4, and assuming you have installed 7.4 from command-line already, add the line:

AddHandler application/x-httpd-php74 .php

to the .htaccess file in the web root. Now call:

sudo a2dismod php7.2

sudo a2enmod php7.4

sudo systemctl restart apache2

And done.