---
title: "Version 3.38.0 Released"
---

I'm happy to announce the release of [version 3.38.0](https://github.com/svunit/svunit/releases/tag/v3.38.0).
You can find the full (but fairly modest) changelog at the previous link.
I've also created a [GitHub discussion](https://github.com/svunit/svunit/discussions/309) for it.
I'm still not sure about how to best use discussions
(though GitHub does provide a list of [best practices](https://docs.github.com/en/discussions/guides/best-practices-for-community-conversations-on-github) for them),
but I kind of like the idea of keeping conversations and issues separate.

One of the main things I wanted to get into this release was a fix related to the `--filter` option.
Even when filtering out all tests from a unit test module,
SVUnit would still print messages related to these modules ([svunit/svunit#207](https://github.com/svunit/svunit/issues/207)).
For example, assuming that we ran `runSVUnit --filter 'interesting_ut.*'`,
we would get something like:

```
INFO:  [0][some_filtered_out_ut]: RUNNING
INFO:  [0][some_other_filtered_out_ut]: RUNNING
INFO:  [0][interesting_ut]: RUNNING
... tests from interesting_ut ...
INFO:  [0][some_filtered_out_ut]: PASSED (0 of 0 tests passing)
INFO:  [0][some_other_filtered_out_ut]: PASSED (0 of 0 tests passing)
INFO:  [0][interesting_ut]: PASSED (5 of 5 tests passing)
```

With many unit tests,
a lot of extraneous log output was printed,
which made it difficult to find the real tests.
The fix for this was long overdue,
but since it wasn't trivial to implement,
I kept putting it off.

Before tackling the main fix,
I wanted to work on something easier,
but somehow connected to it.
Inspired by [GoogleTest](https://google.github.io/googletest/advanced.html#listing-test-names),
I added a new `--list-tests` option to `runSVUnit`,
which is a good companion to the `--filter` option.
This new option just lists test names without actually running them ([svunit/svunit#299](https://github.com/svunit/svunit/pull/299)).
Implementing it was a good way to get familiar with the code related to test execution that I inherited.
The pull request is not the prettiest,
because it contains a lot of duplication,
but this was something to be handled later.

I did some major renovations,
by splitting the declaration of `` `SVTESTs `` from their execution ([svunit/svunit#301](https://github.com/svunit/svunit/pull/301)).
The need for this became apparent while implementing `--list-tests`,
where we really just want to get the tests that are declared.
I didn't do this in the same pull request,
in order to split the change to behavior (adding `--list-tests`)
from the change to structure (moving code out of the `` `SVTEST `` and `` `SVTEST_END `` macros).
This is good advice I got from [this article by Kent Beck](https://tidyfirst.substack.com/p/structure-and-behavior-prs).
Also, the test suite for verifying SVUnit itself was invaluable
in making sure that the code still worked as before.
A happy side effect of this change is
that it's now possible to add arbitrary code between `` `SVTESTs ``.
This is extremely useful for grouping tests and auxiliary code together:

```
class some_fake;
  // ...
endclass

`SVTEST(some_test)
  some_fake dependency;
  class_under_test uut = new(dependency);
  // ...
`SVTEST_END
```

I'm kind of proud of a small pull request I did
which refactored some code related to listing tests ([svunit/svunit#303](https://github.com/svunit/svunit/pull/303))
and which I feel has educational value.
First, I think it's necessary to highlight that [refactoring](https://refactoring.com/) means
"altering [code's] internal structure without changing its external behavior".
I mention this,
because the term is very often used wrongly within our industry to refer to any kind of "major" change,
especially to those that also sneak in some tweaks to behavior.
Each commit in this pull request makes a small, purely structural change to the code,
that could be validated by simple inspection,
though the project's test suite again helped a lot to ensure that nothing broke.

In the end,
I built up the courage to fix what I mentioned at the beginning of the post.
SVUnit no longer prints `RUNNING` for modules that don't contain any selected tests ([svunit/svunit#304](https://github.com/svunit/svunit/pull/304)).
At this point,
fixing this was a breeze,
because I applied another piece of advice from Kent Beck,
on how to deal with difficult changes:
"make the change easy, then make the easy change".

I'd like to add that SVUnit now also has support for the Vivado simulator.
I was very positively surprised by how good the tool is,
especially considering that AMD offers it for free.
If I could figure out the legal and technical aspects of how to do it,
I would gladly use it in the GitHub workflow, alongside Verilator,
to continuously test the project. Ideas are welcome.

Speaking of testing,
I couldn't run the test suite on VCS, Riviera or DSim.
If you have access to any of these tools,
I would be very grateful if you could check whether this release works on it and report back.

If you try out this release and find any bugs,
please report them on the project's [GitHub Issues section](https://github.com/svunit/svunit/issues).
