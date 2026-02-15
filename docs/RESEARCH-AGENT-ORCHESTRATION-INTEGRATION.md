# Research Agent + Context Orchestration Integration Guide

*Implementation guide for combining context orchestration (pain-points methodology) with the research-agent (last30days) fork. Apply these changes in the `jvgitty/research-agent` repository.*

---

## Overview

This document provides everything needed to add a **Pain Points** mode to research-agent. The integration:

1. Adds `variants/pain-points/` with the 5-file context cascade
2. Routes on `last30 pain-points [demographic]` or `last30 pain [demographic]`
3. Loads cascade, substitutes variables, runs research, synthesizes per output-spec

**No changes to prompt-orchestration.** Only addition: this document.

---

## Step-by-Step Implementation Checklist

- [ ] **Step 1:** Create directory `variants/pain-points/` in research-agent
- [ ] **Step 2:** Add the 5 cascade files (copy from sections below)
- [ ] **Step 3:** Add `variants/pain-points/SKILL.md` (copy from section below)
- [ ] **Step 4:** Modify main `SKILL.md` to route `pain-points` / `pain` to the pain-points variant, or document how to enable it (e.g. `cp variants/pain-points/SKILL.md .` for pain-points-only install)
- [ ] **Step 5:** Test with `last30 pain-points SMB founders in B2B SaaS`

---

## Part 1: Cascade Files (Ready to Paste)

Create each file in `variants/pain-points/` with the content below.

### File 1: `target-demographic.md`

```markdown
# Target Demographic

*Who we're researching. Defines the lens for pain point discovery.*

---

## Core Definition

| Field | Value / Description |
|-------|---------------------|
| **Industry** | {{INDUSTRY}} |
| **Role / Title** | {{ROLE}} |
| **Company Size** | {{COMPANY_SIZE}} |
| **Market Niche** | {{MARKET_NICHE}} |

---

## Psychographics

- **Key motivations:** [What drives them?]
- **Common frustrations:** [Surface-level frustrations we already know]
- **Where they seek solutions:** [Communities, platforms, vendors they trust]
- **Language they use:** [How do they describe their problems? Vocabulary, slang]

---

## Where They Show Up

| Platform / Channel | What to look for |
|--------------------|------------------|
| LinkedIn | Posts, comments, job descriptions, group discussions |
| Reddit | Subreddits, threads, complaints, "does anyone else" |
| Twitter/X | Tweets, replies, quote tweets, hashtags |
| Industry forums | Support threads, feature requests |
| Review sites (G2, Capterra, etc.) | What they praise, what they complain about |
| Support tickets / NPS | (If available) Raw feedback |
| Communities / Slack/Discord | (If available) Real conversations |

---

## Exclusions

- Who we're *not* targeting: [e.g. enterprise vs SMB, technical vs non-technical]
- Out-of-scope pain points: [e.g. pricing only, implementation only]
```

---

### File 2: `pain-point-criteria.md`

```markdown
# Pain Point Criteria

*Definitions of latent and powerful. Prioritization framework.*

---

## Latent Pain Points

**Definition:** Pain points that are *not* obvious — they're understated, emerging, or hard for people to articulate. They show up in:
- Off-hand comments
- Workarounds and hacks they've built
- "It's just the way it is" resignation
- Frustration that surfaces when something breaks, not in surveys
- Language like "I wish", "if only", "nobody gets this"

**Signals of latency:**
- Rarely appears in top-3 survey responses
- Shows up in long-form discussion, not quick polls
- Implied more than stated
- Tied to deeper identity or workflow (not surface feature requests)

---

## Powerful Pain Points

**Definition:** Pain points that *matter* — they drive behavior, emotion, or purchase decisions. They're characterized by:
- Strong emotional weight (anger, anxiety, relief when solved)
- Direct impact on outcomes (revenue, time, reputation)
- Recurrence (happens often, not one-off)
- Willingness to pay or switch to solve it

**Signals of power:**
- "I would pay for..." or "I switched because..."
- Intense language, caps, exclamation
- Mentioned in context of churn or switching
- Tied to core job-to-be-done

---

## Prioritization Rubric

| Dimension | Low (1) | Medium (2) | High (3) |
|-----------|---------|-------------|----------|
| **Latency** | Obvious, well-documented | Somewhat hidden | Highly latent, rarely articulated |
| **Power** | Mild annoyance | Meaningful impact | Decision driver, strong emotion |
| **Evidence** | Single source | 2–3 sources | Multiple sources, triangulated |
| **Actionability** | Hard to address | Addressable with effort | Clear path to solution |

**Target:** Prioritize pain points that score high on Latency + Power, with strong Evidence.

---

## Synthesis Rules

- A pain point must appear in **at least 2 distinct sources** to count
- Prefer **verbatim quotes** over paraphrasing
- Note **context** (where found, under what circumstance)
- Flag **contradictions** (e.g. "easy" vs "impossible" for same thing)
```

