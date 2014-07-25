---
layout: post
title: Working on gh-dashboard
categories:
- oss
- github
---

As a developer when I'm not coding nor spending time on StackOverflow good chances are that I'm doing something on GitHub. We all know GitHub is a great tool for our day to day life as programmers, it helps us in many ways and one I really like is the organization dashboard.

Working on different projects and having to follow and work on many different issues I heavily use the organization dashboard as the starting point of every single day. From there I have the control of all the things I'm involved: new issues, updated issues, issues waiting for love...it's the big picture of what I need to get done.

The initial list could be huge, fortunately you can filter issues and PRs per project so you get a smaller set of information to scan, you can also filter issues by the type of your involvement. This means you can choose to view all issues _created by you_ and all issues _assigned to you_. Unfortunately, I don't know why, GitHub folks decided to not allow you to filter issues _mentioning you_ in the organization dashboard while you can do it when browsing a single repository.

And to me this is really a missing feature as issues mentioning you are as important as all the other types as you are supposed to give advice on something or could be interested in some changes going on somewhere in the project where your teammates are working. If someone mentions you in an issue there's a reason and you want to see those issues as well, it's not just another Yo clone.

I pinged @github on Twitter a couple of times reporting this missing feature with no results, then wrote them using their contact form and quickly got a reply telling me that they "added a +1 to this on our internal Feature Request List" but also "We can't promise if we may add this, however your feedback is definitely appreciated". Thanks for you honesty.

I then looked at the API and found that filtering for issues mentioning me looks quite easy so I decided to write some code [1] in order to consume the API and get a CLI tool to solve the issue. I got something working very quickly, called it [gh-dashboard](https://github.com/dlondero/gh-dashboard) and published it on GitHub. It was just working, printing out the list of issues mentioning me in an organization. Happy!

Then I wanted to refine it a bit: colorize the output for better reading and organization of the information, allow to specify some parameters to override the default ones in order to change the organization or state or type of issues to show. So I decided to rewrite completely gh-dashboard using the [Symfony Console Component](http://symfony.com/components/Console) which is perfect for this kind of CLI commands giving you a lot of support from different points of view.

As of now [gh-dashboard is also on Packagist](https://packagist.org/packages/dlondero/gh-dashboard) and you can install it via Composer as a global dependency and you don't need anymore to set it up manually copying and editing configuration files as it's going to ask you some defaults to use on the first usage.

It works for me™, hope it works for you too!

[1] I first took a look at some of the best CLI tools out there already using GitHub's APIs to do all sorts of tricks. Some are very huge and well done but all the ones I looked into have one problem: they work at repository level (no big picture then) or at user account level (this means reading issues from ALL the repositories you are in with no separation between organizations). And this is a problem to me.
