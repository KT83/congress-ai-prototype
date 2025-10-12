⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀

```
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢸⡇⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣈⣁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠘⠛⠛⠃⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⣶⣿⣿⣶⣦⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣴⣿⣿⣿⣿⣿⣿⣿⣿⣦⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⣸⣏⣉⣿⣿⣉⣉⣿⣿⣉⣹⣇⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⣀⣉⣉⣉⣉⣉⣉⣉⣉⣉⣉⣉⣉⣀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠈⣉⠉⠉⢉⠉⠉⢉⡉⠉⠉⡉⠉⠉⣉⠁⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠛⠀⠀⠘⠀⠀⠘⠃⠀⠀⠃⠀⠀⠛⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠚⠛⠛⠛⠛⠛⠛⠛⠛⠛⠛⠛⠛⠛⠛⠛⠛⠛⠛⠓⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⣿⠀⠀⣿⠀⢸⡇⠀⢸⡇⠀⢸⡇⠀⣿⠀⠀⣿⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⢿⠀⠀⡿⠀⠸⡇⠀⢸⡇⠀⢸⠇⠀⢿⠀⠀⡿⠀⠀⠀⠀⠀⠀
⠀⠀⢰⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⣶⡆⠀⠀
⠀⠀⠈⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠉⠁⠀⠀
```

# Congress AI (Prototype)

This project is a prototype of a multi-agent deliberation system, where multiple specialized agents engage in in-depth discussions in response to user questions and proposals, generating new ideas and suggestions.

It relies entirely on Claude Code.

## Project Structure

- `.claude/` - Claude Code configurations
  - `agents/` - Agent definitions (congress-explorer, congress-critic, congress-integrator, congress-evaluator)
  - `commands/` - Slash commands (congress-convene.md - main deliberation protocol)
- `.records/[YYYY-MM-DD]/[topic-slug]/` - Deliberation process outputs (explorers, critics, integrator, evaluator)
- `output/[YYYY-MM-DD]/[topic-slug]/` - Final output files organized by date and topic

## Usage

Start a deliberation with:

```
/congress-convene [topic description]

[goal: discussion | research | article | idea]
```

Example:

```
/congress-convene Everything becomes vibe coding

article
```

### Language Support

The system automatically detects your input language and all outputs (explorer analysis, critic feedback, integrator conclusion, evaluator assessment, and final report) will be generated in the same language. For example:

- Input in Japanese → All outputs in Japanese
- Input in English → All outputs in English

## Deliberation Flow

1. **Frame**: Define goal and pre-scope perspectives (detect user language)
2. **Explore**: 3 agents investigate in parallel (2 domain experts + 1 alternative perspective)
3. **Critique**: 3 Dialectic Critics refine each claim (1 per Explorer)
4. **Integrate**: Integrator produces final output (synthesis + conclusion in one step)
5. **Evaluate**: Evaluator assesses quality and decides CONCLUDE/CONTINUE

## Output

### Process Outputs (`.records/`)

Intermediate deliberation outputs saved to `.records/[YYYY-MM-DD]/[topic-slug]/`:

- `explorer-[perspective-slug].md` (3 files - one per Explorer)
- `critic-[1-3].md` (3 files - one per Critic)
- `integrator-conclusion.md` (1 file)
- `evaluation.md` (1 file)

### Final Output (`output/`)

When Evaluator recommends CONCLUDE, final report saved to:
`output/[YYYY-MM-DD]/[topic-slug]/output.md`

All outputs are generated in the same language as the user's input.
