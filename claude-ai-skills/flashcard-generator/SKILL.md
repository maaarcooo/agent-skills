---
name: flashcard-generator
description: Generate flashcard decks from PDF or Markdown study materials. Use when the user asks for flashcards, a flashcard deck, or study cards from source material. Outputs in importable format (Question | Answer).
---

# Flashcard Generator

Generate study flashcards from PDF or Markdown content. Cards should be terse, exam-aligned, and quick to self-grade during review.

## Process

1. Read the source file (PDF or Markdown) thoroughly
2. Verify accuracy. Where the material names or clearly implies a qualification and exam board (for example OCR A Level Computer Science), treat that specification as the authority on content and terminology. Where the source is wrong and you are confident of the correct version, put the corrected version on the cards and list the fix in the chat response. An error the material itself reveals counts as clear: a swapped table, a mislabelled diagram, a claim the rest of the material contradicts. Where you are not confident of the correct version, leave that content out and say why. Never put a statement you believe to be wrong on a card, and never silently rewrite source content. Keep the source's wording only where it is a legitimate syllabus-level simplification, and flag rather than add content the specification does not require
3. Identify key content: definitions, laws, equations, units, standard values, conditions, named processes, and common explain/justify points. Treat bolded or highlighted text as a strong signal where formatting survives extraction
4. Generate cards covering all essential topic content (see Coverage)
5. Reread the finished set and fix exactly three things: a card that is only true with context it does not state, cards that contradict each other, and confusable pairs the source itself contrasts (see Final Check)
6. Output one card per line: `Question | Answer`

If the user gives instructions with the material (for example restricting to one topic, or asking for every value in a table), follow them. The coverage rules and exclusions then apply to the selected scope only.

## Card Style

This register is the priority. When any rule below conflicts with brevity, prefer brevity.

- **One idea per card, judged flexibly.** Answers are one to two short sentences. A definition may be bundled with one directly associated detail (its formula, unit, key property, or an example) when they are naturally recalled together (e.g. impulse: definition plus $F \times t$). Never bundle a reasoning chain or a second independent concept
- **Bundle or split, not both.** If a detail is bundled into a definition answer, do not also give it its own card. If it has its own card, leave it out of the definition. An answer must never contain the answer to another card in the deck
- **No rationale padding.** Do not append "because..." justifications to recall answers. If the reasoning matters, it gets its own card
- **Production, not recognition.** No yes/no or true/false questions: rephrase so the answer must be generated. Not "Have quarks been observed in isolation? | No", but "In what combinations are quarks always observed? | Pairs (mesons) or groups of three (baryons)"
- **Unambiguous.** Each question has exactly one correct answer. Rephrase vague questions ("What is important about X?") to target one specific property
- **True as phrased.** Every card is true read on its own, with no page around it. Name in the question the condition the answer depends on: "What happens to the internal energy of an insulated gas when it expands?", not "What happens to the internal energy of a gas when it expands?". Where the source states something loosely, tighten it and list the change in the chat response. A condition belongs on a card only when the specification states it with the fact, as it does for Ohm's law at constant temperature. Leave out a condition that is true but that the level never mentions, such as the pressure at which water boils
- **Punctuation.** No em-dashes or semicolons on a card. Use a comma, a colon before a list of values, or two sentences
- **Plain language.** Simple, direct wording. Match the source's syllabus level
- **LaTeX for equations.** Write all mathematical expressions using standard LaTeX notation: inline with `$...$` and display with `$$...$$`. For example, `$E_k = \frac{1}{2}mv^2$` rather than `Ek = ½mv²`. Use LaTeX for all symbols, fractions, subscripts, superscripts, and special characters in equations

## Card Types

- **Definition**: `What is X? | [definition]`. For key terms only (terms the exam asks candidates to define), also output the reverse as a separate line: `[definition] - what term is this? | X`. Reverse the definition of a term only, never a law, a principle, or a description of when something happens
- **Recall**: single facts, values, units, equations
- **Formula application**: for each main equation candidates are expected to calculate with, one worked single-step card with values and units beside its formula card: `What is the resistance of a component with 6 V across it and 2 A through it? | $R = \frac{V}{I} = \frac{6}{2} = 3\;\Omega$`. Never multi-step
- **Cloze**: `The SI unit of energy is the [...] | joule (J)`. Use sparingly, one deletion per card, only where surrounding context is a natural cue without giving the answer away
- **Explain**: only where the source itself explains the reasoning and it is a likely exam point. Answer states the mechanism concisely, still within two sentences. Do not convert recall content into explain cards
- **Enumeration**: one card per list item, not one card per list. A list answer may contain at most 3 items, and only if the source treats them as a single fact

## Coverage

Aim for thorough coverage of the source. Deck size is determined by content density: let the material decide how many cards are needed.

- **Every definition, law and equation gets its card**, along with the unit of each quantity the topic introduces. A law or principle candidates are asked to state gets a card asking for the statement. Do not make a card for a general unit the material only uses in passing, such as the SI unit of mass
- **Experimental methods and required practicals**: cover what is measured and with what, what is kept constant and how, the reason for each key step, and the sources of error the material explains
- **A table the material sets out as content to learn is carded row by row**: food tests, vocabulary lists, dates, stopping distances
- **A formula booklet does not decide what gets a card.** An equation or rule candidates are expected to know and use fluently gets its card even when the exam supplies it, such as the quotient rule or $pV = nRT$. Leave out a supplied formula only when it is long, seldom used, and looked up in practice, and list each one you left out in the chat response

## Final Check

After generating the deck, check for three failure modes only:

1. **Unstated context**: a card that is only true with context it does not state. Name the condition in the question (see True as phrased)
2. **Contradictions**: two cards whose answers conflict as phrased (e.g. "Which radiation is most ionising?" answered "alpha" on one card and "gamma" on another because each assumed a different unstated context). Rephrase each question to name its context so both are unambiguous
3. **Source-contrasted pairs**: where the source explicitly contrasts two concepts (e.g. elastic limit vs limit of proportionality), add one compare card stating the specific point of divergence

Do not generate compare cards beyond these cases, and never two compare cards that test the same distinction.

## Exclusions

Do not create:

- Questions requiring a diagram or visual to answer (convert any factual content from diagrams into text cards instead)
- Multi-step calculations
- A card for rearranging an equation that already has a card
- Cards for constants and data values the exam supplies in the question or on a data sheet
- Cards for content the material itself says need not be learned
- Yes/no or true/false questions
- Answers that merely restate the question (e.g. "Why do kaons have long lifetimes? | This is characteristic of particles containing strange quarks"). If the source only offers a circular explanation, omit the card and flag it
- Cards for content the source does not adequately explain

## Output Format

One card per line, separated by a single pipe:

```
Question | Answer
```

- Never use the `|` character inside a question or answer (e.g. write "magnitude of v" rather than |v|). One pipe per line, exactly
- Reverse cards are separate lines in the same format
- No preamble, headers, blank lines, markdown, or code fences in the output file
- Accuracy flags go in the chat response, never in the output file, as a bullet list in the manner of release notes: one line per item, each starting with `Fixed:`, `Tightened:`, `Skipped:`, `Kept:` or `Flagged:`, the point stated directly, no paragraphs

**Example output:**

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
