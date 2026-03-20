# The Samaritan Protocol

Eight operating principles for agent-assisted open source contribution.

These are not suggestions. They are rules, encoded in the agent's instruction file, enforced on every session. Each one exists because something broke without it.

---

## 1. Read CONTRIBUTING.md and any AI policy before every submission

Some repos ban AI contributions outright (NetBSD, QEMU, Gentoo, GNOME). Others require explicit disclosure (MicroPython). Ghostty will permanently ban you.

The agent checks for these policies before touching any repo. If in doubt, don't submit.

**Why this exists**: Submitting AI-assisted code to a repo that bans it damages both your reputation and the broader AI-assisted development community. One bad actor makes it harder for everyone.

## 2. Understand the codebase section before touching it

Not just the file. The architectural context. How does this component fit into the system? What are the project's conventions? What patterns do they use?

The agent reads surrounding code, checks recent PRs for style, and understands the dependency chain before proposing changes.

**Why this exists**: AI agents are excellent at local optimization and terrible at understanding architectural intent. A "fix" that violates the project's design philosophy will be rejected, and rightly so.

## 3. One repo at a time, go deep

Become a recognized contributor. Post reviews, answer discussions, iterate on feedback. Build a track record before submitting PRs.

The typical progression: reviews first (establish presence and demonstrate understanding), then issues (show you can identify real problems), then PRs (show you can solve them).

**Why this exists**: Maintainers have limited attention. A contributor who has reviewed 10 PRs and answered 3 discussions gets their PR reviewed faster than a stranger who shows up with a drive-by fix.

## 4. Co-Authored-By on every commit

```
Co-Authored-By: Claude Opus 4.6 (1M context) <noreply@anthropic.com>
```

Full transparency. The commit history shows exactly what was human-written and what was agent-assisted.

**Why this exists**: Trust is earned by transparency, not by hiding. If a maintainer asks "did you use AI for this?", the answer is already in the commit metadata.

## 5. Stay for the review

Iterate on feedback within 24 hours. If a reviewer asks for changes, make them promptly. If they ask questions, answer thoroughly. Don't submit and disappear.

**Why this exists**: A PR that gets review feedback and goes silent wastes the reviewer's time. The review is the valuable part, not the submission.

## 6. After merge, stay engaged with the project

Check for follow-up opportunities. Did the merge create new issues? Are there related improvements? Does the documentation need updating?

**Why this exists**: One-and-done contributions are fine, but ongoing engagement is what builds contributor reputation. The second PR from a known contributor gets reviewed in hours, not weeks.

## 7. Never spray PRs

Targeted, high-value contributions only. One well-researched PR is worth more than ten superficial ones.

Before submitting, ask:
- Does this fix a real problem or just a theoretical one?
- Has anyone else already solved this?
- Will the maintainer actually want this change?
- Is this the right scope (not too big, not too small)?

**Why this exists**: PR spam is the fastest way to get blocked from a project. Maintainers talk to each other. Your reputation follows you across the ecosystem.

## 8. If in doubt about value, don't submit

When the answer to "should I submit this?" is "maybe," the answer is no.

Post a review instead. Answer a discussion. File a well-researched issue. Build context. The PR opportunity will come.

**Why this exists**: The cost of a rejected PR is not just the wasted effort. It is the maintainer's time spent reviewing something that should not have been submitted. Respect their time by only submitting work you are confident about.
