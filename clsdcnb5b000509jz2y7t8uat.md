---
title: "Versioning SDK's with Git for builds with Jenkins"
seoTitle: "Versioning SDK's with Git for builds with Jenkins"
datePublished: Thu May 05 2016 12:48:00 GMT+0000 (Coordinated Universal Time)
cuid: clsdcnb5b000509jz2y7t8uat
slug: versioning-sdks-with-git-for-builds-with-jenkins
canonical: https://roderickkennedy.com/dbgdiary/versioning-sdks-with-git-for-builds-with-jenkins
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1707397563332/de4a1a27-8576-4655-9007-60526189871b.jpeg

---

Alright I think I've finally figured out a way to build SDK's with versioning.

Git for source control, obviously.

Development happens on the "main" branch. There's a "dev" branch for experimental stuff.  
About every other month, I'll create a numbered branch. I've started with 4.0 because it continues from the old versioning system.

Each numbered branch receives only bug-fixes. All feature changes go in the main branch.

Jenkins gets a list of versions to build, defined in the main Jenkins Config as an environment variable. At the moment is looks like this:

![Capture.jfif](https://cdn.hashnode.com/res/hashnode/image/upload/v1707397561391/ad67f0df-462a-4009-9693-13615852f118.jpeg align="left")

Using Jenkins' [Dynamic Axis Plugin](https://wiki.jenkins-ci.org/display/JENKINS/DynamicAxis+Plugin) and Matrix Projects, I use this as an axis for the matrix builds. Over time, new project numbers will be added and older ones will drop off. Jenkins will add build numbers to the version numbers, so I'll end up with installers numbered 4.0.125 etc.

Because only bug-fixes go in numbered branches, higher build numbers will always be more stable. And once you've chosen a numbered branch for your project, you can stick with that number, report to us any bugs, and be confident that the fixes will go in without any new breaking changes.

And I can develop freely on the main branch without worrying about breaking the versions in use by customers.

This should work. I'm probably the last person to figure this out.