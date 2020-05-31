---
title: GSOC Weekly Reports
tags: [mypy, GSoC]
---

This post includes all the weekly reports on my GSOC 2020 project with mypy, starting from the day coding starts(in my case, May 22nd after discussion with my mentors). Each weekly report will record significant progress, findings, and problems during the week.

Some useful links:

- [GSOC project page](https://summerofcode.withgoogle.com/projects/#4924815141502976)
- [mypy issue page](https://github.com/mypyc/mypyc/issues/709): main discussions happen here and its linked PRs

If you have any questions, please feel free to reach [TH3CHARLie](mailto:th3charlie@gmail.com).

The following posts will be a stack, it always starts with the newest update.

<!--more-->

## Week 1: 2020-05-24 ~ 2020-05-31

In the first two days of the week, we reviewed and merged [python/mypy#8880](https://github.com/python/mypy/pull/8880) into the master branch. As a summary, #8880 brings:

- A new `CallC` IR element, abstracting high-level code that eventually maps to C function calls. Compared to the very first implementation, the reviewed version added support of `error_kind` and void types(via `RType`)
- Transform `str.join` from `PrimitiveOp` to `CallC`, along with IR dump test.

On Tuesday, Jukka and I had our first weekly video meeting via Zoom. We discussed naming issues in the review. He clarified how steals and is_borrowed work(related to reference counting) and the difference between self.emitter.emit and self.emit(the latter one is just wrapper). We also talked about implementing ops with different features(name reference op, top-level function calls, boolean op, etc) to refine the design.

As a result of our discussion, I implemented [python/mypy#8902](https://github.com/python/mypy/pull/8902) to support top-level function call. Different from the previous PR which only considers method call, this introduces a new op registry and modifies the IR building process accordingly. Besides the main idea, the PR has some other significant spots:

- add type coercing between formal argument/return types and actual argument/result types
- add `steals`
- fix the error that `CallC` is always of `ERR_MAGIC`

The PR is currently under review but it should be merged soon.

Finally, starting from #8902, I will open separate, fine-grained issues on mypyc's tracker to better track our progress.


## Week 0: 2020-05-17 ~ 2020-05-23

After discussions with both my mentors, we agree that I am familiar with the community now so we start the coding period a little bit early than the official date(June 1st) to buffer any unexpected delays.

On May 22nd I had the first daily sync with Jukka via Gitter. We discussed some implementation details about a new `CFunctionCall` to replace `PrimitiveOp` that simply calls a c function. To prevent unexpected change to codebase replies on mypyc, we changed our prototype op from frequently used `list.__len__` to `str.join`.

During weekends, I finished the PR demonstrated the `CFunctionCall` idea: [python/mypy#8880](https://github.com/python/mypy/pull/8880). The general idea is to have a `CFunctionCall` IR op to hold a C function name string and related argument values, which can be easily generated to corresponding C code. Therefore replaces codegen callback style used in `PrimitiveOp`. We still need some kind of low-level description `LLOpDescription` so we can match the AST during irbuild.

In the following week, I expect we'd work mainly around this PR, responding to code reviews and discuss the following questions:

- IR dump: I find that `str.join` dumps textual output exactly like `r2 = foo.join(r1)`, instead of `r2 = PyUnicode_Join(foo, r1)`. I assume overloading `to_str` method is the way to go but clearly something is missing here.
- Variants: I should inspect all the supported primitives so far and find out all the calling variants(void return, var_arg, etc), and change `CFunctionCall` accordingly.