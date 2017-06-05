---
layout: post
title: How changing 'localhost' to '127.0.0.1' sped up my test suite by 1,800%
---

# **Work In Progress!**

I would like to share a (somewhat recent) story on how a one-line code change improved my understanding of software engineering; it's an experience in my life that has had a profound effect on my work ethic ever since.

## Background

So it is 2015. I'm a CS student fresh out of sophomore year, getting ready to jump into the second half of my degree. At that time I had just learned how to construct a B-tree, built my own recursive descent parser, and finished a research program in software security. Having seen how quickly my peers were getting programming jobs (with just under 2 years of coding experience), I decided it was time for me to find some work too.

Fast forward to the next semester, when I landed myself a software engineering job as a QA intern. We practiced many of the common Agile practices: I attended scrum meetings, completed user stories, and documented defects.

The company had amassed some technical debt in the way of automated testing, as many of the integration tests for their main product required manual intervention. So they hired me with the primary goal to automate as many regression tests in the product's test suite as possible. Most of the easy ones were already automated, and the harder ones involved actions such as clicking a series of UI buttons where no SDK was implemented, or plugging in a physical piece of hardware to the network and wait for it to show up.

These integration tests naturally required me to be invested in quite a few disparate components of the entire system. One day I would be executing JavaScript in the browser, the following day I would be sniffing for packets over the network, and the next day I would write SQL queries to check for state changes. In order to for me to test any interaction between services, I had to learn the language in which each component talks, from the network protocols to the objects that abstracted these low-level details.

After a couple months of getting blasted with the fire hose, things finally started to click into place. I would come in the office each day, provision a couple virtual machines, RDP/SSH into them, execute network requests, make some code changes, commit and push them, attend a meeting or two, and then close up shop.

## The Issue

At the time, the QA team adopted the [Robot Framework](http://robotframework.org/) to perform all their integration tests. It worked quite well for what it did, as the tests could be written in business language that directly translated to the acceptance tests we tracked in our system. Here's a taste of what it typically looks like.

<script src="https://gist.github.com/Hyperparticle/2c2772b0eef918068b4af7dfe2478542.js"></script>

Each line below `Valid Login` is executed in sequence, performing some kind of action or validation step. We used a Python driver to translate from this high-level language to the code that performs these tests. The Python class might look something like this.

<script src="https://gist.github.com/Hyperparticle/bfec7f3b2ea4a2cc2632f39853dc9dd6.js"></script>

These Python functions would further go and execute JavaScript/Java/C#/SQL scripts to test various functionality. So upon executing the tests, I would be greeted with a handy visualization of the tests being executed.

![](/public/img/robot-slow.gif)

But there was one thing that was _really_ bugging me. 

One of the members of the QA team mentioned that they saw the tests run blazing fast on a separate VM configured for a different environment.

Fast forward a few months, where I was running large portions of this test suite every day. I knew I was supposed to be working on adding more tests, but I decided that enough was enough, and took a trip down the rabbit hole to find that bug that had been crawling under my skin for months.

So I did what any sensible intern would do: I started commenting out chunks of non-essential code to see what would happen. In hindsight, there could have been much better approaches to solving this problem, but this was a product of my inexperience in the field, not problem solving capability (but hey, the method did end up working for me).

## A Breakthrough

And then the most liberating feeling came upon me. BAM! The dots flew across the screen. My eyes opened wide. THIS is it! I briefly lifted my hands and shook them in joy.

After committing the code, I went and tested the new test suite time. It was 5 minutes. So by changing that one little line of code, the tests ran from 90 minutes to 5 minutes, an 18x improvement.

At that time, I was just content enough that it was fixed, and didn't bother checking _why_ this was the case. In hindsight, I should have investigated this more fully before leaving. I briefly heard one of the team members mention that this issue could have been caused by 'localhost' being resolved as IPv6 incorrectly, then falling back to IPv4 after a second.

## The Aftermath

But at the time of writing this post, I did some digging to see what might actually have caused this bizarre behavior. I landed on [this Stack Overflow post](http://stackoverflow.com/a/15436435), in which lies the answer.