---
layout: post
title: "Installing Pylibmc in Ubuntu"
date: 2013-01-24 15:54
comments: true
categories: python,pylibmc,memcached,ubuntu
---

Before you install Pylibmc on your machine, there are a few dependencies that you need to take care of, so as to ensure a successful installation.

## Install python-dev

<pre><code>sudo apt-get install python-dev</code></pre>

## Install libmemcached

<pre><code>sudo apt-get install libmemcached-dev</code></pre>

Now you can successfully install ````pylibmc```` using ````pip````

## Install pylibmc

<pre><code>sudo pip install pylibmc</code></pre>