# Lab 05 — Conclusion

<!--
Read this before you write.

This file is graded by your instructor, by hand, and it is worth 6 of the 15 points.

- Type it yourself. Do not use a chatbot, an AI assistant, an AI autocomplete or a "rewrite my
  text" tool for any part of it, not even for grammar. English that is plain and a little
  clumsy but yours is better than polished text that is not.
- Use your own numbers. Every number you quote must be one your own notebook printed. Copy
  the number exactly as it appeared. A number your notebook did not print is marked as wrong.
- Answer in your own words, about 80-200 words per question. A short answer that uses your
  numbers beats a long answer that could have been written by anyone.
- Delete these comment lines if you like. Keep the three headings and the last line.
-->

## 1. Which layer changed your results the most?

Give the numbers before -> after for that layer (correct / wrong / no answer, out of 20) and say
whether it changed what the agent *can do* or only what it *admits*. If no layer changed the
number of correct answers, say so and explain what the layers did change instead.

(Layer 1, which sends tool errors back to the model, changed the results the most. The bare agent had 16 correct, 4 wrong and 4 no answer out of 24; with layer 1 it had 18 correct, 6 wrong and 0 no answer. It changed what the agent can do: q23 and q24 became correct because the model read the error, called list_regions and tried again. It also changed what the agent admits, in the wrong direction: the four alias questions that used to stop the program now produce answers, and q21 and q22 are wrong. Layer 2 changed nothing: 18/6/0, 62 model calls and 48708 tokens, identical to layer 1. Layer 3 only changed what the agent admits. Correct stayed at 18, wrong fell from 6 to 4 and no answer rose from 0 to 2, because q18 and q22 became UNVERIFIED.)

## 2. One failure the full harness did not fix

Pick one question that the full harness (layer 3) still gets wrong or refuses. Give its id
(q01 ... q20). Open its trace in the notebook: at which step did it go wrong, and what did
the model do instead? Name one thing you would add to the harness, and what it would cost
(extra model calls, tokens, time, or a new way to fail).

(q17 asks by how many percentage points the unemployment rate in Aktobe fell between 2020 and 2023. With all three layers on, the agent still answers 8.89, which is wrong. The trace shows that get_series returned 4.5 for 2020 and 4.1 for 2023. The model then sent (4.5 - 4.1) / 4.5 * 100 to calculate, which is a percent change, and wrote ANSWER: 8.89. The question needs the plain difference, 0.4. The grounding check passed because 8.89 came from a calculate output. One addition is a second model call that compares the expression with the wording of the question before the answer is accepted. It costs at least one more model call per arithmetic question. Layer 3 already raised model calls from 62 to 74 and tokens from 48708 to 60116, and the new check could also reject correct percent-change expressions such as q12.)

## 3. Workflow or agent for the monthly ranking?

Dala Insight wants to publish, every month, "which region is highest and which is lowest on one
indicator in one year". Would you ship the fixed workflow or the agent? Use the accuracy, the
number of model calls, the tokens and the time you measured in Part 1. Then say in one sentence
what kind of question would make you choose the other one.

(I would use the fixed workflow. On the 15 fixed-shape questions in Part 1 it got 13 correct, 0 wrong and 2 no answer, with 15 model calls, 3852 tokens and 16.0 s. The bare agent got 9 correct, 2 wrong and 4 no answer, with 26 model calls, 19835 tokens and 49.6 s. On ranking questions the agent picked the wrong end of a list sorted highest first (q08 and q11), and the workflow got both right. The workflow's two failures, q21 and q22, were old region names, which a monthly report with fixed inputs does not have. The agent is the better choice for open questions that need several steps, such as a percent change between two years (q12 to q15) or an unknown region name. The workflow has no loop, so it cannot attempt those 9 questions.)

---

I wrote this file myself, without any AI tool.
