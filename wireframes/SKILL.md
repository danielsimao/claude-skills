---
name: wireframes
description: >
  UX/UI exploration skill. Invoked via `/wireframes <idea>`. Guides the user
  through structured discovery, optional research, codebase-aware analysis, and
  generates distinct wireframe directions with complexity ratings and a final
  recommendation. Supports console and web output modes, with optional mobile +
  desktop dual-viewport output.
---

# `/wireframes` Skill

## Purpose

Transform a product idea into distinct, UX-driven wireframe directions through structured discovery, codebase-aware analysis, and production-quality design thinking. Think like a senior product designer who has read the codebase before the meeting.

---

## Invocation

```
/wireframes <idea>
```

If invoked without `<idea>` or the idea is too vague to proceed, ask the user to describe their product idea before continuing.

---

## Phase 0 — Codebase & Context Scan (always run first, silently)

Before asking the user anything, scan the project for design context. Do this silently — do not narrate the scan step by step. Summarise findings briefly when you present yourself to the user.

**Scope**: Scan from the nearest git root relative to the current working directory. If no git root is found, use the current working directory. If the scope is ambiguous (e.g. running from `$HOME` or a monorepo root with multiple apps), ask the user which project to scan before proceeding.

### What to scan

**1. CLAUDE.md (highest priority)**
- Read any `CLAUDE.md` or `.claude/CLAUDE.md` at the project root
- Extract: design system references, tech stack, naming conventions, Storybook/Figma links, explicit "use X not Y" rules, any UX or component guidance

**2. UI components**
Search the project for component directories and UI files using available file-search tools. Look for directories named `components`, `ui`, or similar, at any depth. List the top-level component names (file/folder names only — do not read implementation files unless needed). Note any obvious primitives: buttons, modals, drawers, cards, navigation, forms, etc.

If the project has no frontend UI layer (e.g. it is a backend service, CLI tool, or has no component files), skip this step and note: "No UI layer detected."

**3. Design tokens / theme files**
Search for Tailwind config, theme files, CSS custom property definitions, or token files. Extract: primary colors, font families, spacing scale, border radius conventions, dark/light mode support.

**4. Storybook**
Look for `.storybook/` or any `*.stories.*` files. If found, note that a Storybook exists — this means a design system is likely formalised.

### Output of scan

Produce a brief internal summary (shown once to the user before questions begin):

```
📦 Codebase scan complete
- CLAUDE.md: [found / not found] — [key insights if found]
- UI components: [locations and notable primitives, or "No UI layer detected"]
- Design tokens: [found / not found] — [primary colors, fonts if found]
- Storybook: [found / not found]
- Design system maturity: [None detected / Partial / Mature]
```

If nothing is found, state: "No UI components or design tokens detected. Will proceed with modern generic patterns." and continue.

---

## Phase 1 — Discovery (mandatory, never skip)

Greet the user and reference the codebase scan summary. Then ask discovery questions in a single batched message.

**Principle**: Infer what you can from the idea and the codebase scan before asking. Only ask what is genuinely unclear. A well-described idea with a scanned codebase may need only 3–4 questions. A vague idea on a greenfield repo may need 8–10.

**Weak answers**: If the user gives incomplete or vague discovery answers, state your assumptions explicitly and proceed. Do not ask repeated follow-up questions unless the missing information would materially change the concepts.

### Question areas (ask only what's unclear)

**Users & context**
- Who are the primary users? What is their technical level?
- What is the core job-to-be-done? What pain are they solving?

**Existing designs**
- Are there existing wireframes, mockups, or a live product this should react against or replace? (If yes, ask the user to share them — this shifts the session toward exploring alternatives. At least one concept should be an "evolutionary" refinement of what exists, not only clean-sheet redesigns.)

**Platform**
- What platform(s)? (web app, mobile app, PWA, desktop, embedded)
- Should the wireframes be mobile-first, desktop-first, or both mobile and desktop layouts for each concept?
  - If **both**: each wireframe will include a primary layout sketch plus adaptation notes for the secondary viewport
  - If **one**: proceed with that viewport only

