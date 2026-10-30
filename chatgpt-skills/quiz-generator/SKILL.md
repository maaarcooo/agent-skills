---
name: quiz-generator
description: Generate multiple choice quizzes for NeuroCards from PDF or Markdown study materials. Use when the user asks for multiple choice questions, a multiple choice quiz, or a NeuroCards quiz file from source material, or for changes to a NeuroCards quiz file they already have. Outputs a NeuroCards quiz file in TOML.
---

# Quiz Generator

Generate multiple choice questions from PDF or Markdown content, as a quiz file NeuroCards imports. Each question tests one idea, is quick to answer, and is fair to someone who knows the topic. The learner answers by picking from the choices, which are shown in a new order each time.

## Process

1. Read the source file (PDF or Markdown) thoroughly
2. Verify accuracy. Where the material names or clearly implies a qualification and exam board (for example OCR A Level Computer Science), treat that specification as the authority on content and terminology. Where the source is wrong and you are confident of the correct version, use the corrected version and list the fix in the chat response. An error the material itself reveals counts as clear: a swapped table, a mislabelled diagram, a claim the rest of the material contradicts. Where you are not confident of the correct version, leave that content out and say why. Never mark a choice correct that you believe to be wrong, and never silently rewrite source content. Keep the source's wording only where it is a legitimate syllabus-level simplification, and flag rather than add content the specification does not require
3. Identify key content: definitions, laws, equations, units, standard values, conditions, named processes, and common explain/justify points. Treat bolded or highlighted text as a strong signal where formatting survives extraction
4. Write questions covering all key content of the material
5. Reread the finished quiz and fix exactly two things: a question that is only true with context it does not state, and a wrong choice that is arguably correct (see Final Check)
6. Output the quiz file (see Output Format)

If the user gives instructions with the material (for example restricting to one topic), follow them. The coverage and exclusions then apply to the selected scope only.

If the user gives a quiz file they already have and asks for changes, see Editing a Quiz.

## Question Style

- **One idea per question.** The question makes sense and has one clear answer before the choices are read. Not "Which of these is true?", but "What does the mitochondrion release energy from?"
- **Believable wrong choices.** Each wrong choice is a mistake a learner makes: a related term, a common misconception, a value with a typical slip. Each has the same length, form and level of detail as the correct one, so the correct choice does not stand out, and each is plainly wrong to someone who knows the topic
- **No "all of the above" or "none of the above"**, since the choices are reordered. Never ask "which of these is NOT"
- **Two to four choices, four by default.** Use fewer only when there are not three believable wrong choices
- **More than one correct choice only when the question's wording asks for several**, as in "Which of these are found in plant cells but not animal cells?". Never add "(Select all that apply)" or a count of correct choices to the question, since NeuroCards says so itself when a question has more than one. Never mark a choice correct that is only partly true, and never write two choices that mean the same
- **Short choices.** A word, a phrase, a value with its unit, or one sentence. Never more than 500 characters
- **Explanation.** One or two sentences on why the correct choice is correct and, where it helps, why the most tempting wrong choice is wrong. Leave it out for plain recall, where the answer is its own explanation
- **True as phrased.** Every question is true read on its own, with no page around it. Name in the question the condition the answer depends on: "What happens to the internal energy of an insulated gas when it expands?", not "What happens to the internal energy of a gas when it expands?"
- **Punctuation.** No em-dashes or semicolons in a question, choice or explanation. Use a comma, a colon before a list of values, or two sentences
- **Plain language.** Simple, direct wording. Match the source's syllabus level
- **LaTeX for equations.** Write all mathematical expressions using standard LaTeX notation: inline with `$...$` and display with `$$...$$`. For example, `$E_k = \frac{1}{2}mv^2$` rather than `Ek = ½mv²`

## Question Types

Pick the best fit per piece of knowledge:

- **Definition**: What is X?
- **Recall**: single facts, values, units, equations
- **Single-step application**: a value calculated in one step, with wrong choices from the usual slips, such as a forgotten square or a wrong unit. Never multi-step
- **Cause and effect**: what happens to one quantity when another changes, under a stated condition
- **Classification**: which of these are X, all that apply

## Final Check

After writing the quiz, check for two failure modes only:

1. **Unstated context**: a question that is only true with context it does not state. Name the condition in the question (see True as phrased)
2. **Arguably correct wrong choices**: a wrong choice that someone who knows the topic could defend. Replace it

## Exclusions

Do not create:

- Questions requiring a diagram or visual to answer
- Multi-step calculations
- Trick questions that turn on a word
- Questions for constants and data values the exam supplies
- Questions for content the source does not adequately explain

## Output Format

A TOML file named after the source file, ending `.toml` (e.g. Physics_Chapter_5.pdf → Physics_Chapter_5.toml):

- The first line is `name = "..."`, a short name for the quiz taken from the material, at most 50 characters
- Then one `[[questions]]` table per question, with `question`, `correct` (a list of the correct choices), `wrong` (a list of the wrong choices) and, where there is one, `explanation`
- Write each text between double quotes. A text with a backslash, such as LaTeX, or with a double quote goes between triple single quotes instead: `'''$E_k = \frac{1}{2}mv^2$'''`
- No comments or other text in the file
- Accuracy flags go in the chat response, never in the file, as a bullet list in the manner of release notes: one line per item, each starting with `Fixed:`, `Skipped:`, `Kept:` or `Flagged:`, the point stated directly, no paragraphs

**Example output:**

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

[[questions]]
question = "What is the kinetic energy of a 2 kg mass moving at 3 m/s?"
correct = ['''$9\;\text{J}$''']
wrong = ['''$6\;\text{J}$''', '''$18\;\text{J}$''', '''$3\;\text{J}$''']
explanation = '''$E_k = \frac{1}{2}mv^2 = \frac{1}{2} \times 2 \times 3^2 = 9\;\text{J}$'''
```

## Editing a Quiz

When the user gives a NeuroCards quiz file they already have, pasted or attached, and asks for changes, such as new questions, better wrong choices or questions taken out:

- Change what the user asks for, and mistakes you are confident of (see Process step 2). Leave everything else as it is
- Keep the `name` line, and a `description` line if there is one, exactly as they are
- Copy every question you are not changing word for word and in its place, so NeuroCards can tell what changed and keep each question's review history. The order of a question's choices does not matter
- Leave out the questions the user asks to remove
- New and edited questions follow Question Style, Question Types and Exclusions, and get the Final Check
- Output the whole quiz file, not only the changed questions, under the same file name and in the same format: TOML as in Output Format, or JSON with the same fields where the file was JSON
- In the chat response, list the changes in the manner of release notes: one line per change, each starting with `Added:`, `Edited:` or `Removed:`, after any accuracy flags

The user brings the file back with Update from file in the quiz's menu in NeuroCards, which shows every change before it is made.
