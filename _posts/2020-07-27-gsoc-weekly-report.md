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

## Week 9: 2020-07-20 ~ 2020-07-26

This week we introduced const integer optimization to handle the verbosity problem introduced by low-level IR. With this optimization, we can inline all `LoadInt` ops in both pretty IR printing and generated C.

We also added missing signed type casts to generated C and completely removed the C wrappers and merged all int logical ops into the new style.

As a summary, this week we have the following PRs:

- [[mypyc] Constant integer registers optimization](https://github.com/python/mypy/pull/9158), merged
- [[mypyc] Fix signed integer comparison](https://github.com/python/mypy/pull/9163), merged
- [[mypyc] Optimize const int regs during pretty IR printing](https://github.com/python/mypy/pull/9181), merged
- [[mypyc] Expand int ops when operation contains at least one tagged int](https://github.com/python/mypy/pull/9187), merged
- [[mypyc] Complete support of int comparison ops](https://github.com/python/mypy/pull/9189), merged

## Week 8: 2020-07-13 ~ 2020-07-19

This week we made several integer-op related improvement and bugfixes. We fixed the performance regression by generating inline compare statement when both operands are short ints. We also supported swapping operands, negating result and checking both operands when not equality check, to support all logical ops.

For tagged ints, we stored doubled value in IR, instead of doubling them during codegen, this could potentially simplify other backends' implementations.

As a summary, this week we have the following PRs:

- [[mypyc] Generates inline compare statement on short ints and fix sieve performance regression](https://github.com/python/mypy/pull/9127), merged
- [[mypyc] Check both operands when op is not eq or neq](https://github.com/python/mypy/pull/9148), merged
- [[mypyc] Support swapping operands and negating result and merge int NEQ](https://github.com/python/mypy/pull/9149), merged
- [[mypyc] LoadInt store doubled value if tagged integer](https://github.com/python/mypy/pull/9162), merged
- [[mypyc] Refactor analysis package](https://github.com/python/mypy/pull/9164), merged

## Week 7: 2020-07~06 ~ 2020-07-12

Originally mypyc uses C wrapper functions to handle most integer operations so that the IR only need to generate simple calls to these wrapper. Although the IR side can be implemented very easily, the generalization can be hard to do for other backends. This week, we started to put our last week's discussion into practice, we add new low-level integer operations and use transforms and irbuild to build blocks of IRs representing the old C wrappers. With these modifications, mypyc now generates much more low-level and verbose IR for integer operations while keep the generated C code largely unchanged.

We also merged a lot of other ops.

At the end of this week, [mypyc-benchmarks](https://github.com/mypyc/mypyc-benchmarks) is online so we can monitor performance changes. There are two microbenchmark regressions happen on one of recent commit.

As a summary, this week we have the following PRs:

- [[mypyc] Add BinaryIntOp for low-level integer operations](https://github.com/python/mypy/pull/9108), merged
- [[mypyc] Merge more primitive ops](https://github.com/python/mypy/pull/9110), merged
- [[mypyc] low-level integer operations: integer equal](https://github.com/python/mypy/pull/9116), merged
- [[mypyc] Properly box int32/int64](https://github.com/python/mypy/pull/9119), merged
- [[mypyc] Merge exception ops](https://github.com/python/mypy/pull/9121), merged


## Week 6: 2020-06-29 ~ 2020-07-05

Recent weeks' progress enables us to represent most mypyc's existing primitives in new low-level style. This week we focus on two sets of remaining ops: `in` ops and exceptions-related ops. `in` ops have different calling order in C(and potentially other backends) compared to python syntax, therefore we supported argument reordering via optional argument order in `CallC`. For exception-related ops, they always fail and the old way uses inline function to generate a false boolean follows the call, which is both hard-to-generalize and inelegant. We introduced `ERR_ALWAYS` error kind and related transform to generate IRs representing this semantics.

This week we also started to discuss the inline integer operations design at [Integer binary ops design discussion](https://github.com/mypyc/mypyc/issues/743).

I took several days off this week until next Tuesday to handle some administrative works of my graduation.

As a summary, this week we have the following PRs:

- [[mypyc] Support argument reordering in CallC](https://github.com/python/mypy/pull/9067), merged
- [[mypyc] Support ERR_ALWAYS](https://github.com/python/mypy/pull/9073), merged

## Week 5: 2020-06-22 ~ 2020-06-28

This week we focused on dealing with `call_negative_bool_emit` and `negative_int_emit`. These are two customized emit callback functions designed to handle the difference between C function return values and expected python values. To address this, we introduced a new `error_kind` variant and its corresponding branch variant, adding a new `Branch` op to handle the negative int comparison via the exception transform. These changes helped us to solve the `call_negative_bool_emit` case. To handle the remaining one, we added a new `Truncate` op to represent the cast from C int return value to bool explicitly in IR level.

After these two changes, we'd able to represent most of existing primitive ops, the remaining ones would be `simple_emit` and `name_ref`.

As a summary, this week we have the following PRs:

- [[mypyc] Introduce subcommand to mypyc command line interface](https://github.com/python/mypy/pull/9030), ongoing
- [[mypyc] new error_kind and branch variant to handle call_negative_bool_emit](https://github.com/python/mypy/pull/9035), ongoing
- [[mypyc] handle negative_int_emit and Truncate op](https://github.com/python/mypy/pull/9050), merged


## Week 4: 2020-06-15 ~ 2020-06-21

This week started with refactoring [#8973](https://github.com/python/mypy/pull/8973), we decided to take one step at a time so we only supported literal names and replace related `LoadStatic` usages to `LoadGlobal`. Based on that, we'd further implement `LoadAddress` to handle loading address specifically. Two `LoadGlobal` and `LoadAddress` will both support the registry-based style that is currently used by `name_ref_ops`.

Another topic of this week was to merge more primitive ops to `CallC`, since it's design has become more and more mature. There are some remaining ops that are tricky and need special handling like adding new error kinds and corresponding error transforms.

On Thursday, Jukka, Sullivan and I had our first monthly meeting. We discussed about name generation in mypyc, and a plan to implement low-level integer/pointer arithmetic to help represent some marcos and inline functions.

As a summary, this week we have the following issues and PRs:

- Issues:
  - [Integer and pointer operations in new IR](https://github.com/mypyc/mypyc/issues/741) by Jukka, ongoing

- PRs
  - [[mypyc] Add LoadGlobal IR](https://github.com/python/mypy/pull/8973) merged
  - [[mypyc] Refactor name generation in Load/InitStatic](https://github.com/python/mypy/pull/8987) closed since #8973 handles it
  - [[mypyc] Translate more primitive ops to CallC](https://github.com/python/mypy/pull/9012) merged
  - [[mypyc] Translate more primitive ops to CallC](https://github.com/python/mypy/pull/9014) merged
  - [[mypyc] Merge int ops(for int_rprimitive) to CallC](https://github.com/python/mypy/pull/9019) merged


## Week 3: 2020-06-08 ~ 2020-06-14

This week we focused on representing `name_ref` with a new IR element since it's semantically different from what `CallC` represents. The `name_ref_op`s load global names, therefore we purposed [#8973](https://github.com/python/mypy/pull/8973) as the first attempt. Soon, we realized that `LoadStatic` and `LoadGlobal` have similar purposes and should be merged eventually. Jukka also mentioned about moving mypyc's name generation/mangling logic from codegen to irbuild. Name generation is a little bit messy now since it happens in plenty of processes.

After this refactoring, we should have a `LoadGlobal` IR that load a name without knowing all the name-related stuffs. The name generation logic for literal values is simple, while the one when groups and modules are involved is much more complicated. So it will be the focus of next week's first monthly meeting.

As a summary, this week we have the following issues and PRs:

- Issues:

  - [Refactor LoadStatic](https://github.com/mypyc/mypyc/issues/736) ongoing
  - [Refactor name generation logic](https://github.com/mypyc/mypyc/issues/737) ongoing

- PRs:
  - [[mypyc] Support var arg in CallC, replace new_dict_op](https://github.com/python/mypy/pull/8948): merged
  - [[mypyc] Add LoadGlobal IR](https://github.com/python/mypy/pull/8973) WIP
  - [[mypyc] Refactor name generation in Load/InitStatic](https://github.com/python/mypy/pull/8987) WIP

## Week 2: 2020-06-01 ~ 2020-06-07

This week we aim to introduce more types of ops via `CallC`. The remaining op variants are binary/unary/custom/name_ref ops. Supporting name_ref ops requires adding new IR so we postpone it to have more discussions. Binary and unary ops are easy and are supported via [#8929](https://github.com/python/mypy/pull/8929) and [#8933](https://github.com/python/mypy/pull/8933). The challenging part of this week is to support custom ops which are one-shot ops and are used very differently. We picked `new_dict_op` to demonstrate our implementation on custom ops.

`new_dict_op` has three problems to handle. Firstly, it uses a custom emit callback which exactly is we are replacing in this project, so the solution is to split the functionality of this emit callback and make it into two more concise `CallC`s and change the irbuild process accordingly. Secondly, the new `dict_build_op` calls a C function with variable arguments, so we support it in `CallC`. Finally, the `dict_build_op`'s C function also needs a integer argument. Mypyc has tagged integers and short integers but is lack of a low-level integer type, so we add a new `RType` to represent this.

As a summary, this week we have the following issues and PRs:

- Issues:
  - [Support top level function call via CallC](https://github.com/mypyc/mypyc/issues/732): closed
  - [Support more ops via CallC](https://github.com/mypyc/mypyc/issues/734): ongoing
  - [Introduce low level plain integer type](https://github.com/mypyc/mypyc/issues/735): closed
- PRs
  - [[mypyc] Support top level function ops via CallC](https://github.com/python/mypy/pull/8902): merged
  - [Support binary ops via CallC](https://github.com/python/mypy/pull/8929): merged
  - [Support unary op via CallC](https://github.com/python/mypy/pull/8933): merged
  - [[mypyc] Introduce low level integer type](https://github.com/python/mypy/pull/8955): merged
  - [[mypyc] Support var arg in CallC, replace new_dict_op](https://github.com/python/mypy/pull/8948): ready for review

In the coming week, we will focus on supporting name_ref ops.

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

Finally, as Jukka suggested. starting from #8902, I will open separate, fine-grained issues on mypyc's tracker to better track our progress.


## Week 0: 2020-05-17 ~ 2020-05-23

After discussions with both my mentors, we agree that I am familiar with the community now so we start the coding period a little bit early than the official date(June 1st) to buffer any unexpected delays.

On May 22nd I had the first daily sync with Jukka via Gitter. We discussed some implementation details about a new `CFunctionCall` to replace `PrimitiveOp` that simply calls a c function. To prevent unexpected change to codebase replies on mypyc, we changed our prototype op from frequently used `list.__len__` to `str.join`.

During weekends, I finished the PR demonstrated the `CFunctionCall` idea: [python/mypy#8880](https://github.com/python/mypy/pull/8880). The general idea is to have a `CFunctionCall` IR op to hold a C function name string and related argument values, which can be easily generated to corresponding C code. Therefore replaces codegen callback style used in `PrimitiveOp`. We still need some kind of low-level description `LLOpDescription` so we can match the AST during irbuild.

In the following week, I expect we'd work mainly around this PR, responding to code reviews and discuss the following questions:

- IR dump: I find that `str.join` dumps textual output exactly like `r2 = foo.join(r1)`, instead of `r2 = PyUnicode_Join(foo, r1)`. I assume overloading `to_str` method is the way to go but clearly something is missing here.
- Variants: I should inspect all the supported primitives so far and find out all the calling variants(void return, var_arg, etc), and change `CFunctionCall` accordingly.