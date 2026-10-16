# ASD-STE100 Skill — Simplified Technical English for Agent Output

An agent skill that rewrites dense, ambiguous English into [ASD-STE100 Simplified Technical English](https://www.asd-ste100.org/) (STE), the controlled-language standard the aerospace and defense industry built so aircraft maintenance instructions cannot be misread.

This skill applies that discipline for an **AI agent** that parses another agent's output, a tool description, an error message, or an inter-agent instruction, with no human to resolve ambiguity.

## Table of Contents

- [Why STE, and Why for Agents](#why-ste-and-why-for-agents)
- [Examples](#examples)
- [What This Skill Does](#what-this-skill-does)
- [Installation](#installation)
  - [Claude Code](#claude-code)
  - [Codex](#codex)
  - [Other agents](#other-agents)
  - [Update](#update)
- [Usage](#usage)
- [Scope](#scope)
- [Sources](#sources)
- [License](#license)

## Why STE, and Why for Agents

A misread instruction on an aircraft can kill people, and the readers are often not native English speakers with no author to ask. The standard's fix: one meaning per word, active voice, simple tenses, one instruction per sentence, short sentences, no dropped words.

An LLM agent that parses another agent's output is in a similar position. It cannot ask "did you mean X or Y?" The same rules that keep a mechanic from misreading a torque spec keep an agent from misreading a tool description.

## Examples

See [`examples/before-after.md`](examples/before-after.md) for worked before/after rewrites, including illustrations of the official STE rules.

## What This Skill Does

1. Picks a mode. **Strict** covers procedures, error messages, and tool descriptions. **STE-flavored** covers READMEs, PR descriptions, and prose. It keeps the sentence rules but not the fixed-vocabulary rules.
2. Flags rule violations sentence by sentence: tense, passive voice, multi-instruction sentences, noun clusters, dropped words, long sentences, phrasal verbs, nominalized actions, semicolons, hedge stacks, and marketing adjectives.
3. Rewrites each flagged sentence without dropping any fact, condition, or scope qualifier. If a shorter phrasing would lose precision, it keeps the longer one.
4. Outputs the rewritten text alone, plus a one-line `Kept as-is:` note when it left something unsimplified. Ask "show the diff" or "which rules did it break" to get a table of the rules instead.

The structural rules are mechanical: you can point at the word or mark that breaks each one. Rules that depend on ASD's dictionary are advisory.

`scripts/ste-lint.py` is a deterministic linter for the structural rules (semicolons, phrasal verbs, nominalizations, marketing adjectives, passive voice, present-perfect forms, long sentences, synonym rotation, dangling conjunctions in list items). It never flags hedges or modality, and it cannot verify that a rewrite kept the meaning. Details are in [`references/writing-rules.md`](references/writing-rules.md).

This skill does **not** include ASD's ~900-word approved dictionary. Issue 9 permits reproduction only with ASD's written authority, or by eight listed categories of organisation that this project does not belong to. The skill applies the principle instead: the plainest available word, used the same way every time. For certified STE documentation, use the real standard.

## Installation

Copy the skill folder into the skills directory of your agent. The folder must contain `SKILL.md`, `references/`, and `scripts/`.

```bash
git clone https://github.com/danyuchn/asd-ste100-skill
```

### Claude Code

```bash
mkdir -p ~/.claude/skills
cp -r asd-ste100-skill ~/.claude/skills/asd-ste100
```

- **Personal skill** (all projects): `~/.claude/skills/asd-ste100/`
- **Project skill** (one repository): `.claude/skills/asd-ste100/` in the project root.

### Codex

```bash
mkdir -p ~/.agents/skills
cp -r asd-ste100-skill ~/.agents/skills/asd-ste100
```

- **Personal skill** (all repositories): `~/.agents/skills/asd-ste100/`
- **Project skill** (one repository): `.agents/skills/asd-ste100/` in the repository root.

### Other agents

Use the skills directory that the agent documents.

### Update

Pull the repository and copy the folder again.

## Usage

Ask the agent to simplify or clarify English text:

```
Disambiguate this tool description
Rewrite this error message so an agent can't misparse it
Apply ASD-STE100 to this instruction
```

You get the rewritten text and nothing else. To see which rules applied, add "show the diff" or "explain the changes".

## Scope

Built for: agent-to-agent messages, tool descriptions, error messages, system prompts, and any English text that a machine or non-native reader must parse without a human to ask.

Not built for: creative writing, marketing copy, or anything where voice and nuance matter. STE is flat and literal by design.

STE fixes the form of a text, not its substance. A paragraph with nothing to say comes out short, clean, and still empty.

## Sources

- [ASD-STE100 official site](https://www.asd-ste100.org/)
- [ASD-STE100 — About STE](https://www.asd-ste100.org/about_STE.html)
- [ASD Europe — Simplified Technical English](https://www.asd-europe.org/standards-specifications/simplified-technical-english/)
- [Simplified Technical English — Wikipedia](https://en.wikipedia.org/wiki/Simplified_Technical_English)
- [TechScribe — ASD-STE100 Simplified Technical English](https://www.techscribe.co.uk/techw/asd-simplified-technical-english.htm)

## License

MIT — see [LICENSE](LICENSE).
