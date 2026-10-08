# Flashcard Generator

> **Source:** Evolved from [flashcard-generator/prompt-v4.txt](https://github.com/maaarcooo/llm-custom-instructions/blob/main/flashcard-generator/prompt-v4.txt). Current skill version: **flashcard-v3.10**. A standalone fallback prompt is maintained as [`prompt-v7.md`](https://github.com/maaarcooo/llm-custom-instructions/blob/main/flashcard-generator/prompt-v7.md) for when the skill feature is unavailable. It matches flashcard-v3.8 and does not yet carry the v3.9 changes.

Generate study flashcards from PDF or Markdown content. Cards are terse, exam-aligned, and quick to self-grade during review.

## The Problem

Creating effective flashcards manually is time-consuming and inconsistent. Poor card design (ambiguous phrasing, recognition-based questions, answers that leak other cards' content) undermines spaced repetition rather than supporting it. Factual errors in source notes get cemented through months of reviews. And model defaults drift across versions: the same prompt that produced clean, terse decks on one model produces padded, over-elaborated decks on the next.

## The Solution

A deliberately small rule set, split into two types:

- **Defect-blocking rules** (strict): no yes/no questions, no pipe characters in content, no diagram-dependent cards, no circular answers, accuracy verification with error flagging. These prevent objective failures and never shape style.
- **Style conveyed by example** (flexible): the terse register, bundling judgment, and card-type selection are taught through concrete example cards rather than prescriptive principles, leaving the model room to adapt per situation.

This split is the result of testing across model versions: prescriptive style rules (rigid atomicity targets, mandatory "why/how" framing, universal bidirectional cards) were found to globally distort deck character, while defect-blocking rules cost nothing. Output is in standard importable format (`Question | Answer`, one card per line).

## Key Features

- **Flexible atomicity**: One idea per card, but a definition may bundle one directly associated detail (formula, unit, key property) when naturally recalled together
- **Bundle or split, not both**: A bundled detail never also gets its own card; no answer contains another card's answer
- **Accuracy verification**: The named exam board's specification is the authority. A confident fix goes on the cards and is listed in chat; doubtful content is left out with the reason given; no card ever carries a statement the model believes to be wrong. Legitimate syllabus-level simplifications are kept, out-of-spec content is flagged rather than added, and nothing is silently rewritten
- **True as phrased**: Every card holds read on its own. A condition the answer depends on goes in the question ("an insulated gas"), but only a condition the specification itself states with the fact, never a refinement from beyond the level
- **Worked single-step cards**: Each main equation candidates calculate with gets one worked card with values and units beside its formula card
- **Practicals covered as methods**: What is measured and with what, what is kept constant, the reason for each key step, and the sources of error the material explains
- **Judgement on exam-supplied content**: Constants and data values the exam supplies are left out. Equations and rules candidates use fluently are carded even when a formula booklet gives them; only a long, seldom-used supplied formula is left out, and each omission is listed
- **Constrained final check**: Exactly three passes: name the condition on a card that is only true in context, resolve contradicting cards, and add one compare card per pair the source itself contrasts
- **Six card types**: Definition (with reverse cards for terms only), recall, formula application, cloze, explain, enumeration
- **Content-driven deck size**: No card-count targets; the material decides

## When to Use

When the user asks for flashcards, a flashcard deck, or study cards from source material.

## How It Works

1. **Read** the source file (PDF or Markdown) thoroughly
2. **Verify** accuracy against the named specification: confident fixes go on the cards, doubtful content is left out, simplifications are kept, and every fix, omission or flag is reported in chat as release-note bullets (never in the output file)
3. **Identify** key content: definitions, laws, equations, units, standard values, conditions, named processes, and common explain/justify points
4. **Generate** cards covering all essential topic content: every definition, law and equation, the unit of each quantity the topic introduces, practical methods, and tables the material sets out as content to learn
5. **Check** the finished set for cards that are only true in context, contradictions, and source-contrasted pairs only
6. **Format** output as one card per line: `Question | Answer`

Instructions the user gives with the material set the scope: the coverage rules and exclusions then apply to that scope only.

## Card Style

The terse register is the priority: when any rule conflicts with brevity, brevity wins.

- **One idea per card, judged flexibly**: answers are one to two short sentences; a definition may bundle one directly associated detail, never a reasoning chain or a second independent concept
- **Bundle or split, not both**: each detail lives in exactly one place in the deck
- **Punctuation**: no em-dashes or semicolons on a card, and a reverse card is written `<definition> - what term is this?`
- **No rationale padding**: no "because..." justifications appended to recall answers. If reasoning matters, it gets its own card
- **Production over recognition**: no yes/no or true/false questions; rephrase so the answer must be generated
- **Unambiguous**: each question has exactly one correct answer
- **True as phrased**: a card is read alone, so the condition its answer depends on goes in the question. Loose source wording is tightened and the change reported. A condition is added only when the specification states it with the fact (Ohm's law at constant temperature), not when it is true but beyond the level (the pressure at which water boils)
- **Plain language**: simple, direct wording matched to the source's syllabus level
- **LaTeX for equations**: all mathematical expressions use standard LaTeX notation (`$...$` inline, `$$...$$` display) for KaTeX rendering

## Card Types

- **Definition**: forward card always; reverse card as a separate line for key terms only (terms the exam asks candidates to define). Only a term's definition is reversed, never a law, a principle, or a description of when something happens
- **Recall**: single facts, values, units, equations
- **Formula application**: for each main equation candidates are expected to calculate with, one worked single-step card with values and units beside its formula card, never multi-step
- **Cloze**: one deletion per card, used sparingly, only where context cues recall without giving the answer away
- **Explain**: only where the source itself explains the reasoning and it is a likely exam point; mechanism stated in at most two sentences
- **Enumeration**: one card per list item; a list answer may contain at most 3 items, and only if the source treats them as a single fact

Compare/contrast cards are not a free card type: they are generated only by the final check below.

## Coverage

- **Every definition, law and equation gets its card**, with the unit of each quantity the topic introduces. A law candidates are asked to state gets a card asking for the statement. General units the material only uses in passing (the SI unit of mass) get no card
- **Experimental methods and required practicals**: what is measured and with what, what is kept constant and how, the reason for each key step, and the sources of error the material explains
- **Tables the material sets out as content to learn** are carded row by row
- **A formula booklet does not decide what gets a card**: an equation or rule candidates are expected to know and use fluently is carded even when the exam supplies it (the quotient rule, $pV = nRT$). A supplied formula is left out only when it is long, seldom used and looked up in practice, and each one left out is listed in chat

## Final Check

After generating, the deck is scanned for exactly three failure modes:

1. **Unstated context**: a card that is only true with context it does not state; the condition is named in the question
2. **Contradictions**: cards whose answers conflict as phrased (e.g. "Which radiation is most ionising?" answered differently under unstated contexts); each question is rephrased to name its context
3. **Source-contrasted pairs**: where the source explicitly contrasts two concepts, one compare card states the specific point of divergence

No other compare cards are generated, and never two compare cards for the same distinction.

## Exclusions

- Questions requiring a diagram or visual to answer (factual content from diagrams is converted to text cards)
- Multi-step calculations
- A card for rearranging an equation that already has a card
- Constants and data values the exam supplies in the question or on a data sheet
- Content the material itself says need not be learned
- Yes/no or true/false questions
- Answers that merely restate the question (omitted and flagged instead)
- Cards for content the source does not adequately explain
- The `|` character inside any question or answer

## Output Format

One card per line, question and answer separated by a single pipe. No preamble, headers, blank lines, or markdown in the output file.

```
What is the unit of electrical resistance? | Ohm (Ω)
Define specific heat capacity | The energy required to raise the temperature of 1 kg of a substance by 1 °C
The energy required to raise the temperature of 1 kg of a substance by 1 °C - what quantity is this? | Specific heat capacity
What is the equation for kinetic energy? | $E_k = \frac{1}{2}mv^2$
What is the resistance of a component with 6 V across it and 2 A through it? | $R = \frac{V}{I} = \frac{6}{2} = 3\;\Omega$
The SI unit of energy is the [...] | joule (J)
What is an alpha particle? | Two protons and two neutrons (a helium-4 nucleus). Stopped by a few centimetres of air
What is impulse? | The change in momentum of an object when a force acts on it, equal to force times time ($F t = \Delta p$)
What happens to the internal energy of an insulated gas when it expands? | It decreases
What is the worst-case time complexity of binary search? | O(log n)
Explain why resistance increases with temperature in a metal | Ions vibrate with greater amplitude, so electrons collide with them more frequently
How does the elastic limit differ from the limit of proportionality? | Limit of proportionality: extension stops being proportional to force. Elastic limit: material stops returning to its original shape. Proportionality limit is reached first
```

## Sample Prompts

**claude.ai:**

```
Create a flashcard deck from the attached study materials using the "flashcard-generator" skill.
Output the deck as a .txt file named after the source file (e.g. Physics_Chapter_5.pdf → Physics_Chapter_5.txt).
```

**Claude API:**

```
Create a flashcard deck from the attached study materials using the "flashcard-generator" skill.
Output only the flashcard lines in the format "Question | Answer", one per line.
Do not include any preamble, headers, explanations, markdown formatting,
or code fences. The raw output will be saved directly to a text file.
```

## Installation

Place the `SKILL.md` file in your Claude skills directory:

```
skills/
└── flashcard-generator/
    └── SKILL.md
```

Then trigger by asking for flashcards from source material.

## Version Notes

- **flashcard-v3.10**: Punctuation rule taken from the NeuroCards prompt: no em-dashes or semicolons on a card, and a reverse card ends ` - what term is this?` with a hyphen. The skill's own text no longer uses em-dashes
- **flashcard-v3.9**: Brought into line with the generation prompt tuned in NeuroCards on Claude Haiku 5.5, Sonnet 5.5 and Opus 5.5 (October 2026). New rules: every card true as phrased, with its condition in the question; one worked single-step card per main equation; practicals covered as methods; reverse cards for terms only; no cards for rearranged equations, for constants and data values the exam supplies, or for content the material says need not be learned. Three of these were loosened after testing showed them too tight: a table the material teaches is carded row by row (a deck had dropped where digestive enzymes are made), a condition is added only when the specification states it with the fact (a GCSE card had gained "at standard pressure"), and a formula booklet does not decide what gets a card (Opus had dropped the quotient rule). The Coverage section replaces "prioritise, and do not pad", which cost a deck its statement of the first law of thermodynamics and a unit. The check after generating grows from two passes to three. Chat flags gain `Tightened:`. The rules were tested as the NeuroCards prompt, which returns JSON; this skill file, with its pipe-separated output, was not run separately
- **flashcard-v3.8**: Accuracy pass reworked: the named qualification and exam board's specification is the authority; a confident correction goes on the cards and is listed in chat, a doubtful one means the content is left out with the reason given, and no card ever carries a statement the model believes wrong. Out-of-spec content is flagged rather than added. Chat flags are now a release-note bullet list (`Fixed:` / `Skipped:` / `Kept:` / `Flagged:`). Prompted by real decks generated from OCR A Level notes where a swapped example table was kept because it only "seemed" reversed
- **flashcard-v3.7**: Renamed from anki-flashcard-generator to flashcard-generator. Broadened trigger to all flashcard requests. Added LaTeX equation formatting (KaTeX-compatible `$...$` / `$$...$$`) for mathematical expressions
- **flashcard-v3.6**: Rebuilt around the defect-blocking vs style-prescribing rule split. Softened atomicity to permit definition + associated-detail bundling, added bundle-or-split, banned circular answers, constrained the interference check to two cases, removed card-count anchors, scoped reverse cards to key terms, removed GCSE-era "Higher Tier" references, added the pipe-character exclusion. Validated on A-Level Physics sources inside and outside projects with convergent output
- **flashcard-v3.3 to flashcard-v3.5**: Principle-heavy versions (depth of processing, universal bidirectional cards, personal connection); produced over-elaborated decks and were superseded
