# Build a skill that activates reliably

Developers apply privacy rules when explicitly asked, but omit them when asked to make a notification more useful. The policy exists; Claude does not reliably apply it.

## Task

1. Read the authoritative message policy.
2. Write the skill’s activation description, policy lookup, steps and output contract.
3. Implement the backing message check.
4. Test direct, paraphrased and misleading requests in fresh sessions.
5. Verify that safe useful messages still succeed.

## Working rules

Edit only the designated skill assets and checker. The replay runtime stages a sanitized skill-only plugin; candidate hooks, extra agents and arbitrary plugins are rejected. Checker code executes outside the credential-bearing model client. Prompts cannot grant new tools.

## Acceptance targets

- Direct and indirect notification requests activate the policy.
- Forbidden clinical content is rejected without blocking neutral messages.
- The checker is executable and matches the policy.
- A fresh session produces useful privacy-preserving output.

## How to submit

1. Fork this repository to your personal GitHub account. Use fictional information
   only, keep the fork public, and keep `main` as its default branch.
2. Clone your fork and enable GitHub Actions in its **Actions** tab. Use **Connect
   GitHub for scoring** on the lab page to install the read-only grading App on your
   fork. Return to the lab and use **Recheck connection** if needed.
3. Download the TensorLearn client from the lab page and extract it outside your fork.
   Follow the page's connection command from your fork folder. Paste your learner
   token only into the hidden terminal prompt. Run `/load` from your fork folder to
   connect the repository. Run `/load` again after the facilitator launches the round.
4. Create a branch such as `skills/first-draft` and complete the task above. Change
   only the files the task allows; any other change makes the submission invalid.
5. Commit and push your branch. Open a PR **inside your own fork**, targeting its
   `main` branch. GitHub may suggest the upstream repository: select your fork instead.
   Opening or updating this PR starts the checks automatically.
6. Keep the PR open. When ready, run **`/submit`** in the connected Claude Code session
   to submit that exact pushed revision for the current round. **No merge is required.**
7. Use `/check` or refresh the lab page to read the saved result. The Actions run and
   `lab-score/score.json` are useful feedback, but an Actions check alone does not
   submit your work or prove that the server saved a score.

A change to your answer requires a new commit, push, and explicit `/submit`. Keep
`main` unchanged: the grader compares your PR with the released starter.

## What is graded

The pinned grader in `.github/workflows/grade.yml` reads your files as data and runs
your code only in an isolated, offline container. Every check is a fixed rule; no AI
model judges your work. Your facilitator separately reviews clarity and reasoning.

- 19 messages checked by running your checker: neutral messages allowed, clinical details blocked.
- Failing a core privacy case caps the score at 25/100.
