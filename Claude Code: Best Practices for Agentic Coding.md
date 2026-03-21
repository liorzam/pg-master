── Page 1 ──

# Claude Code: Best Practices for Agentic Coding

**By Boris Cherny and the Claude Code Team at Anthropic**

*A comprehensive guide to getting the most out of Claude Code, from configuring your environment to scaling across parallel sessions.*

---

## Preface

Claude Code is an agentic coding environment. Unlike a chatbot that answers questions and waits, Claude Code can read your files, run commands, make changes, and autonomously work through problems while you watch, redirect, or step away entirely.

This changes how you work. Instead of writing code yourself and asking Claude to review it, you describe what you want and Claude figures out how to build it. Claude explores, plans, and implements.

But this autonomy still comes with a learning curve. Claude works within certain constraints you need to understand.

This guide covers patterns that have proven effective across Anthropic's internal teams and for engineers using Claude Code across various codebases, languages, and environments.

---

## Chapter 1: The Fundamental Constraint

Most best practices in this guide are based on one constraint: Claude's context window fills up fast, and performance degrades as it fills.

Claude's context window holds your entire conversation, including every message, every file Claude reads, and every command output. However, this can fill up fast. A single debugging session or codebase exploration might generate and consume tens of thousands of tokens.

This matters because LLM performance degrades as context fills. When the context window is getting full, Claude may start "forgetting" earlier instructions or making more mistakes. The context window is the most important resource to manage. Track context usage continuously with a custom status line.

The key insight behind all of these best practices: intelligence is not the bottleneck -- context is. Every organization has unique workflows, standards, and knowledge systems, and Claude does not inherently know any of these. The key to making Claude successful is giving it the right context.

---

## Chapter 2: Give Claude a Way to Verify Its Work

**This is the single highest-leverage thing you can do.**

Include tests, screenshots, or expected outputs so Claude can check itself. Claude performs dramatically better when it can verify its own work -- running tests, comparing screenshots, and validating outputs.

Without clear success criteria, Claude might produce something that looks right but actually does not work. You become the only feedback loop, and every mistake requires your attention.

### Verification Strategies

**Provide verification criteria:**
- Before: "implement a function that validates email addresses"
- After: "write a validateEmail function. example test cases: user@example.com is true, invalid is false, user@.com is false. run the tests after implementing"

**Verify UI changes visually:**
- Before: "make the dashboard look better"
- After: "[paste screenshot] implement this design. take a screenshot of the result and compare it to the original. list differences and fix them"

**Address root causes, not symptoms:**
- Before: "the build is failing"
- After: "the build fails with this error: [paste error]. fix it and verify the build succeeds. address the root cause, don't suppress the error"

Your verification can also be a test suite, a linter, or a Bash command that checks output. Invest in making your verification rock-solid.

Internal teams at Anthropic report a 2-3x improvement in output quality when Claude can check its own work, stemming from tighter feedback loops.

---

## Chapter 3: Explore First, Then Plan, Then Code

Separate research and planning from implementation to avoid solving the wrong problem. Use Plan Mode to separate exploration from execution.

The recommended workflow has four phases:

### Phase 1: Explore

Enter Plan Mode. Claude reads files and answers questions without making changes.

```
claude (Plan Mode)
read /src/auth and understand how we handle sessions and login.
also look at how we manage environment variables for secrets.
```

### Phase 2: Plan

Ask Claude to create a detailed implementation plan.

```
claude (Plan Mode)
I want to add Google OAuth. What files need to change?
What's the session flow? Create a plan.
```

Press Ctrl+G to open the plan in your text editor for direct editing before Claude proceeds.

### Phase 3: Implement

Switch back to Normal Mode and let Claude code, verifying against its plan.

```
claude (Normal Mode)
implement the OAuth flow from your plan. write tests for the
callback handler, run the test suite and fix any failures.
```

### Phase 4: Commit

Ask Claude to commit with a descriptive message and create a PR.

```
claude (Normal Mode)
commit with a descriptive message and open a PR
```

### When to Skip Planning

Plan Mode is useful, but also adds overhead. For tasks where the scope is clear and the fix is small (like fixing a typo, adding a log line, or renaming a variable) ask Claude to do it directly.