---

### File 3: `source-definitions.md`

```markdown
# Source Definitions

*Where to look. What to extract. How to recognize pain-point signals.*

---

## Source Hierarchy

Priority order for this research (adjust per use case):

| Priority | Source | Access | Extraction focus |
|----------|--------|--------|-------------------|
| 1 | Reddit | Public / API | Threads, comments, "rant" posts, workaround discussions |
| 2 | LinkedIn | Public / API | Posts, comments, job descriptions (implicit pain in requirements) |
| 3 | Twitter/X | Public / API | Tweets, replies, hashtags, quote tweets |
| 4 | Review sites (G2, Capterra, TrustRadius) | Public | Pros/cons, feature requests, complaints |
| 5 | Industry forums | Public | Support threads, feature requests, "how do I" |
| 6 | YouTube comments | Public | Video-specific frustrations |
| 7 | Support tickets / NPS | Internal | (If available) Raw, unfiltered feedback |

---

## Extraction Rules by Source

### Reddit
- **Look for:** r/[industry], r/[role], r/[tool], r/Entrepreneur, r/smallbusiness
- **Signals:** "Does anyone else", "Am I the only one", "Why does [X] suck", workaround posts
- **Extract:** Problem statement, context, emotion, what they tried

### LinkedIn
- **Look for:** Posts from target role, comment threads, job postings (pain in requirements)
- **Signals:** Rants, "hot take", "unpopular opinion", frustration with tools/processes
- **Extract:** Professional framing, industry language, implicit vs explicit pain

### Twitter/X
- **Look for:** Follow target accounts, hashtags, search queries
- **Signals:** Complaints, "I hate when", "Why is there no", comparison tweets
- **Extract:** Raw sentiment, brevity (often more honest)

### Review Sites
- **Look for:** Competitor reviews, tool reviews in space
- **Signals:** Cons section, "missing feature", "wish it had", comparison language
- **Extract:** Stated vs implied pain (what they don't say matters too)

### Forums / Communities
- **Look for:** Industry-specific forums, Slack/Discord communities
- **Signals:** Support threads, "how do I", workarounds, "I had to build"
- **Extract:** Recurring themes, language patterns

---

## What to Capture Per Finding

- **Source:** [Platform, link or reference]
- **Quote / Paraphrase:** [Verbatim when possible]
- **Context:** [Where, when, under what circumstance]
- **Latency signal:** [Why we think it's latent]
- **Power signal:** [Why we think it matters]
- **Demographic fit:** [How we know it's our target]
```

---

### File 4: `research-methodology.md`

