# Contributing

This repository publishes measurements and claims that other people are invited
to check. The rules below exist because we broke them, not because they sounded
good in the abstract.

## Claims about other people's projects

**A claim about someone else's project is quoted verbatim from their own
source, or it is not made.**

Not paraphrased, not summarised, not recalled, and not taken from a search
result or a tool that rendered the page for you. Open the source, copy the
words, cite where they came from. If the source does not say the thing, the
claim does not get made — the absence is itself a finding worth reporting.

This matters most where it is most tempting to be loose: our credibility rests
on being precise about the limitations of our dependencies, so an imprecise
statement about a dependency costs more than an imprecise statement about our
own work.

Three failures that produced this rule:

- We wrote that `ml-dsa` "states it has never been independently audited; so
  does `fips204`." The first half is verbatim from `ml-dsa`'s README. **The
  second half was never said by `fips204`** — it says the standard and the
  software "should be considered experimental — USE AT YOUR OWN RISK!", which
  is a different statement. We asserted an audit claim on another project's
  behalf, in a document whose whole argument is that we are careful about audit
  status.
- We paraphrased SDF's Quantum Preparedness Plan as naming an "enterprise
  custody tier" for 2026. Its actual words: "enterprise wallets can migrate to
  quantum-safe contract accounts in 2026."
- A draft email quoted an organisation's own web page back to them using wording
  that came from a page summariser rather than the page. Caught only by
  re-opening the page before sending. **A tool that renders a page for you is
  not the page.**

Practical form: if a claim rests on someone else's words, the quoted string
should be greppable in what that source actually serves.

## Figures

- Every published figure carries the date and the protocol version it was
  measured on. Cost models change: testnet moved from protocol 27 to 28 on
  27 August 2026 and every absolute instruction count shifted.
- When a figure is superseded, say so where the old figure lives rather than
  silently replacing it, and grep for the *number* rather than trusting your
  memory of which files contain it.
- Two measurements are comparable only if taken the same way. A `stellar` CLI
  declared-resource figure includes a simulation safety margin; a raw
  `simulateTransaction` cost does not. Differencing one against the other
  produces a number that means nothing.

## Git discipline

**Do not use `git stash` to move work between branches.** Use a WIP commit or
`git worktree add`. Commits are addressable, appear in `git log`, and survive.

Two distinct hazards, both real, both encountered here:

**1. The partially empty commit.** Git *does* refuse a completely empty commit
and exits non-zero, so `git commit && git push` short-circuits on its own — that
case needs nothing from us. What is **not** guarded is the partial case: some
edits in the working tree, the rest parked in a stash, a commit that succeeds,
and a push that quietly ships an incomplete change.

**2. `git stash -u` eats untracked files, and `git stash drop` destroys them.**
The `-u` flag sweeps up new files that have never been committed. `drop` then
deletes that stash with no confirmation and no undo short of `git fsck`. This
file was written, swallowed by `git stash -u`, and destroyed by `git stash drop`
within a single command — while documenting the hazard. Commit new files
*before* running anything that touches the stash.

So the check that actually works is to **assert content, not git state**: before
pushing, grep the file for the string you intended to add. A clean `git status`
tells you the tree matches HEAD, not that HEAD contains your work.

A `pre-push` hook in `.githooks/` refuses to push while a stash exists. Enable
it once per clone:

```
git config core.hooksPath .githooks
```

Override a push you have deliberately decided is fine: `git push --no-verify`.