Planning is most useful when you are uncertain about the approach, when the change modifies multiple files, or when you are unfamiliar with the code being modified. If you could describe the diff in one sentence, skip the plan.

---

## Chapter 4: Provide Specific Context in Your Prompts

The more precise your instructions, the fewer corrections you will need. Claude can infer intent, but it cannot read your mind. Reference specific files, mention constraints, and point to example patterns.

### Prompting Strategies

**Scope the task:** Specify which file, what scenario, and testing preferences.
- Before: "add tests for foo.py"
- After: "write a test for foo.py covering the edge case where the user is logged out. avoid mocks."

**Point to sources:** Direct Claude to the source that can answer a question.
- Before: "why does ExecutionFactory have such a weird api?"
- After: "look through ExecutionFactory's git history and summarize how its api came to be"

**Reference existing patterns:** Point Claude to patterns in your codebase.
- Before: "add a calendar widget"
- After: "look at how existing widgets are implemented on the home page to understand the patterns. HotDogWidget.php is a good example. follow the pattern to implement a new calendar widget that lets the user select a month and paginate forwards/backwards to pick a year. build from scratch without libraries other than the ones already used in the codebase."

**Describe the symptom:** Provide the symptom, the likely location, and what "fixed" looks like.
- Before: "fix the login bug"
- After: "users report that login fails after session timeout. check the auth flow in src/auth/, especially token refresh. write a failing test that reproduces the issue, then fix it"

Vague prompts can be useful when you are exploring and can afford to course-correct. A prompt like "what would you improve in this file?" can surface things you would not have thought to ask about.

### Provide Rich Content

You can provide rich data to Claude in several ways:

- **Reference files with @** instead of describing where code lives. Claude reads the file before responding.
- **Paste images directly.** Copy/paste or drag and drop images into the prompt.
- **Give URLs** for documentation and API references. Use /permissions to allowlist frequently-used domains.
- **Pipe in data** by running `cat error.log | claude` to send file contents directly.
- **Let Claude fetch what it needs.** Tell Claude to pull context itself using Bash commands, MCP tools, or by reading files.

---

## Chapter 5: Configure Your Environment

A few setup steps make Claude Code significantly more effective across all your sessions.

### Write an Effective CLAUDE.md

CLAUDE.md is a special file that Claude reads at the start of every conversation. Include Bash commands, code style, and workflow rules. This gives Claude persistent context it cannot infer from code alone.

Run `/init` to generate a starter CLAUDE.md file based on your current project structure, then refine over time.

There is no required format for CLAUDE.md files, but keep it short and human-readable. For example:

```markdown

── Page 2 ──

# Code style
- Use ES modules (import/export) syntax, not CommonJS (require)
- Destructure imports when possible (eg. import { foo } from 'bar')

── Page 3 ──

# Workflow
- Be sure to typecheck when you're done making a series of code changes
- Prefer running single tests, and not the whole test suite, for performance
```

CLAUDE.md is loaded every session, so only include things that apply broadly. For domain knowledge or workflows that are only relevant sometimes, use skills instead. Claude loads them on demand without bloating every conversation.

Keep it concise. For each line, ask: "Would removing this cause Claude to make mistakes?" If not, cut it. Bloated CLAUDE.md files cause Claude to ignore your actual instructions.

#### What to Include vs. Exclude

**Include:**
- Bash commands Claude cannot guess
- Code style rules that differ from defaults
- Testing instructions and preferred test runners
- Repository etiquette (branch naming, PR conventions)
- Architectural decisions specific to your project
- Developer environment quirks (required env vars)
- Common gotchas or non-obvious behaviors

**Exclude:**
- Anything Claude can figure out by reading code
- Standard language conventions Claude already knows
- Detailed API documentation (link to docs instead)
- Information that changes frequently
- Long explanations or tutorials
- File-by-file descriptions of the codebase
- Self-evident practices like "write clean code"

If Claude keeps doing something you do not want despite having a rule against it, the file is probably too long and the rule is getting lost. If Claude asks you questions that are answered in CLAUDE.md, the phrasing might be ambiguous. Treat CLAUDE.md like code: review it when things go wrong, prune it regularly, and test changes by observing whether Claude's behavior actually shifts.

You can tune instructions by adding emphasis (e.g., "IMPORTANT" or "YOU MUST") to improve adherence. Check CLAUDE.md into git so your team can contribute. The file compounds in value over time.

