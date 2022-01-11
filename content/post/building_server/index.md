---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "How to build your own Machine Learning Server"
subtitle: "in one possible way"
summary: ""
authors: 
- Dylan Yang
tags: 
- Linux
categories: []
date: 2022-01-11T15:51:36-05:00
lastmod: 2022-01-11T15:51:36-05:00
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





# Buy a PC with Nvidia GPU

- For CPU, you can choose AMD
- For GPU, Nvidia GPU is your only choice

Notice that the bottleneck of your machine could most likely be the GPU memory. Personally speaking, for example, I think 1080Ti (12GB) is better than 2080(8GB).


# Install Liunx

the most popular Linux version is Ubuntu. Choose a popular and stable version (18, 20). 


- use a USB boot helper to partition the desk at first (purchase on ebay or Amazon)
  - buy USB boot
  - download Ubuntu install disk file in advance in the USB
  - plug in before launching
  - F12 or F11 to get into boot setting interface
  - choose USB as the boot desk
- install Ubuntu (follow the document in offical websites)


# Get connect to Server

## add a sudo user
Ubuntu has graphic interface, but adding user via terminal is more popular:

```bash
adduser UserName
usermod -aG sudo UserName
su - UserName
```


{{% callout note %}}
Adding a new user means you have a new folder in `/home` directory, so everything you install in the branch of `/home/User` will be your personal configuration. And everything you install in `/home` (you can create a new folder as `/home/share`) can be shared to all users.(or set as global default setting).

For example, I install python2.7 user `/home/share` and install some Deep Learning packages as Pytorch, Tensorflow. Everyone can set the default location of Python interpreter as `/home/share/python27`. 


{{% /callout %}}



## ssh connection

- get internet connection in server
- get ip address in server 
- generate keys in local
- send public to server
- config ssh in local

### get internet connection

strongly recommend that you use cable instead of wifi since some PC does not have wireless internect card pre-installed. If it is pre-installed, the compatible driver may not be pre-installed.

### get IP address 

```bash
ipconfig a
```

google which one is ip address. 


{{% callout note %}}
Please notice that there are 2 kinds of ip address: dynamic and static.

For internet connection in your home, the ip address is most likely to be dynamic. If you want to set your server in your home, you must call your server provider to give you a static ip address (add money...)

For server located in the school(US), the internet server will allocate you a static address which is corresponding to your mac address(see as an unique identity of your device). So you don't have any issue here.

{{% /callout %}}

### install ssh in the server


```bash
sudo apt-get update

sudo apt-get install openssh-server

```

### generate rsa key pair

the logic is to generate a public key and private key pair.  The private is stored in local and public is sent to the server.

in the local computer (Mac) for instance:

```bash
ssh-keygen -t rsa -f ~/.ssh/my-server-key

```
then there will be two new rsa files under `~/.ssh`:
- `my-server-key.pub`
- `my-server-key` (this is private key)

### send public to server

Then send public key to the server
```bash

ssh-copy-id -i ~/.ssh/my-server-key.pub YOUR_USER_NAME@IP_ADDRESS_OF_THE_SERVER


```

### config ssh file

And then config the ssh file into your local PC.

```bash
nano ~/.ssh/config
```

the key is to set HostName, User and IdentityFile correctly. For example, the user account in the server is `dylan`. And I send `my-server-key.pub` to my server located in `192.168.00.00`.

I can add few lines as:


```
Host my-server (any name is ok)
  HostName 192.168.00.00 (ip address of your server)
  User dylan (user account name in server)
  IdentityFile /home/username/.ssh/my-server-key (the location of your private key in local)

```


# install dependencies

including CUDA, pytorch, ... 

Strongly recommend to use conda as env manager. 