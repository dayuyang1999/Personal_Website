---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Create a new project and version control"
subtitle: ""
summary: "Version Control is CRUCIAL and EZ with VSCode and Git."
authors:
- Dylan Yang
tags:
- Git
categories: []
date: 2021-09-12T16:54:34-04:00
lastmod: 2021-09-12T16:54:34-04:00
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
- [Push the Project in local to Git](#push-the-project-in-local-to-git)
- [Pull existing Project in Git to Local](#pull-existing-project-in-git-to-local)
- [Make Commit](#make-commit)
- [Roll Back to Previous Version](#roll-back-to-previous-version)

[Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)

Three most common things I met everyday:
- Pull an existing project created by other genius in Git
- Push an project I created on local
- Make Commit to either of them
- Roll Back to previous version

Here are how to to do them: (assume you already installed VSCode)

# Push the Project in local to Git


1. Go to the main dir of your project in terminal:
   1. `git init`
2. Go to your Github and create a new repository
   1. copy the repository URL
3. Back to VSCode, Open Palette and Hit `Git: Add Remote`
   1. paste the URL
   2. Give a Project Name 
4. End

# Pull existing Project in Git to Local

1. Copy the URL of the repo you like to edit in local
1. Open Terminal in Local
   1. `git clone URL`

1. End

# Make Commit

1. write something
2. Hit the tree buttom on the left bar of VSCode
   1. type in your commit message
   2. click "✅"
   3. click `...` and choose `push`
3. End


# Roll Back to Previous Version

1. find the version number you want to rollback
   1. `git log`
   2. for example, the version I found looks ok is `56e05fced `
2. roll back
```bash

# Resets index to former commit; replace '56e05fced' with your commit code
git reset 56e05fced 

# Moves pointer back to previous HEAD
git reset --soft HEAD@{1}


git commit -m "Revert to 56e05fced"

# Updates working copy to reflect the new commit
git reset --hard


```

End
