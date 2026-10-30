# Quiz Generator

> **Source:** Split out of `flashcard-generator` (its v3.11 Quizzes section) into a skill of its own. Current skill version: **quiz-v1.2**. A standalone fallback prompt is maintained as [`quiz-generator/prompt-v3.md`](https://github.com/maaarcooo/llm-custom-instructions/blob/main/quiz-generator/prompt-v3.md) for when the skill feature is unavailable. It matches quiz-v1.2.

Generate multiple choice questions from PDF or Markdown content, as a quiz file that NeuroCards imports. Each question tests one idea, is quick to answer, and is fair to someone who knows the topic.

## The Problem

A multiple choice question is only as good as its wrong choices. Wrong choices that are obviously wrong, longer or shorter than the correct one, or arguably correct themselves turn the question into a test of reading the choices rather than of knowing the topic. "All of the above" breaks once the app shows the choices in a new order, and a question that only makes sense once the choices are read hides what it is asking.

## The Solution

The question rules NeuroCards generates quizzes with, as a skill. It keeps the accuracy pass and the exam-board authority of `flashcard-generator`, and replaces the card rules with rules for questions and their choices. It is a separate skill because several card rules contradict a multiple choice question: production rather than recognition, reverse cards for key terms, and one `Question | Answer` line per card.

## Key Features

- **Believable wrong choices**: Mistakes a learner makes (a related term, a misconception, a value with a typical slip), of the same length and form as the correct choice, and plainly wrong to someone who knows the topic
- **One idea per question**: The question has one clear answer before the choices are read
- **Two to four choices**: Four by default. More than one correct choice only when the question's wording asks for several, with no "(Select all that apply)" note
- **No "all of the above"**, "none of the above" or "which of these is NOT"
- **Short explanations**: One or two sentences on why the correct choice is correct, left out for plain recall
- **Accuracy verification**: The named exam board's specification is the authority. A confident fix goes in the quiz and is listed in chat, doubtful content is left out with the reason given, and no choice is ever marked correct when the model believes it wrong
- **True as phrased**: A condition the answer depends on goes in the question
- **Constrained final check**: Exactly two passes: name the condition on a question that is only true in context, and replace a wrong choice that is arguably correct
- **Content-driven quiz size**: No question-count targets; the material decides
- **Edits a quiz you have**: Given a NeuroCards quiz file, it changes what you ask for, copies every other question word for word so NeuroCards keeps their review history, and returns the whole file

## When to Use

When the user asks for multiple choice questions, a multiple choice quiz, or a NeuroCards quiz file from source material, or for changes to a NeuroCards quiz file they already have. For flashcards, use `flashcard-generator`.

## How It Works

1. **Read** the source file (PDF or Markdown) thoroughly
2. **Verify** accuracy against the named specification: confident fixes go in the quiz, doubtful content is left out, simplifications are kept, and every fix, omission or flag is reported in chat as release-note bullets (never in the file)
3. **Identify** key content: definitions, laws, equations, units, standard values, conditions, named processes, and common explain/justify points
4. **Write** questions covering all key content
5. **Check** the finished quiz for questions that are only true in context and wrong choices that are arguably correct
6. **Format** the quiz as a NeuroCards quiz file in TOML

Instructions the user gives with the material set the scope.

## Question Types

- **Definition**: What is X?
- **Recall**: Single facts, values, units, equations
- **Single-step application**: One calculation step, with wrong choices from the usual slips
- **Cause and effect**: What happens to one quantity when another changes, under a stated condition
- **Classification**: Which of these are X, all that apply

## Output Format

A TOML file named after the source: a `name` line, then one `[[questions]]` table per question with `question`, `correct`, `wrong` and an optional `explanation`. Text with a backslash or a double quote goes between `'''` quotes. Import it in NeuroCards on a quiz's page (Import questions) or from the library's import.

```toml
name = "Cell biology"

[[questions]]
question = "Which organelle releases most of a cell's energy by aerobic respiration?"
correct = ["Mitochondrion"]
wrong = ["Ribosome", "Golgi body", "Nucleus"]

[[questions]]
question = "Which of these are found in plant cells but not animal cells?"
correct = ["Cell wall", "Chloroplasts"]
wrong = ["Ribosomes", "Cell membrane"]
explanation = "Animal cells have ribosomes and a membrane too, but no wall or chloroplasts."
```

## Editing a Quiz

Give it a quiz file exported from NeuroCards (the quiz's **⋯** menu, **Export quiz**) and say what to change. It keeps the `name` and `description` lines, copies every question it does not change word for word and in place, writes new and edited questions to the same rules, and returns the whole file with its changes listed in chat as `Added:`, `Edited:` and `Removed:` bullets. Bring it back with **Update from file** in the same menu: questions it kept the same keep their review history, and a question whose choices only moved counts as unchanged.

## Sample Prompts

**claude.ai:**

```
Create a multiple choice quiz from the attached study materials using the "quiz-generator" skill.
Output the quiz as a .toml file named after the source file (e.g. Physics_Chapter_5.pdf → Physics_Chapter_5.toml).
```

**Editing a quiz on claude.ai:**

```
Using the "quiz-generator" skill, add questions for anything in the attached notes this quiz misses, and replace any wrong choice that is too easy to rule out.
Return the whole quiz file with the same name.
```

**Claude API:**

```
Create a multiple choice quiz from the attached study materials using the "quiz-generator" skill.
Output only the TOML quiz file, with no preamble, explanations or code fences.
The raw output will be saved directly to a .toml file.
```

## Installation

Place the `SKILL.md` file in your Claude skills directory:

```
skills/
└── quiz-generator/
    └── SKILL.md
```

Then trigger by asking for multiple choice questions from source material.

## Version Notes

- **quiz-v1.2**: Editing a Quiz: given a quiz file the user already has, change what they ask for, keep the name and description lines, copy unchanged questions word for word and in place so NeuroCards keeps their review history, and return the whole file with the changes listed as `Added:`, `Edited:` and `Removed:` bullets. Matches the rules of Ask through your own AI service in NeuroCards (service-5)
- **quiz-v1.1**: Several correct choices are asked for by the question's own wording, with no "(Select all that apply)" note in the question, since NeuroCards shows one when a question has more than one correct choice
- **quiz-v1.0**: The Quizzes section of `flashcard-generator` v3.11 as a skill of its own, so the card rules no longer apply to questions. Rules match the prompt NeuroCards generates quizzes with (October 2026): one idea per question, believable wrong choices of the same form as the correct one, two to four choices, all that apply only when the question says so, short choices, an explanation only where the answer is not its own, and a final check for unstated context and arguably correct wrong choices. Not yet run as a skill on its own; the rules were tested as the NeuroCards prompt, which returns JSON