**Core flows**
- What is the single most important user flow? (e.g. onboarding, search → result → action, creation flow)
- Are there secondary flows that must be considered?

**Constraints**
- Any hard technical constraints? (e.g. must use existing nav, can't add new routes, specific framework)
- Timeline or scope constraints that affect complexity?

**Design preferences**
- Any reference apps, competitors, or screenshots the user finds inspiring or wants to avoid?
- Any strong aesthetic preferences? (density, tone, formality)

**Existing patterns** (only ask if codebase scan found UI components)
- "I found [X, Y, Z] components in your codebase. Are there specific ones you want the wireframes to prioritise or deliberately avoid?"

**Research** (only include if the domain is unfamiliar or market context would be valuable)
- "Should I research competitors or UX patterns in this space before generating? (Only useful if the domain is unfamiliar or you want market context.)"

**Output** (always include at the end of the batch)
- "Do you want the wireframes delivered in the **console** (structured markdown) or on the **web** (interactive HTML page)?"

If the user requests research, complete it before proceeding to Phase 2. Summarise findings concisely (bullet points, not essays) and use them to inform wireframe differentiation.

---

## Phase 2 — Wireframe Generation

Aim for **3 concepts** by default. Go to 4–5 only when the problem space clearly supports that many meaningfully distinct directions. Each must differ in at least 3 of these dimensions:
- Information architecture
- Navigation model
- UX strategy (discovery-led, task-led, feed-led, wizard-led, etc.)
- Layout density (sparse / balanced / dense)
- Interaction patterns
- Onboarding / empty state approach

**If the problem only supports 1–2 genuinely distinct approaches, produce fewer and say why.** Prefer fewer deep concepts over more shallow ones. Never pad with superficial variants (layout density tweaks, color swaps, or minor rearrangements do not constitute a distinct direction).

Span the risk spectrum where the problem allows it. If the problem is straightforward enough that all reasonable approaches are conventional, say so and do not invent unconventional concepts for the sake of variety.

### Compression rules

- **Console mode**: Keep each concept to roughly one screenful (~40–60 lines). Do not repeat rationale already captured in tables.
- **Both modes**: If a section has nothing notable to say for a concept, omit it rather than writing filler.
- Prefer substance over completeness — skip a section rather than padding it.

### Per-wireframe structure

````
## Concept [N]: [Name]

**Core Bet**
One sentence: what UX bet this concept makes and who it serves best.

### Layout

ASCII sketch of the primary viewport. Show key regions, navigation placement, and content hierarchy. 10–20 lines.

If "both" viewports were requested, add a brief adaptation note (2–3 sentences) describing how the layout reflows for the secondary viewport. Only include a second ASCII sketch if the structural change is too significant for prose to convey.

### Primary Flow
Step-by-step description of the core user journey through this concept.

### Component Reuse (only if Phase 0 found UI components)
- ✅ Reuses: [list existing components found in the scan]
- 🔧 Extends: [existing components that need modification]
- 🆕 New: [components that don't exist and must be built]

Only name components actually found during the Phase 0 scan. Do not invent or assume components exist.

### Assessment

| Dimension | Rating | Rationale |
|---|---|---|
| UX Viability | High | How proven is this pattern for this use case |
| Market Fit | Medium | How aligned with user/market expectations |
| Implementation Complexity | Medium | Reference existing components where relevant |
| Design Complexity | Low | Reference existing tokens/patterns |
| Integration Risk | High | Touches routing, auth, existing state, etc. |

Ratings: **Low** / **Medium** / **High** / **Very High**

> Unconventional concepts will naturally rate lower on Market Fit — this reflects intentional risk, not a quality flaw.

### Pros
- [3–5 specific, honest pros]

### Cons
- [3–5 specific, honest cons — do not sugarcoat]
````

---

## Phase 3 — Comparison & Recommendation

### Comparison table

| | Concept 1 | Concept 2 | ... |
|---|---|---|---|
| UX Viability | | | |
| Market Fit | | | |
| Implementation Complexity | | | |
| Integration Risk | | | |
| Component reuse* | | | |
| Mobile adaptability** | | | |

*Include component reuse row only if Phase 0 found UI components.
**Include mobile adaptability row only if "both" viewports were requested.

### Final Recommendation (mandatory)

Always include a final recommendation. Never hedge into "it depends" without resolving it.

Structure:
1. **Primary recommendation**: Which concept and why — argue from the UX tradeoff that makes this concept the strongest choice for this specific user and context. Reference ratings and complexity only to support the argument, not as the argument itself.
2. **Runner-up**: Which concept is the strongest alternative and under what conditions it would win
3. **What to validate first**: The single biggest assumption the recommended concept makes that should be tested before committing
4. If "both" viewports: **Breakpoint note** — which concept adapts most gracefully across viewports and why

End with:

> "Which concept would you like to develop further? I can explore it in more detail, break it into an implementation plan, or prototype it."

---

## Output Mode: Console

Deliver everything as structured markdown in the terminal. Use clear section headers, ASCII layout sketches, and the table formats above. Follow the compression rules.

---

## Output Mode: Web

Load and follow the `frontend-design` skill fully before generating any code.

Write to `/tmp/wireframes.html` by default. If a `.wireframes/` directory exists in the project, write there instead. Only ask the user for a path if they explicitly requested a specific location.

**CRITICAL: Web wireframes are visual UI mockups, NOT ASCII art in styled code blocks.** Each concept must render as a realistic HTML/CSS mockup of the actual interface — buttons, inputs, cards, icons, layout — that looks like a real product screenshot. Use the project's design tokens (colors, fonts, spacing, border radius) from the Phase 0 scan. Do NOT use `<pre>`, monospace fonts, or ASCII art for layout wireframes.

### Page structure

Build a single-file HTML page containing:
- Header: product idea summary and key discovery findings
- Per-concept section with label, description, and **multiple state mockups** shown side by side
- If "both" viewports: a note per concept card describing the secondary viewport adaptation
- Assessment ratings rendered as colored badges (Low / Medium / High / Very High)
- Component reuse shown as a categorised tag list (✅ Reuse / 🔧 Extend / 🆕 New) — only if Phase 0 found UI components
- A comparison table section
- A final recommendation section with strong typographic emphasis

### Per-concept wireframes

- Render each UI state as a **realistic HTML/CSS mockup** of the product interface
- Show multiple states side by side (e.g. "empty" → "filled" → "submitted") to convey the interaction flow
- Label each state with a pill/badge (e.g. "Default", "Active", "Success")
- Use the project's actual component patterns: input fields, buttons, cards, selectors, navigation, etc.
- Match the product's visual language: dark/light theme, border radius, spacing, typography
- Icons can be Unicode symbols or simple CSS shapes — no external icon libraries required
- The mockups should be detailed enough that a developer can understand the layout, hierarchy, and interaction model without reading prose descriptions

### Design requirements

- Must not look generic. Commit to a clear aesthetic direction per the `frontend-design` skill.
- Use the project's actual colors and font conventions if design tokens were found in the scan
- If no tokens were found, use a distinctive but professional palette appropriate to the product domain
- Each concept should be immediately visually distinguishable from the others

---

## Ratings — Global Caveat

Include this note at the top of all output (console and web):

> *Ratings are qualitative judgments based on available information. They improve with thorough discovery answers, completed research, and a mature codebase. Treat them as directional signals for comparison between concepts, not objective measurements.*

---

## Graceful Degradation

| Situation | Behaviour |
|---|---|
| Ambiguous project scope (monorepo, `$HOME`) | Ask the user which project to scan before proceeding |
| User has existing designs | At least one concept must be an evolutionary refinement, not only clean-sheet redesigns |
| User gives weak or incomplete discovery answers | State assumptions explicitly at top of output and proceed. Do not ask repeated follow-ups. |
| Fewer than expected distinct directions | Generate only what's genuinely distinct. State why fewer were produced. |
