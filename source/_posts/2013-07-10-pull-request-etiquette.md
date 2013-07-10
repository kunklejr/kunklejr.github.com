---
layout: post
title: Pull Request Etiquette
comments: true
tags: git
---
Over the last few years I've found myself creating and accepting an increasing number of [Github pull requests](https://help.github.com/articles/using-pull-requests). During that time I've formed some helpful opinions on what makes a good pull request that may also be useful for you to consider. Much of what makes a successful pull request boils down to 

1. doing as much of the work for the author as possible
2. instilling author confidence in the contribution

The overarching goal in the back of your mind should be doing whatever you can to facilitate the quick acceptance of your pull request. While I list four specific practices on what promotes a good pull request below, keeping this goal in mind will likely reveal many more. After all, you didn't spend all that time fixing/editing/enhancing someone else's project for it not to be accepted, did you?

## 1. Include Tests
One of the quickest ways to slow down acceptance of your pull request is to omit tests for your changes. This doesn't just mean adding tests to cover your changes, but also editing existing ones or deleting tests rendered obsolete by your work. Project authors are busy people, especially if the the project is popular or one of many they're maintaining. While pull requests are always appreciated, leaving the testing and verification of your changes to the author is likely to move you down the acceptance queue. It certainly does for my projects.

Also, consider that the author may not be an expert in every aspect of the project. Successful, long-running projects often accumulate features from a variety of external contributors to fix edge cases, support niche features, etc. The author is unlikely to be fully knowledgeable about the intricacies of every contribution, so please help him or her out by including tests that provide some confidence in the quality and thoroughness of your work.

## 2. Keep Changes Small
When I see a pull request with many changed files scattered over dozens of commits, my first thought is "this is going to take a while; better hold off until I have more time." Unfortunately, that extra time doesn't come often enough and the pull request ends up sitting unmerged for much longer than I'd like. If possible, it's better to break your contribution up into multiple, smaller, independent pull requests. For contributions where that's not possible and larger changes are required, consider these two options:

1. Provide a lengthy explanation of the work in your pull request summary. Be sure to describe the changes thoroughly enough for the author to dig into the code with an idea of what he or she should expect to see.
2. Create an issue for the project as a way to open a discussion of the proposed changes before submitting any pull requests. This gives the author an opportunity to better understand the contribution and provide feedback. It also ensures a speedier acceptance when you finally do submit the pull request.

## 3. Stick to Existing Conventions
Nearly everyone has strong opinions on code format and conventions, but when you're submitting a pull request you need to stick with the existing conventions of the project. If the JavaScript in the existing project omits semicolons at the end of the line then you should omit them too, no matter how fervently you feel they should be included. Again, this gets back to doing everything you can to facilitate the quick acceptance of your pull request. It's very frustrating for an author to get a great contribution that they'll need to tediously comb through to reformat. It also gives the impression of a lazy or uncompromising contributor, which penalizes your work confidence scale.

## 4. Update Documentation
It's easy to get caught up in the coding aspects of your contribution, but documentation is often just as important. You don't want your hard work to go unnoticed simply because users didn't know about it. Before submitting that pull request, scan the project for existing documentation related to your change. Update it if necessary or add it if it's missing and important. Updating the documentation also gives the project author more confidence in the completeness of your work. The effort will be greatly appreciated, I promise.

If you follow these four practices and keep the goal of doing whatever you can to promote the quick acceptance of your contribution in mind, I'm confident you'll have more success getting your contributions included in some of your favorite projects while building some good will along the way.
