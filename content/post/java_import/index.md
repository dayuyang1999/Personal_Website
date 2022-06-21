---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Unable to find JVM"
subtitle: ""
summary: ""
authors: 
- Dylan Yang
tags: 
- Information Retrieval
categories: []
date: 2022-06-06T16:12:32-04:00
lastmod: 2022-06-06T16:12:32-04:00
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

A common issue you face when switching environment using conda or different envs depends on different Java version. Here is the solution.


# Find where jdk installed

you can find the location using `whereis java` and `which java`(which only output the dir of Java that is currently used).

However, for linux Java is usually under `/usr/lib/jvm/`


# choose the version you intend to use

I installed Java8 and Java11. Some reimplementation requires different versions of Java.

If I intend to use Java11 for this time, set its dir in environment variable: `export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64`

