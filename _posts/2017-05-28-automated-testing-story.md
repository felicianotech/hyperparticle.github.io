---
layout: post
title: How changing 'localhost' to '127.0.0.1' sped up my test suite by 1,800%
---

# **Work In Progress!**

I would like to share a (somewhat recent) story on how a one-line code change improved my understanding of software engineering; it's an experience in my life that has had a profound effect on my work ethic ever since.

## Background

So it is 2015. I'm a CS student fresh out of sophomore year, getting ready to jump into the second half of my degree. At that time I had just learned how to construct a B-tree, built my own recursive descent parser, and finished a research fellowship in software security. Having seen how quickly my peers were getting programming jobs with just under 2 years of experience, I decided it was time for me to find some work too.

Fast forward to the next semester, when I landed myself a software engineering job as a QA intern. We practiced many of the common Agile practices: I attended scrum meetings, completed user stories, and documented defects.

The company had amassed some technical debt in the way of automated testing, as many of the integration tests for their main product required manual intervention. So they hired me with the primary goal to automate as many regression tests in the product's test suite as possible. Most of the easy ones were already automated, and the harder ones involved things like clicking a series of UI buttons where no SDK was implemented, or plugging in a physical piece of hardware to the network and wait for it to show up.

These integration tests naturally required me to be invested in quite a few disparate components of the entire system. One day I would be executing JavaScript in the browser, and in the next I would be sniffing for packets over the network. In order to test any interaction between component, I had to really understand the language each component talks, from the network protocols to the objects. For the testing, there was a pre-built testing system, but it was woefully inadequate to be a comprehensive integration testing suite.

## The Issue

But there was one thing that was really bugging me. One of the members of the QA team mentioned that they saw the tests run blazing fast on a separate VM configured for a different environment.

Fast forward a few months, where I was running large portions of this test stuite every day. I knew I was supposed to be working on adding more tests, but I decided that enough was enough, and took a trip down the rabbit hole to find that bug that had been crawling under my skin for months.

So I did what any sensible intern would do: I started commenting out chunks of non-essential code to see what would happen. In hindsight, there could have been much better approaches to solving this problem, but this was a product of my inexperience in the field, not problem solving capability (but hey, the method did end up working for me).

<script src="https://gist.github.com/Hyperparticle/41fd65e83bd3aa946eb1488979bb198d.js"></script>

## A Breakthrough

And then the most liberating feeling came upon me. BAM! The dots flew accross the screen. My eyes opened wide. THIS is it!

After committing the code, I went and tested the new test suite time. It was 5 minutes. So by changing that one little line of code, the tests ran from 90 minutes to 5 minutes, an 18x improvement.

At that time, I was just content enough that it was fixed, and didn't bother checking _why_ this was the case. In hindsight, I should have investigated this more fully before leaving. I briefly heard one of the team members mention that this issue could have been caused by 'localhost' being resolved as IPv6 incorrectly, then falling back to IPv4 after a second.

## The Aftermath

But at the time of writing this post, I did some digging to see what might actually have caused this bizarre behavior. I landed on [this Stack Overflow post](http://stackoverflow.com/a/15436435), in which lies the answer.