---
layout: post
title: How changing 'localhost' to '127.0.0.1' sped up my test suite by 1,800%
---

I would like to share a (somewhat recent) anecdote on how a one-line code change improved my understanding of software engineering; it's an experience in my life that has had a profound effect on my work ethic ever since. Enjoy.

## Background

So it is 2015. I'm a CS student fresh out of sophomore year, getting ready to jump into the second half of my degree. At that time I had just learned how to construct a B-tree, built my own recursive descent parser, and finished a  software security research project. Having seen how quickly my peers were getting programming jobs (with just under 2 years of coding experience), I decided it was time for me to find some work too.

Fast forward to the next semester, when I landed myself a software engineering job as a QA intern. We practiced many of the common Agile practices: I attended scrum meetings, completed user stories, and documented defects.

The company had amassed some technical debt in the way of automated testing, as many of the integration tests for their main product required manual intervention. So they hired me with the primary goal to automate as many regression tests in the product's test suite as possible. Most of the easy ones were already automated, and the harder ones involved actions such as clicking a series of UI buttons where no SDK was implemented, or plugging in a physical piece of hardware to the network and wait for it to modify a webpage.

These integration tests naturally required me to be invested in quite a few disparate components of the entire system. One day I would be executing JavaScript in the browser, the following day I would be sniffing for packets over the network, and the next day I would write SQL queries to check for state changes. In order to for me to test any interaction between services, I had to learn the language in which each component talks, from the network protocols to the objects that abstracted these low-level details.

After a couple months of getting blasted with the fire hose, things finally started to click into place. I would come in the office each day, provision a couple VMs, RDP/SSH into them, execute network requests, make some code changes, commit and push them, attend a meeting or two, and then close up shop.

## The Issue

