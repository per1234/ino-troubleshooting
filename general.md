---
title: General
description: General troubleshooting advice
---

#### "Have you tried turning it off and on again?"

Seriously, restarting the Arduino IDE or your computer can solve a wide variety of problems.

---

#### Informational output vs warning vs error

The Arduino development software outputs a variety of information. Just because you see some text in the console, doesn't mean there is a problem.

Error messages will usually contain the word "error". This will usually indicate a problem that you must fix before you can proceed.

Warning messages will usually contain the word "warning". It's a good idea to pay attention to warnings, but they don't always indicate a problem.

If the output doesn't contain the word "error" or "warning", it's probably just some helpful informational output and doesn't indicate any problem.

---

#### Make a minimal demonstration of the problem

When your sketch isn't working as expected (either not compiling or not running right), it is very helpful to distill the problem down to the absolute minimum amount of code that will still reproduce the issue. Often, after you have removed all the irrelevant stuff, the cause of the problem becomes clear. If not, this minimal demonstration sketch will be very helpful to anyone you ask for assistance as it will allow them to get right to looking at the problematic code, rather than having to wade through hundreds of lines of irrelevant code.
