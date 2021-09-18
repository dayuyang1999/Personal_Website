---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Linux Commands: File Management"
subtitle: "Do not use Windows until the world is end"
summary: ""
authors: 
- Dylan Yang
tags:
- Linux
categories: []
date: 2021-09-17T13:47:18-04:00
lastmod: 2021-09-17T13:47:18-04:00
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



# Delete File or Directory

```bash
# remove file 
rm filename
# multiple
rm filename1 filename2 filename3
# remove write-protected file with -f: force
rm -f filenma
# remove directory (-r means recursive;)
rm -r dirname
# remove multiple
rm -r dirname1 dirname2 dirname3
```