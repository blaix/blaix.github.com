---
title: My Thoughts on the Mocking Backlash
layout: post
summary: There's been a surge of backlash against mocking. But it's not new.
---

There's been a recent backlash against using mocks in your test code.  Some of
the [complete and utter hatred for them][0] I haven't felt the need to comment
on ([others have done that very well][1]). But some of the more subtle
criticisms reminded me of my own initial reaction to mocking. For example:

## "Mocks can give your tests false positives"

The first objection that reminded me of my initial resistance to mock is the
idea that [isolating code from your own code is dangerous][2] because it lets
you mock/stub methods that don't actually exist, and can therefore give you a
passing test for broken code.

When I raised this concern myself several years ago, I was reminded that
catching "false positives" like this is one of the jobs of your integration
tests.

Your test suite should have two layers: An inner layer of unit tests
that assert the unit being tested is behaving correctly, and an outer layer of
integration tests that assert your system as a whole is behaving correctly.
Part of your system behaving correclty is that your units are communicating
with each other as designed. Your unit tests can use mocks, but your integration
tests should not. That means if you're working on something test first, and
your unit needs to send a message to another unit, and you use a mock, yes,
your unit test will pass before the message on that other unit even exists, but
that's when your outer layer of integration tests show a failure due to the
first unit calling a method that doesn't exist.  That tells you what your next
unit test should be: start test driving the new method (or fix that typo if you
mocked the wrong method).

## "Mocks make your code hard to refactor"

Another objection I had at the time that I'm seeing again now is that
mocks make your code harder to refactor. The problem is that true mocks are
used to verify messages passed between objects. This can lead to test code
with assertions being made on many methods, verifying many arguments, and
mocks that return mocks (that return mocks).

When this happens it's true that your code is hard to refactor, since you've
basically rewritten your implementation in the tests. The simplest conclusion
is that mocks are bad and you shouldn't use them. A better conclusion is that
your code is bad and your tests are trying to tell you that.

Mocks work best when they are testing code that follows certain design
principles:

* **Single Responsibility Principle:** If you find yourself creating two, three,
four, ten mocks to run one test, that's usually telling you that your code is
doing too much and needs to be split up into smaller objects.
* **Law of Demeter:** If you find yourself creating mocks that return mocks
(that return mocks) that's usually telling you that your code is violating
[the law of demeter][3].
* **Tell Don't Ask:** If you find yourself verifying many arguments or multiple
combinations of arguments on your mocks, that's usually suggesting that your
code could be improved by following [the "tell don't ask" principle][4].

If you don't follow these principles, and you are driving your code with mocks
in your tests, that test code is going to be extremely verbose, hard to write,
and brittle. Listen to that pain! Make your tests easier to write by following
those principles. Because those principles make your code
*easier* to refactor!

And don't forget, if you've got an outer layer of integration tests, even if
you do end up in a situation where you feel like your unit tests are preventing
you from refactoring, throw them out! Drive a new implementation with better
unit tests while your integration tests act as your safety net.

[0]:http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html
[1]:https://www.destroyallsoftware.com/blog/2014/tdd-straw-men-and-rhetoric
[2]:http://www.thoughtworks.com/insights/blog/mockists-are-dead-long-live-classicists
[3]:http://c2.com/cgi/wiki?LawOfDemeter
[4]:http://pragprog.com/articles/tell-dont-ask
