---
title: "Managing multiple SSH private keys with Git."
author:
  name: lgh
date: 2023-04-03 18:30:00 +0900
categories: [Git]
tags: [Git, SSH]
---

# Purpose
* How to register 2 or more SSH Private Keys and access each GitHub Repository.

# SSH Configuration

```bash
* /.ssh/config
# work account
Host github.com-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_work

# me account
Host github.com-me
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_me
```

# Repository Git Configuration

```bash
* /.git/config
[remote "origin"]
url = git@github.com-work:"Repository URL"
# url = git@"ssh Config Host":"Repository URL"
fetch = +refs/heads/*:refs/remotes/origin/*

[user]
name = "UserName"
email = "Email"
```