```markdown
# Research Methodology

*How to gather, synthesize, and surface latent + powerful pain points.*

---

## Workflow

```
1. SCOPE     → Load target demographic, define search focus
2. GATHER    → Query each source per source-definitions
3. EXTRACT   → Capture findings with quotes, context, signals
4. CLUSTER   → Group similar pain points across sources
5. TRIANGULATE → Validate with multiple sources; flag single-source items
6. PRIORITIZE → Apply pain-point-criteria rubric
7. SYNTHESIZE → Structure per output-spec
```

---

## Gathering Strategy

- **Breadth first:** Cast wide across sources before going deep
- **Depth on clusters:** When a theme appears 2+ times, dig for more evidence
- **Follow the workarounds:** People describing hacks often reveal latent pain
- **Negative space:** What do they *not* complain about? What do they assume is unfixable?

---

## Synthesis Approach

1. **Cluster by theme** — Group findings into coherent pain-point categories
2. **Name the pain** — Give each cluster a clear, actionable label
3. **Evidence per pain** — 2+ sources, verbatim quotes, context
4. **Score** — Latency (1–3) + Power (1–3) + Evidence strength
5. **Rank** — Sort by (Latency × Power), then by evidence strength

---

## Latent Detection Heuristics

- **"It's just the way it is"** — Resignation signals unarticulated pain
- **Workaround language** — "I had to", "I built a script to", "we just"
- **Comparison frustration** — "Why can't X be like Y" reveals hidden expectation
- **Identity language** — "As a [role], I..." — pain tied to role is often latent
- **Emerging vs entrenched** — New complaints may indicate emerging pain

---

## Quality Checks

- [ ] Every pain point has ≥2 distinct sources
- [ ] Verbatim quotes included where possible
- [ ] Latency and power justified (not assumed)
- [ ] Contradictions or edge cases noted
- [ ] Output matches output-spec structure
```

---

### File 5: `output-spec.md`

```markdown
# Output Spec

*What "great documentation" on pain points looks like.*

---

## Structure

```markdown
# Pain Points Research: [Target Demographic / Focus]
*Generated: [Date] | Scope: [Brief description]*

## Executive Summary
- Top 3–5 pain points (latent + powerful, prioritized)
- Key insight or pattern across findings

---

## Detailed Findings

### Pain Point 1: [Name]
**Latency:** [1–3] | **Power:** [1–3] | **Evidence:** [Strong/Moderate]

**Definition:** [One-sentence description of the pain]

**Evidence:**
- [Quote 1] — *Source, context*
- [Quote 2] — *Source, context*
- [Summary of additional evidence]

**Why latent:** [Explanation]
**Why powerful:** [Explanation]
**Opportunity:** [How this could be addressed]

---

### Pain Point 2: [Name]
[Same structure]

---

## Methodology Note
- Sources consulted
- Limitations
- Suggested next steps (e.g. deeper dive on Pain Point 3)
```

---

## Depth Requirements

| Section | Requirement |
|---------|-------------|
| Executive Summary | Actionable; decision-maker can skim and get value |
| Each pain point | Min 2 sources, at least 1 verbatim quote |
| Opportunity | Not required for every pain point, but encouraged for top 3 |
| Methodology note | Brief; ensures reproducibility |

---

## Formatting

- **Clear headers** — Pain point names should be scannable
- **Quotes in blockquote** — Distinguish evidence from analysis
- **Tables where helpful** — E.g. quick comparison of pain points
- **No fluff** — Every sentence should add information

---

## What "Great" Means

- **Actionable** — A stakeholder could use this to inform product, positioning, or content
- **Evidence-based** — Claims are backed by research, not assumptions
- **Prioritized** — Reader knows where to focus
- **Honest** — Limitations and contradictions included where relevant
```

---

## Part 2: Variable Substitution Logic

When the user says `last30 pain-points SMB founders in B2B SaaS` (or `last30 pain SMB founders in B2B SaaS`), parse and substitute:

| Variable | Parse Rule | Example |
|----------|-----------|---------|
| `{{TARGET_DEMOGRAPHIC}}` | Everything after `pain-points` / `pain` | "SMB founders in B2B SaaS" |
| `{{INDUSTRY}}` | Extract industry terms (B2B, SaaS, etc.) | "B2B SaaS" |
| `{{ROLE}}` | Extract role (founder, manager, etc.) | "founder" |
| `{{COMPANY_SIZE}}` | SMB, enterprise, startup, etc. if present | "SMB" or "startup" |
| `{{MARKET_NICHE}}` | Optional; niche within industry | (empty or parsed) |
| `{{PAIN_POINT_FOCUS}}` | Optional; user may add "focus on X" | (empty or parsed) |
| `{{RESEARCH_DEPTH}}` | `--quick`, `--deep`, or default `balanced` | "balanced" |

