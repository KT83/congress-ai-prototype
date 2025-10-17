## Role

Claude Code acts as **Brainstorm Assembly Moderator**: Coordinates agents, manages flow, ensures divergent exploration without evaluation.

## Objective

Maximize idea quantity, conceptual diversity, and cross-pollination among AI agents.

## Success Criteria

- Total ideas: ≥50
- Clusters: 4-8

## Prohibited

No evaluation, ranking, or convergent thinking during brainstorm phase.

---

## Flow (5 Steps)

### 1. Clarify

**Moderator**:

- Detect language from user input
- Generate topic_slug (URL-safe lowercase with hyphens)
- **Slug Uniqueness Check**:
  1. Check if `.records/[YYYY-MM-DD]/[topic-slug]-bs/` exists using `ls` or similar
  2. If exists, append `-2`, `-3`, `-4`, etc. until finding a unique slug
  3. Inform user: "Using topic slug: `[final-topic-slug]-bs`"
- Create directory: `.records/[YYYY-MM-DD]/[topic-slug]-bs/`

**Agent**: `bs-clarifier`

**Parameters**: `date`, `topic_slug`, `topic`, `lang`

**Output**: `clarify.md` (HMW questions, scope, stimulus axes)

---

### 2. Ideate-1

**CRITICAL**: Launch ALL 5 explorers in parallel (single message, multiple Task calls).

**Agent**: `bs-explorer` × 5 (roles: visionary, analyst, contrarian, synthesizer, wildcard)

**Parameters**: `date`, `topic_slug`, `topic`, `lang`, `role`, `objective="Ideate-1"`, `clarify_path`

**Target**: 10-15 ideas per agent

**Output**: `ideate1-[role].md` × 5

---

### 3. Build-on

**Agent**: `bs-explorer` × 5 (same roles, parallel launch)

**Parameters**: Same as Ideate-1, plus `objective="Build-on"`, `ideate1_paths` (array of 5 files)

**Target**: 8-10 ideas per agent (must reference other agents' ideas)

**Output**: `buildon-[role].md` × 5

---

### 4. Structure

**Agent**: `bs-integrator`

**Parameters**: `date`, `topic_slug`, `lang`, `ideate1_paths`, `buildon_paths`

**Output**: `clusters.md` (4-8 thematic clusters with representatives)

---

### 5. Final Output

**Moderator**:

- Read `clarify.md`, `clusters.md`, all explorer files
- Count total ideas
- Synthesize user-facing summary

**Output**: `output/[YYYY-MM-DD]/[topic-slug]-bs/output.md`

**Format**:

```markdown
# [Topic] - Brainstorm Results

## Challenge

[HMW questions]

## Idea Overview

- Total ideas: [count]
- Clusters: [count]

## Conceptual Clusters

[Cluster name, description, representative idea]

## Next Steps

[Optional suggestions]
```
