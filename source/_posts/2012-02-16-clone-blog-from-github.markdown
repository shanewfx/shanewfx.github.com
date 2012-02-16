---
layout: post
title: "clone blog from github"
date: 2012-02-16 14:59
comments: true
categories: octopress 
---

按照如下步骤：


- cd f:

- cd blog

- `git clone git@github.com:shanewfx/shanewfx.github.com.git`

- `cd shanewfx.github.com`

- `git checkout source`

- `rake setup_github_pages` and input *git@github.com:shanewfx/shanewfx.github.com.git*

- `rake new_post["test clone from github"]`, edit the file

- `rake generate`

- `rake deploy`

- `git add .`

- `git commit -am 'test clone'`

- `git push origin source`

