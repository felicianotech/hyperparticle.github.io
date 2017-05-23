---
layout: post
title: How changing 'localhost' to '127.0.0.1' sped up my tests by 1,800%
---

**Work In Progress! Don't read this unless you're me.**

You're a CS student, fresh out of sophomore year, getting ready to jump into the second half of your degree. 

In 2015, I had the wonderful opportunity to start an internship at a software company. I was fresh out of my sophomore year, with just over two years of programming experience inside the classroom. I was hired as a QA, with the primary goal of automating as many regression tests in the product's test suite as possible. This meant I was invested into most components of the entire system, because in order to test something, you first have to understand the language each component talks, and for that to happen, you usually need to know how it works. For the testing, there was a pre-built testing system, but it was woefully inadequate to be a comprehensive integration testing suite.

But there was one thing that was really bugging me. One of the members of the QA team mentioned that they saw the tests run blazing fast on a separate VM configured for a different environment.

Fast forward a few months, where I was running large portions of this test stuite every day. I knew I was supposed to be working on adding more tests, but I decided that enough was enough, and took a trip down the rabbit hole to find that bug that had been crawling under my skin for months.

So I did what any sensible intern would do: I started commenting out chunks of non-essential code to see what would happen. In hindsight, there could have been much better approaches to solving this problem, but this was a product of my inexperience in the field, not problem solving capability (but hey, the method did end up working for me).

And then the most liberating feeling came upon me. BAM! The dots flew accross the screen. My eyes opened wide. THIS is it!

After committing the code, I went and tested the new test suite time. It was 5 minutes. So by changing that one little line of code, the tests ran from 90 minutes to 5 minutes, an 18x improvement.

At that time, I was just content enough that it was fixed, and didn't bother checking _why_ this was the case. In hindsight, I should have investigated this more fully before leaving. I briefly heard one of the team members mention that this issue could have been caused by 'localhost' being resolved as IPv6 incorrectly, then falling back to IPv4 after a second.

But at the time of writing this post, I did some digging to see what might actually have caused this bizarre behavior. I landed on [this Stack Overflow post](http://stackoverflow.com/a/15436435), in which lies the answer.