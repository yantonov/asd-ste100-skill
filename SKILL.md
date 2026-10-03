---
name: asd-ste100
description: "Use when English text must be parsed without a human to resolve ambiguity — tool descriptions, error messages, inter-agent instructions, system prompts, status reports — and misreading has a real cost, or when text reads as dense, hedged, or easy to misparse. Triggers: disambiguate, STE100 rewrite, apply Simplified Technical English, plain-language rewrite, controlled-language rewrite, rewrite so an agent cannot misread this. Not for creative or marketing copy."
version: 0.4.0
---

# Simplified Technical English (ASD-STE100)

ASD-STE100 removes the two biggest sources of misreading: words with more than one meaning, and sentences with more than one possible structure.

## Scope

This skill encodes the rule categories of ASD-STE100 Issue 9, not ASD's ~900-word dictionary (it cannot be redistributed). So:

- **Structural rules** describe sentence shape. Apply them with confidence.
- **Lexical rules** depend on the dictionary. Apply them as a direction of travel (plainest common word, used the same way every time). Never claim dictionary compliance.

See `references/writing-rules.md` for the full rule summary.

## Modes

If the user does not say which, infer from the text type.

- **Strict** — procedures, error messages, tool descriptions, inter-agent instructions, safety text. Apply every rule below, including one word, one meaning.
- **STE-flavored** — READMEs, PR descriptions, changelogs, prose. Apply the structural rules in full. Treat lexical rules as advisory: prose needs some range.

## Structural rules

| Rule | Do | Don't |
|---|---|---|
| Active voice | "The agent deletes the file." | "The file is deleted." — unless the actor is unknown or irrelevant |
| No phrasal verbs (9.3) | "Remove the panel." / "Start the job." | "Take off the panel." / "Spin up the job." |
| One instruction per sentence | "Open the file. Read line 3." | "Open the file and read line 3, then check if it matches." |
| Sentence length | ≤20 words for instructions, ≤25 for descriptions | Long compound or subordinate sentences |
| No semicolons (8.1) | Split into sentences | Any semicolon. (Em dashes are allowed, but often signal a sentence to split.) |
| Noun clusters | ≤3 stacked nouns ("fuel pump valve") | "high pressure fuel pump inlet valve assembly" |
| No ellipsis | Keep subject, verb, and article explicit | "Files not backed up will be lost" |
| Verb, not noun (3.7) | "Analyze the log." / "helps" | "Perform an analysis of the log." / "provides assistance to" |
| No marketing adjectives | Delete, or give the measurement that earns the claim | seamless, robust, powerful, cutting-edge, blazing-fast |
| No hedge stacking | State the claim or delete it | "it is important to note that this may potentially help to improve" |
| Paragraphs | One topic, ≤6 sentences | Multi-topic paragraphs |
| Lists for sequences | Numbered or bulleted list for 3+ steps or conditions | A sequence buried in one sentence |

**Simple tenses.** STE allows infinitive, imperative, simple present, simple past, simple future, and past participle as adjective. It excludes present perfect: "we received the report", not "we have received the report". Exception: where the compound form carries information the simple form cannot (current relevance, as in "the job has completed", or a hedge, as in "may have failed"), keep it and flag the departure.

## Lexical rules (directional)

| Rule | Do | Don't |
|---|---|---|
| One word, one meaning | One verb per action, reused every time ("check", never "check"/"verify"/"confirm" for the same action) | Rotate synonyms for one idea, or name one thing "user", "customer", and "client" |
| One part of speech per word | "Apply oil to the valve" | "Oil the valve" |
| Domain terms | Keep necessary technical terms and define each once | Use undefined jargon |

## Process

1. Pick the mode.
2. Read the text once for meaning before rewriting.
3. Flag every violation. For a mechanical first pass over the structural rules, run `scripts/ste-lint.py` (stdin or file args; `--help` for options). It never flags hedges or modality.
4. Rewrite each flagged sentence. Preserve meaning exactly:
   - **Keep modality.** "may have failed" stays "may have failed"; "could be caused by X" does not become "X is the cause". A hedge is content, and a length cap tempts you to cut it.
   - **Add no facts.** A rewrite that supplies a cause, frequency, or mechanism the source did not state is not a rewrite.
   - If simplifying would drop precision (a safety condition, scope qualifier, number), keep the longer phrasing and flag it.
5. If the input already complies, say so. Do not force changes.

## Output format

**Default: the rewritten text and nothing else.** No preamble, mode announcement, violation count, summary, rule table, or closing offer.

One permitted addition: if you kept a longer phrasing on purpose, add one line after the text, `Kept as-is:`, naming the phrase and the precision it protects.

**On request** ("show the diff", "which rules did it break", "before/after"), output a table instead, followed by a one-line note on anything deliberately not simplified:

```markdown
| Rule violated | Original | Simplified |
|---|---|---|
| Present perfect tense | "We have received your request." | "We received your request." |
```

## Boundaries

- Do not polish hollow content. STE fixes form, not substance. If the text has nothing to say, say so.
- Do not shorten past clarity. The goal is no ambiguity, not the fewest words.
- Do not simplify creative, marketing, or persuasive copy.
- Do not claim certified STE compliance. This is a clarity tool inspired by STE.

## Resources

- `references/writing-rules.md` — the 9 rule sections and dictionary structure, with citations.
- `examples/before-after.md` — worked examples.
- `scripts/ste-lint.py` — deterministic linter for the structural rules.
