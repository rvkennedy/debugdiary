---
title: "Problem with handling HTML form checkboxes in php"
seoTitle: "Problem with handling HTML form checkboxes in php"
datePublished: Mon Feb 29 2016 13:42:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdbs0yn00070albf4njfjvu
slug: problem-with-handling-html-form-checkboxes-in-php
canonical: https://roderickkennedy.com/dbgdiary/problem-with-handling-html-form-checkboxes-in-php

---

php doesn't handle checkboxes properly in html forms. If you use

My Checkbox &lt;input type="checkbox" name="my\_checkbox" id="my\_checkbox"'.($current\_value?'checked':'').' /&gt;

You'll only get a result for isset($\_REQUEST\['my\_checkbox'\]) if the box is \*checked\*, not if it's cleared. So you can't test for whether the box was unchecked by the user, because using !isset would be the same whether the page was just loaded, or if the form was submitted with the box unchecked by user input. The solution from [Stack Overflow](http://stackoverflow.com/questions/4554758/how-to-read-if-a-checkbox-is-checked-in-php):

Every checkbox generated is associated with a hidden field of the same name, placed just before the checkbox, and with a value of "0".

Then isset($\_REQUEST\['my\_checkbox'\]) always returns true if the box was modified, and false if the page was just loaded. And you'll always get the correct '0' or '1' value in $\_REQUEST\['my\_checkbox'\].