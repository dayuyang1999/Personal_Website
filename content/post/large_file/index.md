---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Never add Large file to your Github Repo"
subtitle: "It will cause bugs"
summary: ""
authors: 
- Dylan Yang
tags: []
categories: []
date: 2021-09-16T22:21:39-04:00
lastmod: 2021-09-16T22:21:39-04:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

Do not store any large files to your Github Local Directory. If you did that, remove that before making any commit. Otherwise you will see:

```bash

remote: error: File large_file.csv is 607.74 MB; this exceeds GitHub's file size limit of 100.00 MB
remote: error: GH001: Large files detected. You may want to try Git Large File Storage - https://git-lfs.github.com.
To github.com:dayuyang1999/MA.git
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to 'git@github.com:dayuyang1999/MA.git'

```

Now, the cache of large file is stored in the historical commit, so these thing won't work to enable a commit:
- delete the large file
- make new commit
- revert to historical version without clean the commit history


To solve this, use:


```bash
git filter-branch -f --tree-filter 'rm -f /path/to/file' HEAD --all
```

This will remove the file from all historical git log.  `-f` is to force rewriting any backup and `--all` is to remove file from all branches. [Detail see here](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)

The large file error won't go away by removing it from the git cache because it is still lurking in the commit history.