**Simple parsing (minimum viable):**
- `TARGET_DEMOGRAPHIC` = rest of user input after trigger
- `INDUSTRY` = "general" or first noun phrase
- `ROLE` = "professional" or first role-like word
- Leave placeholders as-is if parsing is ambiguous; the cascade files still guide the research.

**Topic derivation for `last30days.py`:**
```
TOPIC = "{TARGET_DEMOGRAPHIC} pain points frustrations"
```
Example: `"SMB founders in B2B SaaS pain points frustrations"`

Add `{{PAIN_POINT_FOCUS}}` if present: `"SMB founders B2B SaaS product onboarding pain points"`

---

## Part 3: SKILL.md Template for `variants/pain-points/`

Create `variants/pain-points/SKILL.md` with the content below. This can be used as:
- A drop-in replacement for the main SKILL (e.g. `cp variants/pain-points/SKILL.md .`) for pain-points-only installs, or
- A reference file that the main SKILL routes to when it detects `pain-points` / `pain`

```markdown
---
name: last30days-pain-points
version: "1.0"
description: "Research pain points for a target demographic. Uses context orchestration. Sources: Reddit, X, YouTube, web."
argument-hint: 'last30 pain SMB founders in B2B SaaS, last30 pain-points indie game devs'
allowed-tools: Bash, Read, Write, AskUserQuestion, WebSearch
---

# Pain Points Research Mode

Research the most latent and powerful pain points for a target demographic using the context orchestration methodology.

## Step 1: Parse User Intent

Extract from user input:
- **TARGET_DEMOGRAPHIC** — Everything after `pain-points` or `pain` (e.g. "SMB founders in B2B SaaS")
- **PAIN_POINT_FOCUS** — Optional narrowing (e.g. "product onboarding" if user says "focus on product onboarding")
- **RESEARCH_DEPTH** — `--quick`, `--deep`, or default `balanced`

**Display before running:**
```
I'll research pain points for: {TARGET_DEMOGRAPHIC}
Focus: {PAIN_POINT_FOCUS or "broad"}
Depth: {RESEARCH_DEPTH}
```

## Step 2: Load the Cascade (REQUIRED)

**Read these 5 files IN ORDER** using the Read tool. Find skill root first:

```bash
for dir in "." "${CLAUDE_PLUGIN_ROOT:-}" "$HOME/.claude/skills/last30days" "$HOME/.agents/skills/last30days"; do
  [ -n "$dir" ] && [ -f "$dir/scripts/last30days.py" ] && SKILL_ROOT="$dir" && break
