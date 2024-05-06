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

- mention reworking how tests are declared and executed
  - noticed while working on previous PR, but want to split changes to behavior from changes to structure
  - test suite was invaluable here to make sure everything still works
  - mention add stuff that now it's possible to add code between `` `SVTESTs ``, show example of util function

- mention PR with refactoring
  - define refactoring, as it is wrongly used within industry
  - small steps

- mention PR with implementation of fix
  - "implement a difficult change, first make it easy, then make the change"

- mention support for Vivado
  - praise that it works very well and that it's also free

- end with:
  - mention that I haven't run on VCS, Riviera or DSim
  - link to issues page
