---
layout: post
title:  "Contributing to Open Source"
date:   2017-09-19 09:30:00
description: Contributing to Rails Girls Summer of Code OSS project.
categories:
- blog
- Open-Source
---

As part of the graduation requirement for Turing, we took on a project to contribute to open-source. My ground selected the [Rails Girls Summer of Code](https://github.com/rails-girls-summer-of-code/summer-of-code){:target="_blank"}.  This is the repository for a program that sponsors teams of people who spend three months building an open source project and learning transferable skills.  Their mission is to bring more diversity into Open Source; a mission that is well aligned with my personal beliefs and priorities.  I was excited to help them improve their website in a some small way.

Their site was built using the Jekyll static site generator (much like this site).  The issue I took on was to add links to a blog's categories from its page. This changes their blog display from:

![Rails Girls Summer of Code - Blog - Pre change](/assets/images/oss_pre.png "Rails Girls Summer of Code - Blog - Pre change")

To this:

![Rails Girls Summer of Code - Blog - post change](/assets/images/oss_post.png "Rails Girls Summer of Code - Blog - Post change")

This required some styling changes and adding some new Liquid templating code to the blog layout.  One of the challenges with this was that liquid templating has limited enumerable functionality so I could not remove the "blog" category that showed up on each.  I got around this by linking that category back to the main blog page.

You should definitely check out the [Rails Girls Summer of Code](https://railsgirlssummerofcode.org/){:target="blank"} website and see how you can contribute to their great efforts!