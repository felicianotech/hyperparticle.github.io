---
layout: post
title: 'Migrating a Jekyll Website from GitHub Pages to Firebase Hosting'
---

![Firebase Logo](/public/img/firebase.png)

# Overview

I really like GitHub Pages as a a free and easy way for creating simple static websites. One major drawback is that HTTPS is not supported for custom domains (it's a [long standing issue](https://github.com/isaacs/github/issues/156)). I would like this personal website to have a custom domain name, and unsecured HTTP is unacceptable (and no, using CloudFlare will not solve it entirely), so it's a deal breaker for me.

However, all is not lost. I have tinkered with Firebase for quite some time, and it seems to be a good fit for what I want to do. Firebase provides free hosting of static websites, and the good news is that it supports custom domain names with HTTPS at no extra charge.

I managed to find someone else who did the exact same thing, detailed in [this helpful blog post](https://chris.banes.me/2017/06/02/jekyll-firebase/). I did it slightly differently (which I will explain in the next section), and it worked like a charm. When I commit new changes to the `master` branch, CircleCI will build the website and deploy it to Firebase.

This website is still using GitHub Pages, but my custom domain is now pointing to the Firebase URL instead of the GitHub Pages URL.

# Steps Taken

I mostly followed [this blog post](https://chris.banes.me/2017/06/02/jekyll-firebase/), so check it out for a more detailed overall explanation. Here I explain in more depth the portions that I did differently.

## Git Repository

Create a Git repository with a Jekyll theme. [Here is mine](https://github.om/Hyperparticle/hyperparticle.github.io), for reference.

## Firebase Setup

Create a new project in firebase. Install the Firebase CLI, and run `firebase init` in the repository.

## CircleCI Setup

Configure the repository to use CircleCI for automatic deployment.

CircleCI recently released [version 2.0](https://circleci.com/docs/2.0/), so I used the newer version of their configuration. First create a directory called `.circleci`, and then create a `config.yml` file inside. This is where the build configuration lives.

Here is what my configuration looks like.

<script src="https://gist.github.com/Hyperparticle/56c64d44170f8db648282a2177db67cc.js"></script>

There are a few things to note in this file.

- I need Ruby for Jekyll, and Nodejs for Firebase, so I found the `starefossen/ruby-node` Docker image to be suitable. 
- Ruby Gems are installed with the `bundle install` command (Make sure there is a `Gemfile.lock` in the repo) and the firebase CLI is installed with `npm install -g firebase-tools --unsafe-perm`.
- The packages are cached for faster builds.
- The website is then built with the `jekyll` Gem, and can be optionally tested for HTML issues with the `htmlproofer` Gem.
- Then the site is ready to deploy to Firebase with a CI token. Notice the environment variable `FIREBASE_TOKEN`. I obtained this token by running `firebase login:ci`, then I created a new environment variable to hold the token.

Do a `git commit` and a `git push`, and you should see CircleCI build and deploy the website for you.

## Use a Custom Domain

As a final step, go into Firebase Hosting and configure it to use a custom domain.

# Conclusion

And done! A Jekyll website with a custom domain and HTTPS is hosted free of charge.
