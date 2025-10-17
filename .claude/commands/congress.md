---
allowed-tools: TodoWrite, Bash, BashOutput, Read, Write
---

## Role

Claude Code acts as the **Assembly moderator**:

- Assign tasks to Subagents
- Manage discussion flow
- Finalize conclusions
- All research/proposals/counterarguments handled by Subagents

## Deliberation Flow (6 Steps)

0. **Initialize**: Set up task management with TodoWrite
1. **Frame**: Define goal and pre-scope perspectives
2. **Explore**: 2-5 agents investigate in parallel from diverse perspectives
3. **Critique**: Dialectic Critics refine each claim
4. **Integrate**: Integrator produces final output (synthesis + conclusion in one step)
5. **Evaluate**: Evaluator assesses quality and decides CONCLUDE/CONTINUE

---

### 0. Initialize Task Management

**MANDATORY FIRST STEP**: Use TodoWrite to create task list with these items:

1. Initialize task management
2. Frame deliberation (goal, perspectives, scoping)
3. Confirm Explorer lineup with user
4. Launch 3 Explorers in parallel
5. Launch 3 Critics in parallel
6. Extract key discussion points
7. Run point-by-point integration
8. Create meta-integration conclusion
9. Run point-by-point evaluation
10. Create meta-evaluation and decide CONCLUDE/CONTINUE
11. Finalize and output results

Mark as `in_progress` when starting, `completed` when done. Keep only ONE `in_progress` at a time.

**Slug Uniqueness Check**:

After receiving the user's topic, generate a topic-slug and ensure uniqueness:

1. Generate initial slug from topic (lowercase, URL-safe, replace spaces with hyphens)
2. Check if `.records/[YYYY-MM-DD]/[topic-slug]/` exists using `ls` or similar
3. If directory exists, append `-2`, `-3`, `-4`, etc. until finding a unique slug
4. Inform user: "Using topic slug: `[final-topic-slug]`"

Example:
- Topic: "Vibe Coding for BI"
- Initial slug: `vibe-coding-for-bi`
- If `.records/2025-10-15/vibe-coding-for-bi/` exists → try `vibe-coding-for-bi-2`
- Continue until unique directory name found

---

### 1. Framing and Pre-Scoping

The user provides a topic and goal: **discussion | research | article | idea**

- **discussion**: Deepen and broaden topic through multiple perspectives, revealing unexpected insights
- **research**: Comprehensive multi-angle investigation with summarized findings
- **article**: Intellectually stimulating piece combining broad research and deep insight
- **idea**: Multiple creative ideas, original and surprising, from diverse viewpoints

**Language Detection**: Detect the user's input language from their initial message. All agent outputs MUST be in the same language as the user's input.

**Development Mode**: Automatically determined by goal type to control how far agents can diverge from the original topic:

- **`focused`** (article, research) → Maintain close connection to original topic; prioritize practical, constructive analysis
- **`expansive`** (idea, discussion) → Encourage bold development and provocative perspectives

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

**Selection Dimensions**: Disciplinary | Temporal | Stakeholder | Approach | Expertise

**Example** - "AI in education":

- Explorer 1 (Domain): Learning scientist - cognitive principles, pedagogy
- Explorer 2 (Domain): Classroom teacher - daily practice, implementation
- Explorer 3 (Alternative): Game designer - engagement mechanics, motivation

**Pre-Scoping**: Define investigation boundaries for each Explorer **before** launching them

- Assign specific aspects of the topic to each perspective
- Ensure minimal overlap between the 2 domain experts
- Allow the alternative perspective to explore freely for unexpected insights

#### Confirm Explorer Lineup with User

**MANDATORY**: Present 3 Explorers (role, focus, angle) and ask "Is this lineup appropriate?"

Wait for explicit approval before launching. If rejected/adjusted, revise and re-confirm.

---

### 2. Exploration (Parallel Execution)

Launch **ALL 3 Explorers in parallel** using multiple Task tool calls.

**Parameters**: Topic, date, topic slug, output language, expert role, perspective slug, investigation scope, discussion development direction, boundary note, **development mode**

**Agent**: `@agent-congress-explorer`

**Development mode determination**:

- article, research → `focused`
- idea, discussion → `expansive`

**Discussion development direction** for each Explorer:

1. Specific angle/lens, depth (surface/mid/deep), breadth (narrow/medium/wide)
2. Advancement strategy: Challenge assumptions | Extrapolate | Transplant | Scale shift | Surface tensions | Bridge gaps
3. **When mode is `focused`**: Ensure all radical expansions maintain clear connection to original topic; prioritize explanatory analogies over decorative ones
4. **When mode is `expansive`**: Use current guidelines (radical expansion encouraged)

**Output**: `.records/[YYYY-MM-DD]/[topic-slug]/explorer-[perspective-slug].md`

---

### 3. Critique and Development

Launch **3 Critics in parallel** (one per Explorer)

**Agent**: `@agent-congress-critic` | **Parameters**: Explorer file path, date, topic slug, output language, explorer number, **development mode** | **Output**: `.records/[YYYY-MM-DD]/[topic-slug]/critic-[explorer-number].md`

**Development mode guidance for Critics**:

- **`focused` mode**: At least one pathway must strengthen the core insight; provocative questions maintain relevance to original topic; speculative pathways must clearly connect back to the main argument
- **`expansive` mode**: Use current guidelines (bold, speculative pathways encouraged)

---

### 4. Point Extraction and Orchestrated Integration

#### 4A: Extract Key Points (Claude Code)

Read 3 Critic files, extract 3-5 distinct themes/tensions → `.records/[YYYY-MM-DD]/[topic-slug]/discussion-points.md`

#### 4B: Point-by-Point Integration

Launch **Integrators in parallel** (one per point)

**Agent**: `@agent-congress-integrator` | **Parameters**: 3 Critic paths, date, topic slug, output language, goal type, point focus, point number | **Output**: `.records/[YYYY-MM-DD]/[topic-slug]/integrator-point-[N].md`

#### 4C: Meta-Integration (Claude Code)

Read point integrations, synthesize final output by goal type → `.records/[YYYY-MM-DD]/[topic-slug]/integrator-conclusion.md`

---

### 5. Point-by-Point Evaluation and Meta-Analysis

#### 5A: Point-by-Point Evaluation

Launch **Evaluators in parallel** (one per point)

**Agent**: `@agent-congress-evaluator` | **Parameters**: Date, topic slug, output language, point focus, point number | **Output**: `.records/[YYYY-MM-DD]/[topic-slug]/evaluator-point-[N].md`

#### 5B: Meta-Evaluation and Decision (Claude Code)

Aggregate evaluations, decide CONCLUDE/CONTINUE (Quality ≥ MEDIUM AND Completeness ≥ ADEQUATE) → `.records/[YYYY-MM-DD]/[topic-slug]/evaluation.md`

---

### 6. Final Output

When CONCLUDE: Read `integrator-conclusion.md` → write to `output/[YYYY-MM-DD]/[topic-slug]/output.md` in user's language
