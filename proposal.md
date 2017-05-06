# Proposal for the Haskell Summer of Code 2017

## Personal info

**Name**: Francesco Gazzetta

**Contact**: see [fgaz.me/about](http://fgaz.me/about)

## Project info

### Project title

Last Mile for `cabal new-build`

### What is the goal of your project? 

Cabal new-build solved lots of the problems of Haskell development, but its
feature set isn't comparable to the "classic" cabal one yet.

This project aims to complete what was started in the last years and bring
the new-build project to a state where it can finally replace the old interface.

### Can you give some more detailed design of what precisely you intend to achieve?
This is the "main body" of your application -- it can be anywhere from a few
paragraphs to two or three pages. More is not necessarily better.

The core functionality of new-build already works well, but the list of
[issues tagged nix-local-build](https://github.com/haskell/cabal/issues?q=is%3Aopen+is%3Aissue+label%3A%22cabal-install%3A+nix-local-build%22)
is still long.

I clearly do not aim to resolve all of them.

The main goal is to bring new-build on par feature-wise with cabal "classic"
and fix blocking bugs, such as failing asserts.
This is tracked in the
"[Default nix-local-build (3.0)](https://github.com/haskell/cabal/milestone/42)"
milestone.

#### Missing features

**new-run**:

This is almost complete, except... it doesn't run the generated executable.

I'll pass the target info up until CmdRun.hs/runAction and finally run it.

**new-haddock**:

Also almost complete, but doesn't generate hackage-specific docs.

There's already a pr about this, I'll help investigate about the
failing test.

**Info**:

Most of the current info command is simply dumping the .cabal file
plus checking the installed/cached packages.

This has to be modified taking into account the packages in the store,
which are currently not displayed.

Adding some info about the paths is a bonus.


**Global install / package binary**:

This is the top priority after the standard features have been implemented.

`new-build` deprecates `install`, but in doing so removes some functionality,
namely the global install/prefix/copy option.

* the priority is installing programs
  * later, I can try with libraries too, but the use cases are limited
* libraries' data files have to be taken into account
  * recompile them with the new `Paths_*` if needed
* enable both static and dynamic linking
  * the latter is more difficult, as also all the libraries have to be correctly
    built and packaged


#### Bugs

There are a few bugs too in the milestone. Each hone has to be investigated,
fixed (some can be skipped, since they are more
[difficult](https://github.com/haskell/cabal/issues/4126) to
[reproduce](https://github.com/haskell/cabal/issues/3907),
until more info is available),
and where possible a test case has to be added.

#### QOL improvements

**Non-hackage dependencies**:

This is probably the most useful optional feature

* git-archive does not always work, so we have to fetch the entire repo
  (maybe a shallow clone if only HEAD is needed?) and checkout the relevant tree
  * this is already at least partially implemented in some other parts of Cabal
  * this is an expensive operation: make sure it's done only when necessary
* the downloaded packages have to be placed in the local store, not the global one
  * unless we use a unique hash of the source and lock it from the user
* chicken-and-egg .cabal problem
  * new-fetch should download source repos too
    * should be automatically invoked by new-build as necessary
* take into account cabal freeze
  * should it freeze the commit hash too?
* mandating the use of tags is an option, as it would greatly reduce
  the complexity

**New-link / new-install**:

Symlink the built programs in .cabal/bin, so they are in the PATH.

Take into account the fact that multiple version can coexist,
and give the possibility to choose which one to run.

**Output improvements**

Colors, a better visualization for parallel builds
(one line per job, displaying what package is being built),
a better help message...

This is not related to new-build, but if I'll have spare time after
solving all the above issues (unlikely, given Hofstadter's law...)
I'll give it a shot.

### Do you have a mentor in mind?
If so, whom? Have you already spoken with them about this?

The ideas page mentions "someone at Galois".
I did not contact them yet.

### Please provide a desired timeline of your work.
Can you outline an approximate schedule of milestones?

**June, first half**
Community bonding period

Take advantage of this time to gain familiarity with
the new-build codebase while solving some of the
simpler issues like
[#4412](https://github.com/haskell/cabal/issues/4412).

Very light work, since I have some exams during this time.

**June, second half**

Work on the bigger and most important features, I expect
to complete the standard commands.

Request more information on some of the issues.

**July, first half**

Again, some exams, but nothing too heavy.

Investigate and fix the most important issues.

Plan the architecture of the new features
(non-hackage deps, link, and install/package if not done already...)

Prepare for the evaluation, write a summary of the progress.

**July, second half**

I may take a short vacation, not completely offline though,
so there will be some space for discussion and light work.

Work on the remaining features.

I should complete at least the install/package and fix the reported bugs.
Possibly the QOL improvements, particularly the non-hackage deps.

**August, first half**

I'll be at the scout camps, so I'll be completely unavailable and
disconnected from the net during this time.

**August, second half**

Complete the ongoing features, polish, fix and document the code.

The last week is left as a buffer for eventual delays.

**September, first days**

Write the final announcement post.

### In what ways will this project benefit the wider Haskell community?

`new-build` is a long-awaited feature in cabal, and will positively
affect --directly or indirectly-- almost all Haskellers, as cabal
is used in almost every Haskell developing stack.

The impact for newcomers will be great too, as this will remove
completely any remaining trace of the so-called "cabal hell".

### What relevant experience do you have?
e.g. Have you coded anything in Haskell? Have you contributed to any
other open source software? Been studying advanced courses in a related topic?

I started to learn Haskell about three years ago,
and I followed the community (even if more as a lurker) since then,
witnessing the AMP, the FTP, the rise of stack and the creation of
new-build...

In the meantime, i continued to work on personal Haskell
[projects](http://fgaz.me/projects/),
the last one being
[polyvariadic](https://hackage.haskell.org/polyvariadic),
and made little contributions to others where I could.

I didn't have much experience with dev-side cabal until now,
but I recently started to study its code, and I'm working on
[some contributions](https://github.com/haskell/cabal/pulls?q=is%3Apr%20author%3Afgaz)
(some are local and not shown there).

### How does the project benefit you?
This is optional, but gives us a better understanding of how you will stay motivated.

Apart from the learning experience, this project has a very practical benefit for me too.
Like all Haskellers, I use cabal heavily too, and I switched to `new-build` shortly after
it came out.
I'd like for it to replace the current `build` as soon as possible, so
I can use it completely (instead of a mixture of the two).

### In what ways do you envisage interacting with the wider Haskell community during your project?
e.g. How would you seek help on something your mentor wasn't able to deal with? How will you
get others interested in what you are doing?

In the last years I mainly interacted directly with the
[italian Haskell community](http://haskell-ita.it/),
while lurking a lot in the international one,
so I know where things happen but others don't know me.

For example, I know that the IRC is very active and
helpful towards small questions and opinions.

For more visibility, and for bigger questions and announcements,
there is the mailing list and the (also very active) subreddit.
[TODO is all of this necessary?]

The maintainers at haskell/cabal are particularly responsive
to PRs and questions, so a cabal-specific question would go
at them.

I plan on keeping a public weekly journal in which I'll log
my progress and my plans for the next weeks.
This will inform the community of my progress and force myself
to evaluate my progress.

Finally, once the work is completed I'll write a post describing the changes
and pointing any future contributor in the right direction.

### Other comments / concerns?

This proposal is available in markdown format
[here](http://github.com/fgaz/hsoc2017/blob/master/proposal-cabal.md).

## Optional

### Gender

### Country of residence

### Other information you would like to share with us

