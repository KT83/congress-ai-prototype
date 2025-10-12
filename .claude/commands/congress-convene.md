## Role

Claude Code acts as the **Assembly moderator**:

- Assign tasks to Subagents
- Manage discussion flow
- Finalize conclusions
- All research/proposals/counterarguments handled by Subagents

## Deliberation Flow (5 Steps)

1. **Frame**: Define goal and pre-scope perspectives
2. **Explore**: 2-5 agents investigate in parallel from diverse perspectives
3. **Critique**: Dialectic Critics refine each claim
4. **Integrate**: Integrator produces final output (synthesis + conclusion in one step)
5. **Evaluate**: Evaluator assesses quality and decides CONCLUDE/CONTINUE

---

### 1. Framing and Pre-Scoping

The user provides a topic and goal: **discussion | research | article | idea**

- **discussion**: Deepen and broaden topic through multiple perspectives, revealing unexpected insights
- **research**: Comprehensive multi-angle investigation with summarized findings
- **article**: Intellectually stimulating piece combining broad research and deep insight
- **idea**: Multiple creative ideas, original and surprising, from diverse viewpoints

**Language Detection**: Detect the user's input language from their initial message. All agent outputs MUST be in the same language as the user's input.

**Success criteria**: Confirm goal and define what makes this deliberation successful.

**Explorer Count**: Fixed at **3 Explorers**

#### Perspective Selection Framework (2+1 Structure)

**Structure**: 2 Domain Experts + 1 Alternative Perspective

**Explorer 1 & 2 - Domain Experts**:
Select 2 experts who are most qualified to discuss this topic:

- Choose different specializations within the domain
- Ensure complementary viewpoints (e.g., practitioner + researcher, technical + humanistic)
- Cover core aspects of the topic

**Explorer 3 - Alternative Perspective**:
Select 1 expert from a different domain who can provide fresh insights:

- Choose a discipline that seems unrelated but could offer analogies
- Look for unexpected connections (e.g., artist for tech topic, engineer for social topic)
- Prioritize potential for novel reframing

**Selection Dimensions**:

1. **Disciplinary**: Technical | Social/Cultural | Economic | Ethical | Political | Artistic | Scientific
2. **Temporal**: Short-term pragmatist | Long-term visionary | Historical contextualist
3. **Stakeholder**: Individual/user | Organizational | Societal | Marginalized communities
4. **Approach**: Optimistic builder | Pragmatic realist | Critical skeptic | Systems thinker
5. **Expertise**: Practitioner | Researcher/Theorist | Policymaker | Domain outsider

**Example** - "AI in education":

- **Explorer 1** (Domain Expert): Learning scientist - Focus on cognitive principles, evidence-based pedagogy
- **Explorer 2** (Domain Expert): Classroom teacher - Focus on daily practice, student engagement, implementation challenges
- **Explorer 3** (Alternative): Game designer - Focus on motivation, engagement mechanics, learning through play

**Example** - "Future of remote work":

- **Explorer 1** (Domain Expert): Organizational psychologist - Focus on team dynamics, productivity, well-being
- **Explorer 2** (Domain Expert): Urban planner - Focus on city design, infrastructure, commuting patterns
- **Explorer 3** (Alternative): Anthropologist - Focus on ritual, culture, social cohesion, human needs

**Pre-Scoping**: Define investigation boundaries for each Explorer **before** launching them

- Assign specific aspects of the topic to each perspective
- Ensure minimal overlap between the 2 domain experts
- Allow the alternative perspective to explore freely for unexpected insights

---

### 2. Exploration (Parallel Execution)

**CRITICAL**: Call **ALL 3 Explorers in parallel** using multiple Task tool calls in a single message.

Example:

```
<invoke Task with Explorer 1 - Domain Expert A>
<invoke Task with Explorer 2 - Domain Expert B>
<invoke Task with Explorer 3 - Alternative Perspective>
```

**Parameters for each Explorer:**

- Topic, date (YYYY-MM-DD), topic slug
- **Output language**: [detected from user input]
- Expert role, perspective slug
- **Investigation scope**: Specific aspect to focus on
- **Radical direction**: Provocative angle using inversion/extrapolation/cross-domain transplant
- **Boundary note**: For domain experts - avoid overlap; for alternative - explore freely

**Radical direction generation**: Challenge fundamental assumptions, use strategies from agent-explorer.md (inversion, extreme extrapolation, cross-domain transplant, scale shift). Keep intellectually productive.

**Usage**: `@agent-congress-explorer`

**Output**: Each Explorer returns a file path in format: `OUTPUT_PATH: .records/[YYYY-MM-DD]/[topic-slug]/explorer-[perspective-slug].md`

**Token Optimization**: Fixed 3-explorer structure ensures predictable token consumption

---

### 3. Critique and Development

Call **3 Dialectic Critics in parallel** (one per Explorer) using **@agent-congress-critic**.

**Parameters**: Explorer file path, date, topic slug, output language, explorer number

**Output**: `OUTPUT_PATH: .records/[YYYY-MM-DD]/[topic-slug]/critic-[explorer-number].md`

---

### 4. Point Extraction and Orchestrated Integration

**This step has two phases to manage context length:**

#### Phase 4A: Extract Key Discussion Points (Claude Code)

**Claude Code reads all 3 Critic files and extracts 3-5 key discussion points** that represent distinct themes/tensions cutting across analyses. Avoid generic categories.

**Output**: Write to `.records/[YYYY-MM-DD]/[topic-slug]/discussion-points.md`

#### Phase 4B: Point-by-Point Integration

For EACH point, call **@agent-congress-integrator** in point-focused mode using **parallel Task calls**.

**Parameters**: All 3 Critic paths, date, topic slug, output language, goal type, point focus, point number

**Output**: `OUTPUT_PATH: .records/[YYYY-MM-DD]/[topic-slug]/integrator-point-[N].md`

#### Phase 4C: Meta-Integration (Claude Code)

**Claude Code reads all point integrations and synthesizes final output** formatted by goal type.

**Output**: Write to `.records/[YYYY-MM-DD]/[topic-slug]/integrator-conclusion.md`

---

### 5. Point-by-Point Evaluation and Meta-Analysis

**Two phases:**

#### Phase 5A: Point-by-Point Evaluation

For EACH point, call **@agent-congress-evaluator** in point-focused mode using **parallel Task calls**.

**Parameters**: Date, topic slug, output language, point focus, point number

**Output**: `OUTPUT_PATH: .records/[YYYY-MM-DD]/[topic-slug]/evaluator-point-[N].md`

#### Phase 5B: Meta-Evaluation and Decision (Claude Code)

**Claude Code aggregates evaluations and decides CONCLUDE/CONTINUE** using decision matrix (Quality ≥ MEDIUM AND Completeness ≥ ADEQUATE).

**Output**: Write to `.records/[YYYY-MM-DD]/[topic-slug]/evaluation.md`

---

### 6. Final Output

When CONCLUDE: Read conclusion from `integrator-conclusion.md` and write to `output/[YYYY-MM-DD]/[topic-slug]/output.md` in user's language.
