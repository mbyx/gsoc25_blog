# GSoC 2024 Final Report

Contributor: [mbyx (Abdul Muiz)](https://github.com/mbyx)  
Mentor: [tgross35 (Trevor Gross)](https://github.com/tgross35)  
Project: [Modernizing the `libc` Crate (NOT YET LINKED)]()  
Organization: [The Rust Foundation](https://www.rust-lang.org/)

## Overview
`libc` is a vital Rust crate that aims to act as a single source of truth for interfacing with the C library of the same name. During Google Summer of Code (GSoC), I worked on modernizing the `libc` crate in many ways, the most significant of which are:
1. Rewrite the testing infrastructure that uses old libraries to a more modern equivalent to reduce failures and problems maintaining it, as well as documenting the tests properly.
2. Port all testing platforms to use this new testing infrastructure, ensuring that they work the same, and in some cases better than before.
3. Try to fix as many smaller issues that plague `libc` so that it can finally have it's first major version `libc 1.0.0` released.
4. Make the testing infrastructure easier to maintain, as well as adding nice to have features for convenience.

All of these were the main goals outlined in my project proposal, with the exception of one, which I was unable to do (namely helping improve the CI, for which I was unfortunately not experienced in).

As a result of these contributions, libc is finally on its way to `1.0.0`, and has released an alpha version as a result. There's still a long road ahead to the version change, however. Additionally, all grievances caused by the old testing infrastructure, namely inexplicably failing on a file that hasn't even been touched in some PRs, and the failing of an entire platform in tests so consistently that it became the norm to ignore them, were fixed.

More detail on each topic is below:

## Rewriting the testing infrastructure
## Porting testing platforms to the new infrastructure
## Fixing smaller issues
## Nice to haves, documentation, maintenence


For now, just collecting all my PRs, merged and closed here:
- Unrelated PR to dip my toes in: https://github.com/rust-lang/libc/pull/4296
- Stub the project folder and deps: https://github.com/rust-lang/libc/pull/4467
- Add extraction of items and translation from Rust to C: https://github.com/rust-lang/libc/pull/4477 (unmerged, was split)
    - Add macro expansion: https://github.com/rust-lang/libc/pull/4484
    - Add extraction of items: https://github.com/rust-lang/libc/pull/4485
    - Add translation from Rust to C: https://github.com/rust-lang/libc/pull/4501
    - Add skips and mappings: https://github.com/rust-lang/libc/pull/4514
- Add more tests: https://github.com/rust-lang/libc/pull/4543 (unmerged, was split)
    - Clean up test template: https://github.com/rust-lang/libc/pull/4551
    - frontend: https://github.com/rust-lang/libc/pull/4555
    - Add tests for size, alignment, and signededness: https://github.com/rust-lang/libc/pull/4556
    - Add tests for field size, offset, and pointers: https://github.com/rust-lang/libc/pull/4561
    - Add roundtrip test: https://github.com/rust-lang/libc/pull/4560
- https://github.com/rust-lang/libc/pull/4579 (draft)
- Port ctest-test: https://github.com/rust-lang/libc/pull/4558
- Lint error: https://github.com/rust-lang/libc/pull/4548
- Remove enum uses: https://github.com/rust-lang/libc/pull/4575
- [Adding foreign function tests](https://github.com/rust-lang/libc/pull/4594)
- [Adding foreign static tests](https://github.com/rust-lang/libc/pull/4601)
- [Port windows to use ctest-next](https://github.com/rust-lang/libc/pull/4600)
- [bug fixes batch 1](https://github.com/rust-lang/libc/pull/4644)
- [Wrap padding fields with `Padding` type (unmerged)](https://github.com/rust-lang/libc/pull/4609)
- [Improving the translation backend](https://github.com/rust-lang/libc/pull/4617)

Week 9:
- [Port apple to use ctest-next](https://github.com/rust-lang/libc/pull/4610)
- [Port freebsd to use ctest-next](https://github.com/rust-lang/libc/pull/4648)
- [bug fixes batch 2](https://github.com/rust-lang/libc/pull/4613)
- [Adding a new Padding type](https://github.com/rust-lang/libc/pull/4632)
- [Promoting ctest-next to be the new ctest](https://github.com/rust-lang/libc/pull/4655)
- [Adding support for C enums (unmerged)](https://github.com/rust-lang/libc/pull/4658)
- [Port linux to use ctest-next](https://github.com/rust-lang/libc/pull/4647)

## Future work
In terms of what's left, there's quite a bit! There's a lot of small issues and API changes that would be preferable to perform before `1.0.0` lands, as well as the swathes of tests that have to be skipped for each platform tested, due to one reason or the other. Some of these are understandable due to version differences, so it would be better if it was easier to distinguish between them.

While the platforms have been ported to the new testing infrasturcture, the layout is basically still the same, so all the skips and renames are in one place, not organized very well by what is permanent, temporary, etc.

Secondly it would be quite nice if debugging problems in the tests was easier. Right now you have to dig through multiple folders to find the generated test file, and errors aren't formatted nicely, and aren't very descriptive.

## Conclusion

Working on an open-source project, and a large project at that, has been an incredible experience. Not only did it gave me a sense of accomplishment, it also made me realize that my coding skills are actually half decent. The discussions, the welcoming community, and of course the support of my mentor were a valuable asset during this journey, as my code was torn to shreds (in a good way) in code reviews, and then slowly but surely becoming better.

It was also quite humbling. I got to experience how a real developer [debugged a problem](https://github.com/rust-lang/libc/pull/4648#issuecomment-3193970040), digging through assembly, and even figuring out the offending lines of code, just from a segmentation fault.

We really are standing on the shoulders of giants.