#### CLAUDE.md File Locations

- **Home folder (~/.claude/CLAUDE.md):** Applies to all Claude sessions
- **Project root (./CLAUDE.md):** Check into git to share with your team, or name it CLAUDE.local.md and .gitignore it
- **Parent directories:** Useful for monorepos where both root/CLAUDE.md and root/foo/CLAUDE.md are pulled in automatically
- **Child directories:** Claude pulls in child CLAUDE.md files on demand when working with files in those directories

CLAUDE.md files can import additional files using @path/to/import syntax:

```markdown
See @README.md for project overview and @package.json for available npm commands.

── Page 4 ──

# Additional Instructions
- Git workflow: @docs/git-instructions.md
- Personal overrides: @~/.claude/my-project-instructions.md
```

#### Collective Learning via CLAUDE.md

Each team at Anthropic maintains a CLAUDE.md in git to document mistakes, so Claude can improve over time. During code reviews, team members tag @.claude in pull requests; a GitHub Action automatically adds these learnings to CLAUDE.md, creating persistent organizational memory across all future sessions.

### Configure Permissions

Use /permissions to allowlist safe commands or /sandbox for OS-level isolation. This reduces interruptions while keeping you in control.

By default, Claude Code requests permission for actions that might modify your system: file writes, Bash commands, MCP tools, etc. This is safe but tedious. After the tenth approval you are not really reviewing anymore, you are just clicking through. There are two ways to reduce these interruptions:

- **Permission allowlists:** Permit specific tools you know are safe (like npm run lint or git commit)
- **Sandboxing:** Enable OS-level isolation that restricts filesystem and network access, allowing Claude to work more freely within defined boundaries

Create a shared settings.json file in your codebase to pre-approve common commands and block risky ones, with everyone inheriting the same sensible defaults.

### Use CLI Tools

Tell Claude Code to use CLI tools like gh, aws, gcloud, and sentry-cli when interacting with external services.

CLI tools are the most context-efficient way to interact with external services. If you use GitHub, install the gh CLI. Claude knows how to use it for creating issues, opening pull requests, and reading comments. Without gh, Claude can still use the GitHub API, but unauthenticated requests often hit rate limits.

Claude is also effective at learning CLI tools it does not already know. Try prompts like: "Use 'foo-cli-tool --help' to learn about foo tool, then use it to solve A, B, C."

### Connect MCP Servers

Run `claude mcp add` to connect external tools like Notion, Figma, or your database.

With MCP servers, you can ask Claude to implement features from issue trackers, query databases, analyze monitoring data, integrate designs from Figma, and automate workflows.

### Set Up Hooks

Use hooks for actions that must happen every time with zero exceptions. Hooks run scripts automatically at specific points in Claude's workflow. Unlike CLAUDE.md instructions which are advisory, hooks are deterministic and guarantee the action happens.

Claude can write hooks for you. Try prompts like "Write a hook that runs eslint after every file edit" or "Write a hook that blocks writes to the migrations folder." Run /hooks for interactive configuration, or edit .claude/settings.json directly.

### Create Skills

Create SKILL.md files in .claude/skills/ to give Claude domain knowledge and reusable workflows. Skills extend Claude's knowledge with information specific to your project, team, or domain. Claude applies them automatically when relevant, or you can invoke them directly with /skill-name.

```markdown

── Page 5 ──

# .claude/skills/api-conventions/SKILL.md
---
name: api-conventions
description: REST API design conventions for our services
---

── Page 6 ──

# API Conventions
- Use kebab-case for URL paths
- Use camelCase for JSON properties
- Always include pagination for list endpoints
- Version APIs in the URL path (/v1/, /v2/)
```

Skills can also define repeatable workflows you invoke directly:

```markdown

── Page 7 ──

# .claude/skills/fix-issue/SKILL.md
---
name: fix-issue
description: Fix a GitHub issue
disable-model-invocation: true
---
Analyze and fix the GitHub issue: $ARGUMENTS.

1. Use `gh issue view` to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix
6. Ensure code passes linting and type checking
7. Create a descriptive commit message
8. Push and create a PR
```

### Create Custom Subagents

