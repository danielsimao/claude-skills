---
name: writing-github-issues
description: Use when drafting or rewriting a GitHub issue body — new issues, or cleaning up existing ones with scattered context. Enforces Daniel's 6-section structure and an interactive structure-first review workflow.
---

# Writing GitHub Issues

## Overview

Daniel prefers GitHub issue bodies in a fixed 6-section structure. Follow it exactly. Do not invent sections, do not merge sections, do not add an "Implementation notes" section — implementation detail belongs in the PR, not the issue.

## Workflow (structure-first review)

**Always a two-step handshake — never draft the full body in one shot:**

1. **Propose the structure first.** Show the 6 section headings with one-line descriptions of what will go in each for this specific issue. Wait for approval or tweaks.
2. **Draft the full body** only after the structure is confirmed. Show it in chat — do NOT call `gh issue edit` or `gh issue create` until Daniel explicitly says "push" / "submit" / "1" / equivalent.

Skipping the handshake is a violation. If the user asks you to "write an issue" with no other context, your first reply is the structure proposal, not the body.

## The 6 sections (exact names, exact order)

```markdown
## Summary

One sentence. What's changing. No motivation, no scope — save that for later sections.

## Why

2–3 lines. Motivation, stakeholder ask, link to external requirements doc (Notion / Linear / Figma).

## Scope

**Applies to:** <explicit list of subtypes / components / modules in scope>.

**Explicitly untouched:** <explicit list of things NOT changing — call out reversals and non-goals here>.

## Requirements

Grouped bullets, one line each where possible. Sub-bullets only when a requirement has multiple components. NO "Implementation notes" sub-section — technical file paths and architectural decisions belong in the PR description, not the issue.

- **<Requirement category>** → <what changes, imperative voice>.
- **<Requirement category>** → <what changes>.

## Acceptance criteria

Checkbox list. One verifiable outcome per line. Use past-tense/state language a reviewer can tick off.

- [ ] <Observable outcome>
- [ ] <Observable outcome>

## Links

Emoji-prefixed, labelled, with the category in bold:

- 📐 **Wireframes / design:** [label](url)
- 📄 **Requirements doc:** [label](url)
- 🔗 **Related PR / issue:** #1234
```

## Hard rules

- **Never add an "Implementation notes" or "Technical approach" section.** File paths, layering decisions, refactor steps → PR description.
- **Summary and Why are separate.** Don't merge them.
- **Requirements is separate from Scope.** Scope = what's in/out. Requirements = the actual spec.
- **Scope uses inline `Applies to` / `Explicitly untouched` bold labels**, NOT `### In scope` / `### Out of scope` subheaders.
- **Acceptance criteria, not "Definition of done".** Checkboxes, not prose.
- **Links use emoji + bold category label**, not a plain bullet list.
- **No raw screenshots in the body.** Link to them from the Links section or leave them in comments.

## Common mistakes

| Mistake | Fix |
|---|---|
| Drafting the full body before showing structure | Stop. Show the 6 headings with one-line descriptions. Wait. |
| Adding an "Implementation notes" section | Delete it. That content goes in the PR. |
| Merging Why into Summary | Split. Summary = what. Why = motivation. |
| Using "Definition of done" | Rename to "Acceptance criteria". |
| Using `### In scope` subheaders | Use inline `**Applies to:**` / `**Explicitly untouched:**` on two lines. |
| Pushing to `gh issue edit` before explicit approval | Don't. Draft → show → wait for "push". |

## Length target

~40–60 lines rendered. If you're over 80, the Requirements section is probably leaking into implementation detail — trim it.
