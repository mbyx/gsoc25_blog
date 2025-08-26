# GSoC 2025 Final Report

Contributor: [mbyx (Abdul Muiz)](https://github.com/mbyx)  
Mentor: [tgross35 (Trevor Gross)](https://github.com/tgross35)  
Project: [Modernizing the `libc` Crate](https://summerofcode.withgoogle.com/programs/2025/projects/r3LkZkOy)  
Organization: [The Rust Foundation](https://www.rust-lang.org/)

Blog that goes into more detail: [GSoC 25 Blog](https://mbyx.github.io/gsoc25_blog/)

## Overview
`libc` is a vital Rust crate that aims to act as a single source of truth for interfacing with the C library of the same name. During Google Summer of Code (GSoC), I worked on modernizing the `libc` crate in many ways, the most significant of which are:
1. Rewrite the testing infrastructure that uses old libraries to a more modern equivalent to reduce failures and problems maintaining it, as well as documenting the tests properly.
2. Port all testing platforms to use this new testing infrastructure, ensuring that they work the same, and in some cases better than before.
3. Try to fix as many smaller issues that plague `libc` so that it can finally have it's first major version `libc 1.0.0` released.
4. Make the testing infrastructure easier to maintain, as well as adding nice to have features for convenience.

All of these were the main goals outlined in my project proposal, with the exception of one, which I was unable to do (namely helping improve the CI, which I was unfortunately not experienced enough in to understand).

As a result of these contributions, libc is finally on its way to `1.0.0`, and has released an alpha version as a result. There's still a long road ahead to the version change, however. Additionally, all grievances caused by the old testing infrastructure, namely inexplicably failing on a file that hasn't even been touched in some PRs, and the failing of an entire platform in tests so consistently that it became the norm to ignore them, were fixed.

More detail on each topic is below:

## Rewriting the testing infrastructure

Related PRs:
- [Unrelated PR to dip my toes in](https://github.com/rust-lang/libc/pull/4296)
- [Stub the project folder and deps](https://github.com/rust-lang/libc/pull/4467)
- [Add extraction of items and translation from Rust to C](https://github.com/rust-lang/libc/pull/4477 (unmerged, was split))
    - [Add macro expansion](https://github.com/rust-lang/libc/pull/4484)
    - [Add extraction of items](https://github.com/rust-lang/libc/pull/4485)
    - [Add translation from Rust to C](https://github.com/rust-lang/libc/pull/4501)
    - [Add skips and mappings](https://github.com/rust-lang/libc/pull/4514)
- [Add more tests](https://github.com/rust-lang/libc/pull/4543 (unmerged, was split))
    - [Clean up test template](https://github.com/rust-lang/libc/pull/4551)
    - [frontend](https://github.com/rust-lang/libc/pull/4555)
    - [Add tests for size, alignment, and signededness](https://github.com/rust-lang/libc/pull/4556)
    - [Add tests for field size, offset, and pointers](https://github.com/rust-lang/libc/pull/4561)
    - [Add roundtrip test](https://github.com/rust-lang/libc/pull/4560)
- [Port ctest-test](https://github.com/rust-lang/libc/pull/4558)
- [Adding foreign function tests](https://github.com/rust-lang/libc/pull/4594)
- [Adding foreign static tests](https://github.com/rust-lang/libc/pull/4601)
- [bug fixes batch 1](https://github.com/rust-lang/libc/pull/4613)
- [Improving the translation backend](https://github.com/rust-lang/libc/pull/4617)
- [bug fixes batch 2](https://github.com/rust-lang/libc/pull/4644)
- [Promoting ctest-next to be the new ctest](https://github.com/rust-lang/libc/pull/4655)
- [Adding support for C enums (unmerged)](https://github.com/rust-lang/libc/pull/4658)
- [Expand macros using the correct target](https://github.com/rust-lang/libc/pull/4674)

Definitely the most interesting part about this entire project is the automated testing done by libc. Because it must exactly follow the libc API in every way for every supported platform (with every platform having small differences), a large amount of tests have to be performed for each type. The main gist of how it works is that a Rust test is generated that compares the properties of the rust version of every type with statically linked library files of the C version of those tests. So you would have a `ctest_size__TYPE` function in C that gets called in Rust, and checks if for example the size of the type is the same across both. This is tested natively on every platform so that differences are caught.

Some of the more interesting tests include round trip tests, where we send data from Rust to C, and then from C to Rust, validating that the data does not change in any way either time. Other than that we have basic tests to check if both sides point to the same function or static variable, that constants have the same value, or that structs and unions have the correct fields, padding, alignment, etc.

My role was to port the testing infrastructure to use a more modern library for parsing Rust syntax, namely `syn` instead of the much older `gerando_syntax`, which couldn't parse newer syntax at all and failed inexplicably. The tests themselves were also rewritten so that they used more modern Rust and were simplified to be easier to maintain.

To me, the most fun part was definitely trying to generate C types from Rust types, which used to be done via string manipulation, until I replaced it with a bit more robust string manipulation until my mentor created an intermediate representation for C types that I used instead of the string manipulation.

It definitely is awe inspiring when an 80k line of code test file is generated and validates everything written.

## Porting testing platforms to the new infrastructure

Related PRs:
- [Port windows to use ctest-next](https://github.com/rust-lang/libc/pull/4600)
- [Port apple to use ctest-next](https://github.com/rust-lang/libc/pull/4610)
- [Port freebsd to use ctest-next](https://github.com/rust-lang/libc/pull/4648)
- [Switch linux to ctest-next](https://github.com/rust-lang/libc/pull/4647)
- [Switch tier 3 targets to the new ctest](https://github.com/rust-lang/libc/pull/4670)
- [Switch tier 2 targets to the new ctest](https://github.com/rust-lang/libc/pull/4675)

A bit more tedious part was porting all the platforms to use the new infrastructure, because of the improvements to the API, instead of passing strings to the end user we were actually passing proper items that represented the things we were skipping or renaming. As of writing, two of three tier 1 targets have been ported and merged (apple and windows), with linux being ported but unmerged due to a bug that still needs to be fixed. Tier 3 targets have been mostly ported, with a large amount of tier 2 targets left (again blocked due to some bugs that need to be fixed). By 1st September these should mostly be done.

## Fixing smaller issues

Related PRs:
- [Wrap padding fields with `Padding` type (unmerged)](https://github.com/rust-lang/libc/pull/4609)
- [Remove enum uses](https://github.com/rust-lang/libc/pull/4575)
- [Make structs non exhaustive by default (draft)](https://github.com/rust-lang/libc/pull/4579)
- [Adding a new Padding type](https://github.com/rust-lang/libc/pull/4632)
- [Lint error](https://github.com/rust-lang/libc/pull/4548)
- [Publish ctest to crates.io](https://github.com/rust-lang/libc/pull/4661)

Things such as replacing all Rust enums with the `c_enum` macro, adding a Padding newtype, etc were handled. I wasn't able to do as much as I would have liked to, mostly due to the old ctest bugging out a lot in my PRs. One thing I did notice was that quite a lot of issues are still open that are actually closed.

## Nice to haves, documentation, maintenence

Related PRs:
- [Add defines for specific headers and language selection](https://github.com/rust-lang/libc/pull/4673)
- [Improve documentation and remove unused items](https://github.com/rust-lang/libc/pull/4665)


Documentation of the tests was improved, as well as simplifying the tests so that they could be easily maintained. The ctest API was documented properly, as well as the correct way to work on the new ctest so that its integration tests don't fail. By far the best thing done was structuring of the rewrite so that every function was completely understandable even without documentation. Going from `ty2name` and `rust_ty_to_c_ty` to `translate_type` and `translate_primitive_type` for example. Thankfully the original ctest was quite small because the names of the functions were very confusing.

## Future work
In terms of what's left, there's quite a bit! There's a lot of small issues and API changes that would be preferable to perform before `1.0.0` lands, as well as the swathes of tests that have to be skipped for each platform tested, due to one reason or the other. Some of these are understandable due to version differences, so it would be better if it was easier to distinguish between them.

While the platforms have been ported to the new testing infrasturcture, the layout is basically still the same, so all the skips and renames are in one place, not organized very well by what is permanent, temporary, etc.

Secondly it would be quite nice if debugging problems in the tests was easier. Right now you have to dig through multiple folders to find the generated test file, and errors aren't formatted nicely, and aren't very descriptive.

## Conclusion

Working on an open-source project, and a large project at that, has been an incredible experience. Not only did it gave me a sense of accomplishment, it also made me realize that my coding skills are actually half decent. The discussions, the welcoming community, and of course the support of my mentor were a valuable asset during this journey, as my code was torn to shreds (in a good way) in code reviews, and then slowly but surely becoming better.

It was also quite humbling. I got to experience how a real developer [debugged a problem](https://github.com/rust-lang/libc/pull/4648#issuecomment-3193970040), digging through assembly, and even figuring out the offending lines of code, just from a segmentation fault.

We really are standing on the shoulders of giants.