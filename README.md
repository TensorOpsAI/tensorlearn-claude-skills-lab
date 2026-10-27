# Build a skill that activates reliably

Developers apply privacy rules when explicitly asked, but omit them when asked to make a notification more useful. The policy exists; Claude does not reliably apply it.

The policy is `policy.md`. A Claude Code skill is a folder with a `SKILL.md` file: its `description` tells Claude when the skill applies, and Claude loads the skill by itself when a request matches. This lab's skill is `.claude/skills/notification-policy/SKILL.md`, and it should tell Claude to run the backing checker, `checker/check-message.mjs`, on any message it drafts.

The sensitive terms in `policy.md` are matched as listed (ignoring letter case), so “positive result” is one phrase. The fixture message “The result is positive. Your doctor recommends metformin.” is blocked because it names `metformin`.

**What you'll practise:** writing a Claude Code skill whose description makes it activate for direct and indirect requests, backed by an executable check, and confirming in fresh sessions that it fires.

**In the course slides:** Chapter 1, Lesson 5 (Skills): the same patient-data policy as a skill, tested by asking in different words. The slides back the skill with a hook; here the deterministic check is your `checkMessage` function.

## Task

1. Read the authoritative message policy.
2. Write the skill’s activation description, policy lookup, steps and output contract in `.claude/skills/notification-policy/SKILL.md`, replacing both `TODO`s.
3. Implement the backing message check in `checker/check-message.mjs`, following the checker contract at the end of `policy.md`.
4. Test direct, paraphrased and misleading requests in fresh sessions. The prompts in `fixtures/prompts.json` are a starting set.
5. Verify that safe useful messages still succeed.

**Checking activation.** After editing `SKILL.md`, start a fresh conversation (a new `claude` session in the repository folder, or `/clear`) and send one prompt from `fixtures/prompts.json` at a time, without naming the skill. When the skill activates, Claude Code shows it loading `notification-policy` before answering. You can also run it directly with `/notification-policy`, but the point is that it fires on its own.

Run the checker on the fixture messages, or on one draft (the skill can tell Claude to use the second command):

```sh
node -e "import('./checker/check-message.mjs').then(({checkMessage})=>{for(const {body,allowed} of require('./fixtures/messages.json')){const r=checkMessage({body});console.log(r.allowed===allowed?'ok  ':'FAIL',JSON.stringify(body),r.violations)}})"
node -e "import('./checker/check-message.mjs').then(m=>console.log(JSON.stringify(m.checkMessage({body:process.argv[1]}))))" "An update is available in your secure portal."
```

## Working rules

Edit only the skill folder (`.claude/skills/notification-policy/**`) and `checker/check-message.mjs`. Hooks, extra agents, plugins and settings changes are not part of the answer; adding them makes the submission invalid. The grader runs `checkMessage` in a sandbox without Node APIs (no imports, no `console`, no file access), so embed the policy rules in the checker instead of reading `policy.md` at runtime. Use Node 22; there is no `package.json`.

## Acceptance targets

- Direct and indirect notification requests activate the policy.
- Forbidden clinical content is rejected without blocking neutral messages.
- The checker is executable and matches the policy.
- A fresh session produces useful privacy-preserving output.

## Done when

- The fixture command prints `ok` for all five messages in `fixtures/messages.json`.
- A message with support hours, a support telephone number or a version label is allowed; any clinical term listed in `policy.md` (in any letter case) or a measurement such as `130 mg/dL` is blocked.
- In a fresh session, each prompt in `fixtures/prompts.json` loads the `notification-policy` skill, and the request for the glucose result gets a useful neutral message instead.
- `SKILL.md` has no `TODO` left.
- `git status` shows changes only in `.claude/skills/notification-policy/` and `checker/check-message.mjs`.

## Graded format

- `checkMessage({body})` returns exactly `{allowed, violations}`. `allowed` is a boolean; `violations` is an array of non-empty strings (rule IDs or short reasons, not the clinical text), empty when allowed and non-empty when blocked.
- It never throws. A missing, non-string or blank `body` is not allowed.
- It inspects only `body` (other fields of the input are ignored) and does not change the input.
- `SKILL.md` is reviewed by your facilitator; it is not scored automatically.

## How to submit

1. Fork this repository to your personal GitHub account. Use fictional information
   only, keep the fork public, and keep `main` as its default branch. In your fork's
   **Actions** tab, click **I understand my workflows, go ahead and enable them** if shown.
2. On the lab page, use **Connect GitHub for scoring** to install the read-only grading
   App on your fork, then **Recheck connection**.
3. Download the TensorLearn connector from the lab page and extract it outside your fork.
   From your fork folder, run the page's `--code` connection command and paste your token
   only into the hidden terminal prompt. This opens a Claude Code session connected to the lab.
4. In that session, run `/load`. Run `/load` again after your instructor launches the round.
5. Create a branch such as `skills/first-draft` and complete the task above. Change only the
   files the task allows: any other change, including `CLAUDE.md`, `.claude/` settings or
   agents, or notes files, makes the submission invalid.
6. Commit and push your branch. Open a PR **inside your own fork**, targeting its `main`
   branch (GitHub may suggest the upstream repository: select your fork instead). Check
   that **Check skills submission** starts in the PR's checks. Keep the PR open; no merge
   is required.
7. Run **`/submit`** in the connected Claude Code session, then `/check` (or refresh the
   lab page) to read your saved score. The Actions run is useful feedback, but it does not
   submit your work.

A change to your answer needs a new commit, push and `/submit`. Keep `main` unchanged:
the grader compares your PR with the released starter.

## What is graded

The pinned grader in `.github/workflows/grade.yml` reads your files as data and runs your
code only in an isolated, offline container. Every check is a fixed rule; no AI model
judges your work. Your score is out of 100. Your facilitator separately reviews
clarity and reasoning.

- 19 messages checked by running your checker: neutral messages allowed, clinical details blocked.
- Failing a core privacy case caps the score at 25/100.
- The skill itself (SKILL.md) and whether it activates are reviewed by your facilitator, not scored.
