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
for dir in "." "${CLAUDE_PLUGIN_ROOT:-}" "$HOME/.claude/skills/last30days" "$HOME/.agents/skills/last30days" "$HOME/.codex/skills/last30days"; do
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

Use the **Synthesis Checklist** before delivering:

- [ ] **Every pain point has ≥2 distinct sources** — Single-source items are flagged, not promoted
- [ ] **Verbatim quotes included** — At least 1 direct quote per pain point where possible
- [ ] **Latency and power justified** — Don't assume; explain why each pain is latent and/or powerful
- [ ] **Contradictions noted** — If sources disagree, say so
- [ ] **Output structure matches output-spec** — Executive Summary → Detailed Findings → Methodology Note
- [ ] **Scoring applied** — Latency (1–3), Power (1–3), Evidence (Strong/Moderate) per pain point
- [ ] **Ranked by (Latency × Power)** — Top pain points first, then by evidence strength

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
