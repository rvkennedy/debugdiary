---
title: "Reverting all deletes in Git, leaving file modifications in place"
seoTitle: "Reverting all deletes in Git, leaving file modifications in place"
datePublished: Fri Feb 12 2016 12:56:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdc4auj000009l364p4gap3
slug: reverting-all-deletes-in-git-leaving-file-modifications-in-place
canonical: https://roderickkennedy.com/dbgdiary/reverting-all-deletes-in-git-leaving-file-modifications-in-place

---

I asked how to do this on [StackOverflow](http://stackoverflow.com/questions/35362525/pipe-git-diff-to-git-checkout), and the answer is:

git diff --diff-filter=D --name-only | xargs git checkout

You need the "xargs": just piping the output of "diff" to "checkout" doesn't work.