Define specialized assistants in .claude/agents/ that Claude can delegate to for isolated tasks. Subagents run in their own context with their own set of allowed tools. They are useful for tasks that read many files or need specialized focus without cluttering your main conversation.

```markdown

── Page 8 ──

# .claude/agents/security-reviewer.md
---
name: security-reviewer
description: Reviews code for security vulnerabilities
tools: Read, Grep, Glob, Bash
model: opus
---
You are a senior security engineer. Review code for:
- Injection vulnerabilities (SQL, XSS, command injection)
- Authentication and authorization flaws
- Secrets or credentials in code
- Insecure data handling

Provide specific line references and suggested fixes.
```

Tell Claude to use subagents explicitly: "Use a subagent to review this code for security issues."

---

## Chapter 6: Communicate Effectively

The way you communicate with Claude Code significantly impacts the quality of results.

### Ask Codebase Questions

When onboarding to a new codebase, use Claude Code for learning and exploration. You can ask Claude the same sorts of questions you would ask another engineer:

- How does logging work?
- How do I make a new API endpoint?
- What does `async move { ... }` do on line 134 of foo.rs?
- What edge cases does CustomerOnboardingFlowImpl handle?
- Why does this code call foo() instead of bar() on line 333?

Using Claude Code this way is an effective onboarding workflow, improving ramp-up time and reducing load on other engineers. New employees report reducing research time by 80% -- tasks taking an hour now consume 10-20 minutes. No special prompting required: ask questions directly.

### Let Claude Interview You

For larger features, have Claude interview you first. Start with a minimal prompt and ask Claude to interview you using the AskUserQuestion tool. Claude asks about things you might not have considered yet, including technical implementation, UI/UX, edge cases, and tradeoffs.

```
I want to build [brief description]. Interview me in detail using the
AskUserQuestion tool.

Ask about technical implementation, UI/UX, edge cases, concerns, and
tradeoffs. Don't ask obvious questions, dig into the hard parts I
might not have considered.

Keep interviewing until we've covered everything, then write a
complete spec to SPEC.md.
```

Once the spec is complete, start a fresh session to execute it. The new session has clean context focused entirely on implementation, and you have a written spec to reference.

---

## Chapter 7: Manage Your Session

Conversations are persistent and reversible. Use this to your advantage.

### Course-Correct Early and Often

The best results come from tight feedback loops. Though Claude occasionally solves problems perfectly on the first attempt, correcting it quickly generally produces better solutions faster.

- **Esc:** Stop Claude mid-action with the Esc key. Context is preserved, so you can redirect.
- **Esc + Esc or /rewind:** Press Esc twice or run /rewind to open the rewind menu and restore previous conversation and code state, or summarize from a selected message.
- **"Undo that":** Have Claude revert its changes.
- **/clear:** Reset context between unrelated tasks. Long sessions with irrelevant context can reduce performance.

If you have corrected Claude more than twice on the same issue in one session, the context is cluttered with failed approaches. Run /clear and start fresh with a more specific prompt that incorporates what you learned. A clean session with a better prompt almost always outperforms a long session with accumulated corrections.

### Manage Context Aggressively

Claude Code automatically compacts conversation history when you approach context limits, which preserves important code and decisions while freeing space.

During long sessions, Claude's context window can fill with irrelevant conversation, file contents, and commands. This can reduce performance and sometimes distract Claude.

- Use /clear frequently between tasks to reset the context window entirely
- When auto compaction triggers, Claude summarizes what matters most, including code patterns, file states, and key decisions
- For more control, run /compact with instructions, like `/compact Focus on the API changes`
- To compact only part of the conversation, use Esc + Esc or /rewind, select a message checkpoint, and choose Summarize from here. This condenses messages from that point forward while keeping earlier context intact.
- Customize compaction behavior in CLAUDE.md with instructions like "When compacting, always preserve the full list of modified files and any test commands" to ensure critical context survives summarization

### Use Subagents for Investigation

Since context is your fundamental constraint, subagents are one of the most powerful tools available. When Claude researches a codebase it reads lots of files, all of which consume your context. Subagents run in separate context windows and report back summaries:

```
Use subagents to investigate how our authentication system handles token
refresh, and whether we have any existing OAuth utilities I should reuse.
```

The subagent explores the codebase, reads relevant files, and reports back with findings -- all without cluttering your main conversation.

