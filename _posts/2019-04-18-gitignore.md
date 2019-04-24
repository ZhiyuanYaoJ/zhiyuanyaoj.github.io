---
title: Configure gitignore and remove .DS_Store files
date: 2019-04-18
description: bash scripts that deal w/ gitignore
categories:
- Git
tags:
- Git
photos:
-
---

```bash
find . -name .DS_Store -print0 | xargs -0 git rm --ignore-unmatch
echo .DS_Store >> .gitignore
echo .DS_Store >> ~/.gitignore_global
git config --global core.excludesfile ~/.gitignore_global
```

# Ref
- \[1\] [Stack Overflow - .gitignore all the .DS_Store files in every folder and subfolder](https://stackoverflow.com/questions/18393498/gitignore-all-the-ds-store-files-in-every-folder-and-subfolder)<a name="ref1"></a>