At the time, the QA team adopted the [Robot Framework](http://robotframework.org/) to perform all their integration tests. It worked quite well for what it did, as the tests could be written in business language that directly translated to the acceptance tests we tracked in our system. Here's a taste of what it typically looks like.

<script src="https://gist.github.com/Hyperparticle/2c2772b0eef918068b4af7dfe2478542.js"></script>

Each line is executed in sequence, the indented lines being sub-methods of a single test, performing some kind of action or validation step. We used a Python driver to translate from this high-level language to the code that performs these tests. The Python class might look something like this.

<script src="https://gist.github.com/Hyperparticle/bfec7f3b2ea4a2cc2632f39853dc9dd6.js"></script>

These Python functions would further go and execute JavaScript/Java/C#/SQL scripts to test the various events in the system. So upon executing the tests, I would be greeted with a handy visualization of their execution. Each dot on the screen represents a Robot sub-method executed.

<img src="/public/img/robot-slow.gif" style="width: 100%;" alt="Robot Example">

We had about 100 of these high level tests in total, which took about 1.5 hours to execute on our Windows VMs. I thought nothing of it at the time. So I'm happily writing more tests with the Robot Framework, adding new Python functions, and writing a few scripts here and there. But then came something that _really_ bugged me.

One of the members of the QA team mentioned that they saw the tests run much faster than usual. I turned my shoulder to see that yes, in fact, those dots were flying across the screen, and some tests were finishing in seconds where they usually took minutes. I immediately asked why this was the case. There was no definitive answer, but it seemed that the tests being run in a VM with an unusual environment was causing this behavior. It seemed quite innocuous; the VM was simply configured to test one of our non-standard production systems. How could this cause them to run so fast?

I implored further. One of the developers looked briefly into it and said that every single sub-method executed in Robot was never finishing in under a second. This probably meant there was a strange one-second delay (per test) being introduced somewhere. I wanted to look further. But alas, I had no ability to access the original VM, I had several other things to do, and to top it off the testing code had several layers of code execution: Robot looked up a JSON configuration file to execute a Python file which then looked up a second JSON file to call JavaScript functions to remotely talk to---as you can imagine debugging these tests were quite miserable at times. All I got was a log file that appended state changes at several key execution stages. I was just a newbie intern that could barely keep up with the torrent of information overload. So that was that.

## A Breakthrough

A few months go by, and I am running the test suite more and more frequently to make sure they are working properly. Running another test? Time to go grab a cup of coffee and [meander around the office](https://xkcd.com/303/). I even at one point experimented with provisioning double the VMs so that I could run two testing environments simultaneously, saving me some considerable waiting time.

But I couldn't shake this feeling. I thought to myself.
>How was that possible? Was what I saw a hoax? Did those tests just exit early without doing anything? Or maybe I wasn't hallucinating and I'm wasting so much time executing these tests over and over.

I finally cracked. I knew I was _supposed_ to be working on this and that, but I was determined with all my soul to find that bug that had been crawling under my skin for months.

So I set a time-box of two hours. The race was on to see if I could find the root cause or be forever sealed into slow-test syndrome. In the mind of an intern: what is the most reasonable approach to solving this problem? Commenting out code of course! In my mind, I was thinking that if I remove all of the non-essential code and still see the tests run slow, I have a much smaller search area to look.

So I first jumped into the JavaScript files. As I was doing this I thought to myself:
>Ok, commenting out that block broke all the Robot tests. This function isn't needed. I have no idea what _this_ piece of code does but the tests now fail randomly. Ugh, the tests are still slow. 

Then I thought maybe there's some weird configuration causing problems. I dove into the JSON files but found nothing.
>One hour gone, one to go. Now, what code _haven't_ I touched? These Python files, maybe. But all they do is call other functions in other files. Wait, what's this?

<script src="https://gist.github.com/Hyperparticle/56b0fd04dd72bdd0e52089765f1eabf6.js"></script>

> I wonder if I comment this out?

```
def __init__(self):
#     self._sut_path = os.path.join(os.path.dirname(__file__),
#                                   '..', 'sut', 'login.py')
#     self._status = ''
```
> Nope, tests fail. How about this?

```
# self.logger = logging.getLogger('localhost', 3456)
# handler = logging.StreamHandler()
# formatter = logging.Formatter('%(asctime)s %(levelname)-8s %(message)s')
# handler.setFormatter(formatter)
# self.logger.addHandler(handler)
# self.logger.setLevel(logging.DEBUG)

# for f,a in my_funcs:
#     self.logger.debug('Executing ' + f ' with args ' + a)
#     # ...
```

And without a second more, I see this pop up on my screen.

<img src="/public/img/robot-fast.gif" style="width: 100%;" alt="Robot Example 2">

And then the most liberating feeling came upon me. ***BAM***! The dots flew across the screen. My eyes opened wide. ***This*** is it! I briefly lifted my hands and shook them in joy.

I further whittled down the problem to this single line of code.
```
self.logger = logging.getLogger('localhost', 3456)
```
> What in the world could possibly cause a logging library to stall for one second every time a test is initialized?

I shot an email to the development team saying that the tests run blazing fast when I leave this piece of code out. One of the developers responded back to me.
> I wonder if you replace `'localhost'` with `'::1'`? Or maybe `'127.0.0.1'`?

So the code became
```
self.logger = logging.getLogger('::1', 3456)
```
> Nope, still a problem.

Then I tried
```
self.logger = logging.getLogger('127.0.0.1', 3456)
```
And lo and behold, the tests ran as smooth as butter. I couldn't believe it.
> Okay, this is the strangest ***FEATURE*** that I've ever seen. I don't even...

And even I didn't. I was so overjoyed at the discovery, so the sudden release of dopamine distracted me from pursuing this issue and finding the root cause. I was glad just to see that I solved the problem that was haunting me for months, and in just under two hours no less.

After committing and pushing the code fix, I went and tested the new test suite time. It was 5 minutes. So by changing a few characters (not even 1.0 lines of code), the tests ran from 90 minutes to 5 minutes, an 18x improvement.

## The Aftermath

The QA team and my managers all congratulated me on my find and awarded me some story points for the "defect" (it wasn't even documented). I could run tests with such ease, and my productivity noticeably increased as well. It felt like I was on top of the world, ready to conquer any massive challenges hurled my way.

In hindsight, there could have been better approaches to solving this problem, but this was a product of my inexperience in the field, not problem solving capability (but hey, the method did end up working for me). Or perhaps this was exactly what I was supposed to do in this situation, I mean, debugging was paltry at best.

Side note: not too long after that fateful day, the QA team and I created a new test suite based on [Mochajs](https://mochajs.org/) that ran on top of Nodejs. The idea was to remove the debugging spaghetti and needless code layering in favor of a unified testing interface. It was even faster, and 100% debugable too!

At that time, I was just content enough that it was fixed, and didn't bother checking _why_ this was the case. I left the company about a year later, not ever knowing what could have caused that problem (but don't fear, the next section will explain the cause!). I knew I should have investigated this more fully before leaving. But I was blinded by the sudden rush of bliss, coupled with the fact that we were planning on replacing the testing framework anyway. And now I think I am much more likely to not repeat this mistake. It is a learning opportunity to seek out the root cause of issues. It makes me a much more nuanced programmer as a result.

But the most important lesson I learned from this entire ordeal is to constantly challenge the status quo. Don't be complacent when something doesn't seem right. Take some brief time to (safely) explore issues that grind your gears. Even when there are other things to do, it helps to gain some perspective by experimenting with proposed solutions to an unknown problem. It may not result in _the_ solution, but it may be worth the while just to learn some of the contributing factors.

## After the Aftermath

At the time of writing this post, I did some digging to see what might actually have caused this bizarre behavior. I landed on [this Stack Overflow post](http://stackoverflow.com/a/15436435), in which lies the answer. The question was about MySQL, but it still applies here.

I have high confidence that the issue was caused by IPv4 vs IPv6 discrepancies on Windows machines. I vaguely remember that the QA team's VMs were configured to prefer IPv6 addresses when they could. This meant that a DNS lookup for `'localhost'` resolved to the IPv6 string `'::1'`. However, the logging server was not bound to the IPv6 address, but the IPv4 address. So Windows failed to bind to `'::1'` in the default 1-second timeout window, in turn falling back to the IPv4 version, `'127.0.0.1'`. This inadvertently succeeded, and the tests resumed after the timeout. But the placement of this code meant that this would happen _each and every single time_ a test would run. The likely reason why we initially saw a VM run the tests very fast was because the IPv4 address `'127.0.0.1'` was the default resolved address for localhost. How frustrating.

There's no place like 127.0.0.1, eh?
