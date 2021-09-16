---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Connect your own PC with Github"
subtitle: ""
summary: "Using SSH Authentication"
authors:
- Dylan Yang
tags: 
- Git
categories: []
date: 2021-09-16T17:30:57-04:00
lastmod: 2021-09-16T17:30:57-04:00
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



# HTTPS Connection
If you use HTTPS connection with Github instead of SSH, you may need to type in your `username` and `password` everytime you make any commit and push to the Github like this:

```bash
(base) dalab@lambda:~/Projects/MA$ git push
Username for 'https://github.com': dayuyang1999
Password for 'https://dayuyang1999@github.com':
Counting objects: 3, done.
Delta compression using up to 36 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 610 bytes | 610.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/dayuyang1999/MA.git
   d6e853f..fe2b751  master -> master


```

One thing important that here the `password` is not indeed your Github Account password. Instead, it is the `Personal Access Tokens` Github generated. [detail see here](https://github.com/settings/tokens/)

The conclusion is HTTPS Connection with your repo is not a good solution, since you have to generate an accesss token frequently to ensure security. I strongly recommend you connect with SSH Authentication.

# SSH Connection

The Cloud server encrypt the data using the `Public Key` provided by the user.

Then, transfer the encrypted data to user

User holds the `Private Key` to decipher the data

---
Some terms:
- public key: a encryptor
    - user should let the server know what her public key is
- private key: a decipher
    - user should not tell private key to anyone. Only hold it in local.
- ssh agent: A helper program keep track user's identity
    - 

---

So the general SSH Key Authentication Process is like this:

1. User create Key pair
1. Private key stays with the user (and only there), while the public key is sent to the server.
1. Server stores the public key
1. Server will now allow access to anyone who can prove they have the corresponding private key.


For the Github case, refer to [Github SSH Docs](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh) for detail.


# When you clone your Project 

Like I said, I recommend you to connect with your Github Repo with SSH. So when you clone the repo, please copy the SSH URL instead of HTTPS(by default) 

![](https://cdn.mathpix.com/snip/images/zCIAUdmE3ysCbvhKyjFAg8-fy3cKQ9ucOgKtDQxrWLw.original.fullsize.png)

such as:

```bash
git clone git@github.com:dayuyang1999/Sample.git

```

if you have already clone via HTTPS URL, you will find you have to use the "HTTPS" way (type in username and password) even after you successfully connect your local to Github server. However, it's simple to switch the connection to SSH:

```bash

git remote -v # check the remote connection status is https or ssh

git remote set-url origin git@github.com:USERNAME/REPOSITORY.git  # refer to here https://docs.github.com/en/get-started/getting-started-with-git/managing-remote-repositories

git remote -v # recheck the remote 


```



