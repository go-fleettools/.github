# go-fleettools

[![License](https://img.shields.io/badge/license-BSD--3--Clause-blue.svg)](https://github.com/go-fleettools/.github/blob/main/LICENSE)
[![Pure Go](https://img.shields.io/badge/pure%20Go-CGO%3D0-00ADD8?logo=go&logoColor=white)](https://github.com/go-fleettools/fleettools)

**A fleet of 1800 repositories cannot be asked by hand, and every wrong answer it
gave once is a tool here.**

The first sweep for red default branches found 44 of 1819 — and **all 44** were stale
Renovate lanes whose last run predated a token rotation by three hours. Nothing was
broken. A red branch is not a broken one, and the question that distinguishes them is
*when did it last run?* That is `redscan`, and it is the shape of everything in this
organisation: a question somebody answered wrongly by eye, turned into a program that
cannot answer it that way.

## What is here

| Repo | |
|---|---|
| [`fleettools`](https://github.com/go-fleettools/fleettools) | Thirty commands, listed below. The sweeps that read every organisation, and the per-repository halves they pair with. |
| [`staletools`](https://github.com/go-fleettools/staletools) | Two ways an installed tool is not what you think: built with an older **toolchain** — an analyser built with an older Go does not fail, it misreports — and being an older **release** of itself. |
| [`mutate`](https://github.com/go-fleettools/mutate) | Prove that a test can fail: one exact source mutation, run the suite, restore whatever happens. A green suite over a mutation that survives is a suite that proves nothing. |
| [`prwait`](https://github.com/go-fleettools/prwait) | Wait for a pull request's checks, and **refuse to call an empty check list a pass**. A pull request with no merge ref runs no workflow and reads as green to any filter. |
| [`unbounded`](https://github.com/go-fleettools/unbounded) | Which repositories read a ZIP entry or decode an image **without a ceiling** — the sweep that follows one fix across its siblings. |
| [`fleetfind`](https://github.com/go-fleettools/fleetfind) | Which repository already does this: a live, **dated** inventory, because a hand-kept capability map drifts and a stale NO is worse than no answer. |
| [`landingkit`](https://github.com/go-fleettools/landingkit) | The go-\* landings' shared design, as a Hugo module, plus the command that derives a brand from a logo and checks a landing against the reference. |
| [`landingdiff`](https://github.com/go-fleettools/landingdiff) | Which of a set of landing layouts is the odd one out, by counting what each puts into the shared design system. |
| [`modfloor`](https://github.com/go-fleettools/modfloor) | The Go toolchain floor a module **declares**, against the floor a fix **requires**: read from the published `go.mod`, where a consumer reads it. |
| [`fuzzrun`](https://github.com/go-fleettools/fuzzrun) | Run every Go fuzz target of a module, in parallel within a load budget, and report crashers with the corpus file that reproduces them. |

## The thirty commands in `fleettools`

They are listed here because **nothing else can find them.** `fleetfind` answers
"which repository already does this" from a repository-level inventory, so a command
*inside* a multi-command repository is invisible to it: `fleetfind brief` and
`fleetfind "wasm size"` both return nothing across 2072 repositories, while
`fleetfind mutation` finds `mutate` immediately, because that one is a repository.
The repository holding the most tools is the one the inventory is blindest to.

Each line of [`fleettools/README.md`](https://github.com/go-fleettools/fleettools#readme)
says what its command proves and what it does not. This is the index, not a summary.

**Is the fleet green, and is "green" the right word?**
`redscan` (branches whose last run failed — and when) ·
`quietscan` (organisations whose Renovate runner **stopped**: a runner that stops does
not fail, it says nothing) ·
`prscan` (open pull requests, batched ~15 `org:` qualifiers per query) ·
`prmerge` (merges dependency PRs mergeable **and** fully green; never a Go toolchain
bump; reads `agentsync` leases and stays out of claimed repositories) ·
`relverify` (whether a tagged release is what its tag says, every file held to
SHA256SUMS)

**Is what is merged reachable, and is the version right?**
`tagscan` (modules whose branch is **ahead of its latest tag** — merged work `go get`
cannot reach) ·
`vscan` (what the next version should be) ·
`tagcells` (keeps the version a page states equal to the latest release) ·
`gorequires` (which of our modules require X, and at what version — the reverse
question)

**Does the CI actually test it?**
`testscan` (CI that runs `go test` on only PART of what has tests, or on nothing) ·
`judgescan` (tests that look for an external tool the CI **never installs** — a
foreign judge that has never run) ·
`covblocks` (the coverage blocks never entered, each named) ·
`buildscan` (which build **configurations** CI compiles, and what source none reach) ·
`buildtag` (ANDs a build constraint into Go files)

**Is it safe?**
`vulnscan` (which repositories **reach** a known vulnerability) ·
`vulngate` (the per-repository half) ·
`wfaudit` (workflows that let somebody else's pull request reach this repository's
secrets) ·
`blobpurge` (removes history blobs **by ID**, never by path, into a NEW repository,
and proves what the rewrite did)

**Is it advertised, and is the page what it builds?**
`docscan` (every repository an organisation has and does not advertise, and every one
it advertises and no longer has) ·
`pagescheck` (whether a Pages site serves what its repository builds — a green deploy
is not that) ·
`docsym` (identifiers a README names that the code does not have) ·
`i18ncheck` (where a translated content tree drifted from its source language)

**What does it cost, and on what?**
`wasmsize` (where a Go WebAssembly binary's bytes go, section by section) ·
`hostload` (how busy a host's CPUs are **now**, from two readings of `/proc/stat`) ·
`truncspill` (where Go 1.27's loong64 back end spills a conversion onto a slot it can
overwrite) ·
`gvlscan` (calls that block the OS thread without releasing the interpreter lock) ·
`gopin` (replaces `go-version: stable` with an explicit version)

**And the small ones, each because an idiom lied.**
`brief` — runs a command, shows the end of what it said, and **keeps its exit
status**, because `cmd | head` throws the status away and *no amount of knowing that
has stopped it*: a pipeline's status is its last command's, and `head` always
succeeds ·
`unarc` (extracts each archive into its own subdirectory, and never deletes the
archive) ·
`modrename` (gives a forked module its new path, where it is one and nowhere else)

## The rules every one of them is built on

- **A failed call must never become a verdict.** A sweep that could not read must say
  so; "0 found" from a request that errored is the worst possible output, because it
  is indistinguishable from good news.
- **Each says when it is not the code in this repository.** A reading taken from a
  modified tree prints that it is not reproducible from any commit.
- **The count and the completeness are separate lines.** How many were found, and how
  many could not be looked at, are different facts and are never added together.
- **A tool that misnames its own test gets corrected.** `staletools` said *behind* and
  compared with `!=`, so 35 of 52 "behind" were newer; `docscan` said *the org no
  longer has* about a fork the organisation owns. Both were found by reading the test
  against the sentence above it.

## Links

- Every command, with what it proves and what it does not: [`fleettools/README.md`](https://github.com/go-fleettools/fleettools#readme)