You can also use subagents for verification after Claude implements something:

```
use a subagent to review this code for edge cases
```

### Rewind with Checkpoints

Every action Claude makes creates a checkpoint. You can restore conversation, code, or both to any previous checkpoint.

Claude automatically checkpoints before changes. Double-tap Escape or run /rewind to open the rewind menu. You can restore conversation only, restore code only, restore both, or summarize from a selected message.

Instead of carefully planning every move, you can tell Claude to try something risky. If it does not work, rewind and try a different approach. Checkpoints persist across sessions, so you can close your terminal and still rewind later.

### Resume Conversations

Claude Code saves conversations locally. When a task spans multiple sessions, you do not have to re-explain the context:

```bash
claude --continue    # Resume the most recent conversation
claude --resume      # Select from recent conversations
```

Use /rename to give sessions descriptive names ("oauth-migration", "debugging-memory-leak") so you can find them later. Treat sessions like branches. Different workstreams can have separate, persistent contexts.

---

## Chapter 8: Automate and Scale

Once you are effective with one Claude, multiply your output with parallel sessions, headless mode, and fan-out patterns.

### Run Headless Mode

Use `claude -p "prompt"` in CI, pre-commit hooks, or scripts. Add `--output-format stream-json` for streaming JSON output.

With `claude -p "your prompt"`, you can run Claude headlessly, without an interactive session. Headless mode is how you integrate Claude into CI pipelines, pre-commit hooks, or any automated workflow. The output formats (plain text, JSON, streaming JSON) let you parse results programmatically.

```bash

── Page 9 ──

# One-off queries
claude -p "Explain what this project does"

── Page 10 ──

# Structured output for scripts
claude -p "List all API endpoints" --output-format json

── Page 11 ──

# Streaming for real-time processing
claude -p "Analyze this log file" --output-format stream-json
```

### Run Multiple Claude Sessions

Run multiple Claude sessions in parallel to speed up development, run isolated experiments, or start complex workflows.

There are three main ways to run parallel sessions:

- **Claude Desktop:** Manage multiple local sessions visually. Each session gets its own isolated worktree.
- **Claude Code on the web:** Run on Anthropic's secure cloud infrastructure in isolated VMs.
- **Agent teams:** Automated coordination of multiple sessions with shared tasks, messaging, and a team lead.

Beyond parallelizing work, multiple sessions enable quality-focused workflows. A fresh context improves code review since Claude will not be biased toward code it just wrote.

#### Writer/Reviewer Pattern

| Session A (Writer) | Session B (Reviewer) |
|---|---|
| "Implement a rate limiter for our API endpoints" | |
| | "Review the rate limiter implementation in @src/middleware/rateLimiter.ts. Look for edge cases, race conditions, and consistency with our existing middleware patterns." |
| "Here's the review feedback: [Session B output]. Address these issues." | |

You can do something similar with tests: have one Claude write tests, then another write code to pass them.

### Fan Out Across Files

For large migrations or analyses, you can distribute work across many parallel Claude invocations:

**Step 1: Generate a task list.** Have Claude list all files that need migrating (e.g., "list all 2,000 Python files that need migrating").

**Step 2: Write a script to loop through the list.**

```bash
for file in $(cat files.txt); do
  claude -p "Migrate $file from React to Vue. Return OK or FAIL." \
    --allowedTools "Edit,Bash(git commit *)"
done
```

**Step 3: Test on a few files, then run at scale.** Refine your prompt based on what goes wrong with the first 2-3 files, then run on the full set. The --allowedTools flag restricts what Claude can do, which matters when you are running unattended.

You can also integrate Claude into existing data/processing pipelines:

```bash
claude -p "<your prompt>" --output-format json | your_command
```

Use --verbose for debugging during development, and turn it off in production.

---

## Chapter 9: How Anthropic Teams Use Claude Code

Anthropic's internal teams have developed specific patterns from daily usage across ten departments.

### Autonomous Execution for Peripheral Features

Teams use "auto-accept mode" for rapid prototyping on product edges. The Claude Code team reports their Vim mode implementation came 70% from autonomous work. Save clean git states, review 80% complete solutions, then refine. Success rate: approximately one-third on first autonomous attempt.

### Synchronous Collaboration for Critical Features

