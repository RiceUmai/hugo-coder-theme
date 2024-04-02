---
title: "git-ssh private Key의 다중운용"
author:
  name: lgh
date: 2023-04-03 18:30:00 +0900
categories: [Git]
tags: [Git, SSH]
---

# 목적
2개 이상의 SSH 개인 키를 등록하고 각각의 GitHub 리포지토리에 액세스하는 방법

# SSH 구성 설정

```bash
Copy code
* /.ssh/config
# 작업 계정
Host github.com-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_work

# 내 계정
Host github.com-me
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_me
```

# 리포지토리 Git의 구성 설정

```bash
Copy code
* /.git/config
[remote "origin"]
url = git@github.com-work:"리포지토리 URL"
# url = git@"SSH 구성 호스트":"리포지토리 URL"
fetch = +refs/heads/*:refs/remotes/origin/*

[user]
name = "사용자명"
email = "이메일"
```