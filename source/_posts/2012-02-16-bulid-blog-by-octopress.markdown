---
layout: post
title: "bulid blog by octopress"
date: 2012-02-16 14:45
comments: true
categories: octopress
---

- cd f:

- mkdir blog

- cd blog

- git clone git://github.com/imathis/octopress.git octopress

- cd octopress

- ruby --version  # Should report Ruby 1.9.2

- gem install bundler

- bundle install

- rake install

- modify _config.yml

- rake setup_github_pages,input git@github.com:shanewfx/shanewfx.github.com.git

- rake generate

- rake deploy

- rake new_post["bulid blog by octopress"], edit the content of generated file in _post

- rake generate

- rake deploy

- git status

- git add .

- git commit -a -m 'bulid blog'

- git push origin source