For features touching core business logic, teams work synchronously with Claude Code, providing detailed prompts with specific implementation instructions. This hybrid model preserves engineering judgment while accelerating mechanical coding work.

### Knowledge Extraction and Codebase Navigation

New employees reduce research time by 80% -- tasks taking an hour now consume 10-20 minutes. Claude Code serves as the first stop for understanding dependencies and identifying relevant files.

### Production Metrics

Boris Cherny, Head of Claude Code, reported these metrics for December 2025:
- 259 pull requests
- 497 commits
- Approximately 40,000 lines added
- Approximately 38,000 lines removed
- 30-day period
- 0 human-written lines

He operates five simultaneous Claude Code instances in terminal tabs with system notifications alerting when input is needed, plus 5-10 additional sessions via the web interface, treating Claude as continuously running rather than being opened and closed for discrete tasks.

### Model Selection

Anthropic teams use the most capable model (Opus) with thinking mode enabled for all tasks. Despite its larger size and slower response time, it requires less human steering, resulting in net time savings.

---

## Chapter 10: Avoid Common Failure Patterns

These are common mistakes. Recognizing them early saves time.

### The Kitchen Sink Session

You start with one task, then ask Claude something unrelated, then go back to the first task. Context is full of irrelevant information.

**Fix:** /clear between unrelated tasks.

### Correcting Over and Over

Claude does something wrong, you correct it, it is still wrong, you correct again. Context is polluted with failed approaches.

**Fix:** After two failed corrections, /clear and write a better initial prompt incorporating what you learned.

### The Over-Specified CLAUDE.md

If your CLAUDE.md is too long, Claude ignores half of it because important rules get lost in the noise.

**Fix:** Ruthlessly prune. If Claude already does something correctly without the instruction, delete it or convert it to a hook.

### The Trust-Then-Verify Gap

Claude produces a plausible-looking implementation that does not handle edge cases.

**Fix:** Always provide verification (tests, scripts, screenshots). If you cannot verify it, do not ship it.

### The Infinite Exploration

You ask Claude to "investigate" something without scoping it. Claude reads hundreds of files, filling the context.

**Fix:** Scope investigations narrowly or use subagents so the exploration does not consume your main context.

---

## Chapter 11: Develop Your Intuition

The patterns in this guide are not set in stone. They are starting points that work well in general, but might not be optimal for every situation.

Sometimes you should let context accumulate because you are deep in one complex problem and the history is valuable. Sometimes you should skip planning and let Claude figure it out because the task is exploratory. Sometimes a vague prompt is exactly right because you want to see how Claude interprets the problem before constraining it.

Pay attention to what works. When Claude produces great output, notice what you did: the prompt structure, the context you provided, the mode you were in. When Claude struggles, ask why. Was the context too noisy? The prompt too vague? The task too big for one pass?

Over time, you will develop intuition that no guide can capture. You will know when to be specific and when to be open-ended, when to plan and when to explore, when to clear context and when to let it accumulate.

---

## Key Takeaways

1. **Context is the bottleneck, not intelligence.** Manage your context window as your most precious resource.
2. **Verification is the highest-leverage practice.** Give Claude tests, screenshots, or expected outputs so it can check itself.
3. **Explore, plan, then implement.** Separate research from execution to avoid solving the wrong problem.
4. **Be specific in your prompts.** Reference files, mention constraints, and point to example patterns.
5. **Keep CLAUDE.md concise and actionable.** Only include what Claude cannot figure out on its own.
6. **Use subagents to protect context.** Delegate investigation to subagents so exploration does not consume your main conversation.
7. **Course-correct early.** A clean session with a better prompt beats a long session with accumulated corrections.
8. **Scale horizontally.** Use parallel sessions, headless mode, and fan-out patterns to multiply output.
9. **Treat Claude as a system, not a chatbot.** Parallel, continuous, and self-verifying usage produces dramatically better results than single-threaded request-response.
10. **Develop your own intuition.** These patterns are starting points. Pay attention to what works for your specific codebase and workflow.

---

*Source: Anthropic Engineering Blog -- "Claude Code: Best Practices for Agentic Coding" and the official Claude Code documentation at code.claude.com/docs. Content synthesized from Anthropic's published best practices, internal team usage patterns, and Boris Cherny's documented workflows.*
