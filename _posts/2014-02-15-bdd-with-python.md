---
title: BDD-style Tests in Python
layout: post
summary: Write python tests in a behavior-driven style using nose.
---

BDD stands for Behavior Driven Development. You can go straight to
[the horse's mouth](http://dannorth.net/introducing-bdd/) for all the details,
but for the purposes of this post, when I talk about a "BDD-style" test, I
mean a test that reads like a sentence describing some behavior of the object
being tested. And I do mean sentence. For example, instead of:

```
test_post_publisher
```

you want:

```
PostPublisher:
- sets published_at to given date
- emits post_published event with post id
- does nothing for published posts
```

If you haven't already, you really should read
[Dan North's introduction of BDD](http://dannorth.net/introducing-bdd/) for all
the ways tests like these can improve your development process.

This way, your test suite should read like a set of requirements or
specifications. In fact, BDD-style tests are usually referred to as "specs".

To get there, you can use [nose](https://nose.readthedocs.org/en/latest/) (a
handy extension to python's `unittest`) with the
[pinocchio](http://darcs.idyll.org/~t/projects/pinocchio/doc/) plugin (a handy
set of extensions to nose).

```
pip install nose
pip install pinocchio
```

and write your tests with a structure like this:

```
class Test[the thing being tested](TestCase):
    def test_[sentence describing the behavior](self):
        ...
```

For example:

```
from unittest import TestCase


class TestPostPublisher(TestCase):
    def test_sets_published_at_to_given_date(self):
        ...

    def test_emits_post_published_event_with_post_id(self):
        ...

    def test_does_nothing_for_published_posts(self):
        ...
```

Then run `nosetests` with the `--with-spec` option provided (by pinocchio) and
you should see your beautiful specs instead of a boring row of dots.

But wait, since all of the underscores are turned into spaces, the specs that
mention variable names are a bit confusing (e.g. "published at" instead of
"published_at"). You can fix that with docstrings.  If nose sees one, it will
use that instead of converting the Class or method name. For example:

```
def test_sets_published_at_to_given_date(self):
    """sets published_at to given date"""
    ...
```

You can also use the `--spec-color` option to easily see the status of your
specs (yellow: pending, red: fail, green: pass).

If you want to write specs like this with Django, configure nose as your
test runner in `settings.py` with:

```
TEST_RUNNER = 'django_nose.NoseTestSuiteRunner'
NOSE_ARGS = ['--with-spec', '--spec-color']
```

This only covers the specific nuts and bolts of getting spec output with
python. I hope this marks my return to blogging, because I have lot more to say
on the subject of testing in general.
