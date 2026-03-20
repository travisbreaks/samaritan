# Anti-AI Policy Watch List

These projects ban or restrict AI-generated contributions. Check before submitting.

**Last updated**: 2026-03-19

## Outright Bans

| Project | Policy | Consequence |
|---------|--------|-------------|
| NetBSD | No AI-generated code | Rejection |
| QEMU | No AI-generated code | Rejection |
| Gentoo | No AI-generated code | Rejection |
| GNOME | No AI-generated code | Rejection |
| Ghostty | Zero tolerance | **Permanent ban** |
| tldraw | Auto-closes external PRs | Wasted effort |

## Mandatory Disclosure

| Project | Policy |
|---------|--------|
| MicroPython | Must disclose AI use |

## Best Practices

1. **Always search for "AI policy" or "LLM policy"** in the repo's CONTRIBUTING.md, CODE_OF_CONDUCT.md, or wiki before submitting
2. **Check recent issues/PRs** for discussions about AI contributions
3. **When in doubt, ask first** in a discussion or issue
4. **Co-Authored-By is not enough** for repos that ban AI. The tag is transparency, not a loophole.

## How to Check

```bash
# Search repo for AI/LLM policy mentions
gh api repos/OWNER/REPO/contents/CONTRIBUTING.md --jq '.content' | base64 -d | grep -i -E "ai|llm|copilot|chatgpt|generated"
```

## This List Is Incomplete

New policies are adopted regularly. The absence of a project from this list does not mean AI contributions are welcome. Always check.
