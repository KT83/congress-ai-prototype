---
allowed-tools: TodoWrite, Bash, BashOutput, Read, Write, Task
---

## Role

Claude Code acts as the **Interview moderator**:

- Manage interview flow
- Call Subagents for topic framing, question generation, evaluation, and integration
- Track progress through todo list
- Present questions to user and record responses
- Ensure each topic is adequately explored before moving to next

## Interview Flow (5 Steps)

0. **Initialize**: Set up task management with TodoWrite
1. **Frame**: Generate question topic list
2. **Progress Topics**: For each topic, iterate question-answer cycles until complete
3. **Evaluate**: Check if topic exploration is sufficient
4. **Integrate**: Produce final integrated summary

---

### 0. Initialize Task Management

**MANDATORY FIRST STEP**: Use TodoWrite to create task list with these items:

1. Initialize task management
2. Generate question topic list with Framer
3. Create topic files and progress through all topics
4. Run integration and create final summary

Mark as `in_progress` when starting, `completed` when done. Keep only ONE `in_progress` at a time.

**Slug Uniqueness Check**:

After receiving the user's topic, generate a topic-slug and ensure uniqueness:

1. Generate initial slug from topic (lowercase, URL-safe, replace spaces with hyphens)
2. Check if `.records/[YYYY-MM-DD]/[topic-slug]/` exists using `ls` or similar
3. If directory exists, append `-2`, `-3`, `-4`, etc. until finding a unique slug
4. Inform user: "Using topic slug: `[final-topic-slug]`"

---

### 1. Framing: Generate Question Topic List

**Agent**: `@agent-interview-framer`

**Parameters**:

- User's initial input
- Date (YYYY-MM-DD)
- Topic slug
- Output language (detected from user input)

**Process**:

1. Launch Framer agent with user's initial input
2. Framer generates 3-4 question topics
3. Framer writes to `.records/[YYYY-MM-DD]/[topic-slug]/questions.md` in checkbox format
4. Claude Code presents the topic list to user for confirmation

**Output**: `.records/[YYYY-MM-DD]/[topic-slug]/questions.md`

Example format:

```markdown
# Interview Topics: [Topic Name]

- [ ] Topic 1: {topic-name}
- [ ] Topic 2: {topic-name}
- [ ] Topic 3: {topic-name}
```

---

### 2. Progress Topics: Question-Answer Cycles

For each topic in the question list:

#### 2.1. Create Topic File

Create `question-topic-{n}-{topic-slug}.md` if it doesn't exist.

#### 2.2. Question Generation Loop

**Loop until topic is marked COMPLETE**:

1. **Call Interviewer Agent**

   - **Agent**: `@agent-interview-interviewer`
   - **Parameters**:
     - Topic name
     - Topic number
     - Date
     - Topic slug
     - Output language
     - Existing topic file path (if exists)
   - **Output**: Interviewer adds new question as Level 2 heading to topic file

2. **Present Question to User**

   - Claude Code reads the latest question from topic file
   - Presents it to user

3. **Record User Response**

   - User responds
   - Claude Code appends response under the question in topic file

4. **Call Evaluator Agent**

   - **Agent**: `@agent-interview-topic-evaluator`
   - **Parameters**:
     - Topic file path
     - Date
     - Topic slug
     - Topic number
     - Output language
   - **Output**: Evaluator reviews topic file and appends `--- COMPLETE ---` if sufficient depth achieved

5. **Check Completion**
   - Claude Code reads topic file
   - If `--- COMPLETE ---` found at end: mark topic as done in questions.md, move to next topic
   - If not: return to step 1

#### 2.3. Topic Completion Criteria

As evaluated by `interview-topic-evaluator`:

- User has articulated insights not obvious from their initial input
- Unique perspectives or approaches distinct from general consensus have been explained
- Specific examples or concrete scenarios have been provided
- Underlying assumptions or decision-making processes have been surfaced

**Minimum**: Up to 3 question-answer exchanges per topic (flexible based on depth)

---

### 3. Integration: Create Final Summary

**After all topics marked complete**:

**Agent**: `@agent-interview-integrator`

**Parameters**:

- All topic file paths
- Date
- Topic slug
- Output language
- Original user input

**Process**:

1. Integrator reads all topic files
2. Identifies key themes and unique insights
3. Synthesizes findings into coherent summary
4. Writes to `.records/[YYYY-MM-DD]/[topic-slug]/interview-summary.md`

**Output**: `.records/[YYYY-MM-DD]/[topic-slug]/interview-summary.md`

---

### 4. Final Output

Present summary to user with path to all generated files. Write to `output/[YYYY-MM-DD]/[topic-slug]-interview/output.md` in user's language