done
```

Then Read (replace {SKILL_ROOT} with resolved path):
1. `{SKILL_ROOT}/variants/pain-points/target-demographic.md`
2. `{SKILL_ROOT}/variants/pain-points/pain-point-criteria.md`
3. `{SKILL_ROOT}/variants/pain-points/source-definitions.md`
4. `{SKILL_ROOT}/variants/pain-points/research-methodology.md`
5. `{SKILL_ROOT}/variants/pain-points/output-spec.md`

**Variable substitution:** When loading target-demographic.md, mentally substitute:
- {{INDUSTRY}} → derived from TARGET_DEMOGRAPHIC (e.g. "B2B SaaS")
- {{ROLE}} → derived (e.g. "founder")
- {{COMPANY_SIZE}} → derived if clear (e.g. "SMB"), else leave as placeholder
- {{MARKET_NICHE}} → derived or leave blank

## Step 3: Derive Research Topic

```
TOPIC = "{TARGET_DEMOGRAPHIC} pain points frustrations"
```
If PAIN_POINT_FOCUS is set: append it. Example: "SMB founders B2B SaaS product onboarding pain points frustrations"

## Step 4: Run Research Script

Run in FOREGROUND with 5-minute timeout:

```bash
python3 "${SKILL_ROOT}/scripts/last30days.py" "$TOPIC" --emit=compact
```

Pass --quick or --deep if user specified.

**Read the ENTIRE output** — Reddit, X, YouTube sections.

## Step 5: WebSearch Supplement

After script completes, do WebSearch (exclude reddit.com, x.com, twitter.com):
- `{TARGET_DEMOGRAPHIC} pain points frustrations`
- `{TARGET_DEMOGRAPHIC} common problems`
- Optional: `{TARGET_DEMOGRAPHIC} {PAIN_POINT_FOCUS} complaints` if focus set

## Step 6: Synthesize Per Pain-Points Methodology

Follow research-methodology.md and output-spec.md. Output MUST:

1. **Cluster** findings by theme (not just "top patterns")
2. **Name** each cluster as a pain point
3. **Score** each: Latency (1–3) + Power (1–3) + Evidence (Strong/Moderate)
4. **Require** ≥2 distinct sources per pain point
5. **Structure** per output-spec:
   - Executive Summary (top 3–5 pain points, key insight)
   - Detailed Findings (each pain point with Definition, Evidence, Why latent, Why powerful, Opportunity)
   - Methodology Note (sources, limitations, next steps)

Use the **Synthesis Checklist** (see Part 4 below) before delivering.

## Step 7: Deliver Report

Output the structured report. After the report, show stats:

```
---
✅ Pain points research complete
├─ Reddit: {N} threads
├─ X: {N} posts
├─ YouTube: {N} videos
└─ Web: {N} pages
---
```

## Context Memory

Remember for follow-up: TARGET_DEMOGRAPHIC, pain points found, key evidence. Do NOT run new research unless user asks about a different demographic.
```

---

## Part 4: Synthesis Checklist for Pain-Points Mode

Before delivering the output, verify:

- [ ] **Every pain point has ≥2 distinct sources** — Single-source items are flagged, not promoted
- [ ] **Verbatim quotes included** — At least 1 direct quote per pain point where possible
- [ ] **Latency and power justified** — Don't assume; explain why each pain is latent and/or powerful
- [ ] **Contradictions noted** — If sources disagree, say so
- [ ] **Output structure matches output-spec** — Executive Summary → Detailed Findings (with blocks) → Methodology Note
- [ ] **Scoring applied** — Latency (1–3), Power (1–3), Evidence (Strong/Moderate) per pain point
- [ ] **Ranked by (Latency × Power)** — Top pain points first, then by evidence strength

---

## Part 5: Enabling the Pain-Points Variant

### Option A: Replace main SKILL (pain-points only)

```bash
cp variants/pain-points/SKILL.md SKILL.md
```

User then runs `last30 pain SMB founders` directly.

### Option B: Add routing to main SKILL (multi-mode)

In the main `SKILL.md`, add routing logic near the top (before "Parse User Intent"):

```
If user input starts with "pain-points" or "pain ":
  → Read variants/pain-points/SKILL.md and follow its instructions
  → Otherwise continue with standard research flow
```

Or add a `references/pain-points.md` that the open variant's command router can use, and extend the routing table to include `pain` / `pain-points`.

### Option C: Standalone install

Users who want only pain-points can clone and swap SKILL:

```bash
git clone https://github.com/jvgitty/research-agent.git ~/.claude/skills/last30days
cp ~/.claude/skills/last30days/variants/pain-points/SKILL.md ~/.claude/skills/last30days/SKILL.md
```

---

## Reference: Dependency Graph

```
OUTPUT: Pain points report
    │
    ├─► SYNTHESIZED_FINDINGS
    │       ├─► RAW_RESEARCH_DATA (from last30days.py + WebSearch)
    │       ├─► SOURCE_DEFINITIONS (cascade file 3)
    │       ├─► TARGET_DEMOGRAPHIC (parsed from user)
    │       ├─► PAIN_POINT_CRITERIA (cascade file 2)
    │       └─► RESEARCH_METHODOLOGY (cascade file 4)
    │
    └─► OUTPUT_SPEC (cascade file 5)
```

---

*Implementation guide for context orchestration + research-agent integration. Source: prompt-orchestration docs.*
