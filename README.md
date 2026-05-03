# Agentic Engineering Sharing

**Presentation deck:** https://jtz18.github.io/agentic-engineering-sharing/

*A practical field guide to AI agentic coding, skills, plugins, subagents, and workflow design.*

This README-style article was prepared as a companion resource for a talk on **AI agentic coding** — how to think about coding agents, how they use tools, why skills/plugins matter, and how to start building your own repeatable agent workflows.

The core thesis:

> The leverage is no longer just “prompt the model harder.” The leverage is designing workflows, tools, skills, constraints, and feedback loops that let agents repeatedly do useful work.

---

## Contents

- [1. What is a coding agent?](#1-what-is-a-coding-agent)
- [2. The agent stack: model + harness](#2-the-agent-stack-model--harness)
- [3. Tools: how agents do things](#3-tools-how-agents-do-things)
- [4. MCPs, CLIs, and the context bloat problem](#4-mcps-clis-and-the-context-bloat-problem)
- [5. Skills: SOPs for agents](#5-skills-sops-for-agents)
- [6. Plugins: bundled workflow systems](#6-plugins-bundled-workflow-systems)
- [7. Agentic coding as an SDLC workflow](#7-agentic-coding-as-an-sdlc-workflow)
- [8. Subagent-driven development](#8-subagent-driven-development)
- [9. Building your own skills and plugins](#9-building-your-own-skills-and-plugins)
- [10. Plugins and repos worth exploring](#10-plugins-and-repos-worth-exploring)
- [11. Personal workflow examples](#11-personal-workflow-examples)
  - [11.1 Daily health monitoring skill](#111-daily-health-monitoring-skill)
  - [11.2 Daily Twitter/X research skill](#112-daily-twitterx-research-skill)
  - [11.3 Shopping research orchestrator skill](#113-shopping-research-orchestrator-skill)
  - [11.4 Salesforce business relations sales skills](#114-salesforce-business-relations-sales-skills)
  - [11.5 Corporate banking relationship manager skills](#115-corporate-banking-relationship-manager-skills)
- [12. Karpathy’s autoresearch and the Three-File Contract](#12-karpathys-autoresearch-and-the-three-file-contract)
- [13. The new human skill: problem formulation](#13-the-new-human-skill-problem-formulation)
- [14. Taking the autoresearch pattern beyond ML](#14-taking-the-autoresearch-pattern-beyond-ml)
- [15. Practical tips for working with agents](#15-practical-tips-for-working-with-agents)
- [16. Global skills vs project-scoped skills](#16-global-skills-vs-project-scoped-skills)
- [17. Suggested learning path](#17-suggested-learning-path)
- [18. Mental model summary](#18-mental-model-summary)
- [19. Links and resources](#19-links-and-resources)

---

## 1. What is a coding agent?

At the simplest level, a **coding agent** is an AI system that can take actions inside a development environment on your behalf.

A minimally useful coding agent can usually:

- Read files
- Write files
- Create files
- Delete files
- Search files
- Patch files
- Run commands
- Inspect command outputs
- Modify code
- Iterate based on feedback

A normal chatbot mostly talks.

A coding agent can act.

That action layer is the whole game.

---

## 2. The agent stack: model + harness

A useful shorthand:

```text
Agent = Model + Harness
```

The **model** is the intelligence layer.

The **harness** is the environment around the model that gives it:

- Tools
- Permissions
- Filesystem access
- Command execution
- Context loading
- Memory conventions
- Workflow rules
- Safety boundaries
- Integrations with external systems

Examples:

- `GPT-5.5 + Codex`
- `Opus 4.7 + Claude Code`
- `Gemma 4 + OpenClaw`
- `Gemma 4 + Hermes`

The first two are more developer-focused.

The second two are more personal AI assistant-focused.

The important point: the model alone is not the agent. The agent emerges when the model is placed inside a harness that lets it act.

---

## 3. Tools: how agents do things

Agents become useful when they are connected to tools.

Think of tools as the hands and senses of the agent.

### File-based tools

These allow agents to manipulate the local workspace:

- Read files
- Write files
- Create files
- Delete files
- Search through files
- Apply patches
- Inspect directory structure

This is the minimum tool layer for agentic coding.

### CLI tools

Command-line tools extend agent functionality dramatically.

Instead of creating a special custom agent tool for every possible operation, the agent can use existing CLIs:

- `git`
- `gh`
- `python`
- `node`
- `npm`
- `pytest`
- `playwright`
- cloud provider CLIs
- custom internal tools

This matters because CLIs are already:

- Scriptable
- Composable
- Debuggable
- Familiar to developers
- Easy to wrap in skills

Example:

```bash
pytest tests/test_auth.py
```

or:

```bash
gh pr view 123 --json title,body,files
```

From the agent’s perspective, the CLI becomes a compact interface to a much larger system.

### MCP tools

MCP stands for **Model Context Protocol**.

You can think of MCPs as APIs for agents.

They expose external systems to an agent in a structured way.

Examples of systems MCPs can connect to:

- Databases
- Browsers
- SaaS apps
- CRMs
- Search tools
- Internal company APIs
- External APIs
- Filesystems
- Cloud systems

MCPs are powerful because they standardize the way tools are exposed to agents.

---

## 4. MCPs, CLIs, and the context bloat problem

A common beginner mistake is giving an agent every MCP, every tool, every instruction, and every document upfront.

This creates bloat.

Too much irrelevant context causes:

- Wasted context window space
- Higher token usage
- More distraction
- More irrelevant reasoning paths
- Higher chance of using the wrong tool
- Lower quality decisions over time
- Context rot

### Context rot

**Context rot** happens when the agent’s context window becomes overloaded with stale, irrelevant, or low-signal information.

The agent may technically still “have” the information, but performance degrades because too much junk is competing for attention.

This is why progressive disclosure matters.

Instead of loading everything at the start, you load only what is relevant when it becomes relevant.

### Why CLIs came back into the picture

MCPs can be token-intensive.

If every MCP tool comes with large schemas, long descriptions, and verbose outputs, the agent’s context window gets eaten quickly.

CLIs offer another way to program tools for agents.

A CLI can hide complexity behind a simple command:

```bash
my-tool search --query "latest AI robotics papers"
```

The agent only needs to know:

- What the command does
- When to use it
- What arguments it accepts
- How to interpret the output

That can be much cheaper than loading a huge tool schema into the context window.

---

## 5. Skills: SOPs for agents

A **skill** is like an SOP — a standard operating procedure — for an agent.

It tells the agent how to perform a task in a particular way.

At the simplest level, a skill is just a markdown file that acts like a system prompt for a workflow.

A skill can include:

- Task instructions
- Step-by-step workflows
- Best practices
- Tool usage guidance
- Constraints
- Examples
- Verification steps
- Common pitfalls

A skill is not magic. That is why it is powerful.

It is usually just a structured markdown file that says:

> When this kind of task appears, do it this way.

### Skills can also package scripts

A skill can be one level more powerful than a prompt.

It can reference scripts, CLIs, MCPs, templates, or supporting files.

Example skill instruction:

```markdown
When asked to generate a daily health report:

1. Run `python scripts/import_health_data.py`.
2. Query the SQLite database.
3. Analyze sleep, heart rate, HRV, and exercise load.
4. Generate a markdown report.
5. Recommend whether the user should train hard, train lightly, or rest.
```

A skill can mention specific tools that should be used when activated:

- Use this CLI
- Load this MCP
- Read this markdown file
- Run this Python script
- Follow this output template

So a skill becomes a lightweight workflow package.

---

## 6. Plugins: bundled workflow systems

A **plugin** is a bundle of related skills packaged together.

If one skill is an SOP for one task, a plugin is a collection of SOPs for a broader domain.

Example:

- [Superpowers plugin](https://github.com/obra/superpowers)

Superpowers packages a series of skills for software development.

The important thing about plugins is that they encode workflows, not just isolated prompts.

A good plugin answers:

- What workflow should the agent follow?
- What stages should happen first?
- What artifacts should be produced?
- What checks should happen before coding?
- When should subagents be used?
- What does “done” mean?

---

## 7. Agentic coding as an SDLC workflow

It is useful to be a bit meta and ask:

> How is a software development lifecycle workflow actually executed?

A strong agentic coding workflow should not just say:

```text
Write code.
```

That is too vague.

A better workflow guides the agent through a software development lifecycle.

### 1. Brainstorming

The first stage is clarifying the problem.

This may include:

- Asking clarifying questions
- Understanding requirements
- Exploring the codebase
- Researching libraries
- Identifying constraints
- Finding prior art
- Thinking through edge cases

### 2. Design plan spec

Next, create a higher-level design plan.

This includes:

- Overall structure
- System architecture
- Main components
- Main functionality
- User-facing behavior
- Data flow
- Constraints
- Tradeoffs

### 3. Implementation plan spec

Then, create a more granular implementation plan.

This includes:

- Specific files to modify
- Specific functions or modules to create
- Libraries to use
- Tests to write
- Commands to run
- Migration steps
- Edge cases to handle
- Verification criteria

### 4. Subagent-driven development

Finally, execute the task list in the implementation plan.

This is where a main orchestrator agent can spawn subagents to execute parts of the plan.

Some tasks can run in parallel.

Some tasks are blocking and depend on earlier tasks.

The plan should make those dependencies explicit.

---

## 8. Subagent-driven development

In subagent-driven development, your main chat agent becomes an **orchestrator**.

The orchestrator spawns multiple subprocess agents, each with its own fresh context window.

Each subagent receives:

- A specific task
- Necessary context
- Relevant files or paths
- Constraints
- Expected output format

The subagent then works independently and reports back with a compact summary.

### Why this matters

If one orchestrator agent does everything itself, every code file it reads, every diff it writes, every error it sees, and every intermediate detail builds up in the same context window.

That degrades performance over time.

The orchestrator/subagent pattern reduces context rot.

The orchestrator only keeps track of:

- The overall plan
- Which tasks are done
- Which tasks are blocked
- Summaries from subagents
- Final integration details

The heavy task-specific context stays inside the subagent’s separate context window.

### Benefits

- Parallel processing
- Less context rot
- Better task isolation
- Cleaner orchestration
- Easier debugging
- More scalable workflows
- Better handling of blocking dependencies

### Example

A plan might contain:

```text
Task 1: Add database migration
Task 2: Implement backend endpoint
Task 3: Build frontend form
Task 4: Write integration tests
Task 5: Update documentation
```

Some of these tasks can run in parallel.

Some depend on earlier work.

A good orchestrator can run parallel tasks where possible and sequence blocking tasks where needed.

---

## 9. Building your own skills and plugins

Coming up with your own skills and plugins is not that difficult.

The hard part is not the markdown file.

The hard part is taking a step back and thinking clearly about operational workflows.

Ask:

- What task do I repeat often?
- What steps do I usually follow?
- What tools are involved?
- What mistakes do I want the agent to avoid?
- What decisions do I make along the way?
- What should the final output look like?
- What does “done” mean?
- What should be verified before the agent stops?

A skill is just the codification of that workflow.

A plugin is a bundle of related workflows.

### The basic loop

1. Start a chat with the agent.
2. Guide it through a task manually.
3. Correct it along the way.
4. Once the workflow succeeds, ask it to package the workflow as a skill.
5. Reuse the skill next time with new variables.

Example prompt:

```text
Package the workflow we just did as a skill so you can repeat it next time.
```

---

## 10. Plugins and repos worth exploring

These plugins try to automate or guide SDLC-style agentic coding workflows:

- [Superpowers](https://github.com/obra/superpowers)
- [Get Shit Done / GSD](https://github.com/gsd-build/get-shit-done)
- [BMAD — Breakthrough Method for Agile Development](https://github.com/bmad-code-org/BMAD-METHOD)
- [oh-my-claude-code](https://github.com/yeachan-heo/oh-my-claudecode)

The useful exercise is not only using them.

Study how they structure agent behavior.

Look at how they divide the lifecycle into stages, artifacts, commands, and review loops.

Skills are not only for software development either.

Other examples:

- [Trader Monty Claude Trading Skills](https://github.com/tradermonty/claude-trading-skills)
- [gstack by Garry Tan](https://github.com/garrytan/gstack)

Garry Tan’s `gstack` is interesting because it packages startup-building knowledge, office-hours-style guidance, design consults, and review patterns into a plugin.

That makes a version of YC-level guidance more accessible to anyone who can run the workflow.

---

## 11. Personal workflow examples

The best way to understand skills is to look at concrete examples.

### 11.1 Daily health monitoring skill

This skill turns raw personal health data into a daily recovery report.

#### Setup

The agent was given the path to an iCloud database where health data is stored.

The task was to perform WHOOP-band-level data science and analysis on:

- Sleep
- Heart rate
- Exercise
- Recovery
- Training readiness

#### What the agent built

The agent created:

- A Python script
- A SQLite database
- A data import workflow
- A daily report generation workflow

The SQLite database became tiny local infrastructure for analyzing the health data.

#### Output

The skill creates a daily health report that helps answer:

- How recovered am I today?
- How hard should I train?
- Should I exercise lightly?
- Should I rest?
- Are there signs of fatigue?

Example report:

```markdown
# Daily Health Report

## Recovery Summary

- Sleep quality: Good
- Resting heart rate: Normal
- Training load: Moderate
- Recovery status: Slightly under-recovered

## Recommendation

Do light Zone 2 cardio or mobility work today. Avoid maximal effort strength training.

## Notes

Sleep duration was acceptable, but elevated resting heart rate suggests accumulated stress.
```

---

### 11.2 Daily Twitter/X research skill

This skill creates a daily research digest from X/Twitter.

#### Interest areas

The agent was told to focus on AI research posts about:

- Robotics
- Computer vision
- Frontier labs
- Open-source models
- Agentic coding

#### Authentication workflow

The agent was asked to check whether it could access browser cookies from an already logged-in X account.

It extracted session login variables:

- `ct0_token`
- `auth_token`

Those tokens were then reused to scrape posts and summarize them daily.

Important: do not expose real tokens in logs, public repos, screenshots, or shared docs.

#### Output

Example daily digest:

```markdown
# Daily AI Research Digest

## Robotics

- Summary of notable robotics posts
- Links to original posts
- Why it matters

## Computer Vision

- Summary of CV model releases or papers
- Practical implications

## Frontier Labs

- Updates from major labs
- Notable claims and caveats

## Open-Source Models

- New model releases
- Benchmarks
- Community reactions

## Agentic Coding

- New tools
- Workflow examples
- Interesting demos
```

---

### 11.3 Shopping research orchestrator skill

This workflow decomposes shopping research across multiple platform-specific skills.

#### Platforms

The target platforms were:

- AliExpress
- Shopee
- Carousell
- Lazada

#### Step 1: Build individual platform skills

For each platform, a separate chat session was used.

The agent was instructed:

```text
Use the Playwright CLI to access the browser, go to the website, and search for this product.
```

Once it successfully searched a platform, the agent was told:

```text
Package the workflow and what we have done as a [platform] search skill.
```

This created separate skills:

- AliExpress search skill
- Shopee search skill
- Carousell search skill
- Lazada search skill

#### Step 2: Build the orchestrator skill

After all four platform skills existed, a parent skill was created.

The orchestrator skill launches four parallel subagents:

```text
Subagent 1: Search AliExpress
Subagent 2: Search Shopee
Subagent 3: Search Carousell
Subagent 4: Search Lazada
```

Each subagent invokes the relevant shopping skill.

The orchestrator then combines the results into a final comparison.

#### Output

Example report:

```markdown
# Shopping Research Report

## Product

Keychron mechanical keyboard

## AliExpress Findings

- Best price
- Seller rating
- Shipping time
- Risks

## Shopee Findings

- Best price
- Voucher availability
- Local seller options
- Delivery estimate

## Carousell Findings

- Used listings
- Negotiation potential
- Condition notes

## Lazada Findings

- Official store availability
- Warranty
- Delivery estimate

## Recommendation

Best overall option: Shopee official store

Reason: Local warranty, fast delivery, competitive price after voucher.
```

---

### 11.4 Salesforce business relations sales skills

A friend working as a Business Relations Director in Salesforce has many client accounts to handle.

Before reaching out, he needs to research the account deeply.

#### Research needs

For each client company, he needs to know:

- Who the C-suite leaders are
- What problems those leaders publicly talk about
- What the company’s priorities are
- What tech stack the company appears to use
- Which Salesforce products may fit their needs
- How to tailor an email pitch

#### Possible skills

This can be decomposed into:

- Company research skill
- C-suite staff research skill
- LinkedIn/news research skill
- Exa MCP-powered research skill
- Tech stack research skill
- Whitepaper proposal skill
- Email draft skill for each C-suite stakeholder

#### Example workflow

```text
1. Research the company’s business model.
2. Identify C-suite leaders.
3. Research each leader’s public statements, interviews, posts, and priorities.
4. Infer likely business pain points.
5. Research the company’s current tech stack.
6. Map Salesforce products to those pain points.
7. Draft a personalized outreach email.
8. Generate a whitepaper-style proposal.
```

#### Example output

```markdown
# Client Account Research: Acme Corp

## Company Overview

- Industry: Logistics
- Region: Southeast Asia
- Business model: B2B supply chain software

## Key Leaders

- CEO: Publicly focused on operational efficiency and regional expansion
- CTO: Speaks often about cloud modernization and data integration
- CMO: Focused on customer retention and lifecycle engagement

## Likely Pain Points

- Fragmented customer data
- Manual sales operations
- Lack of unified customer view
- Difficulty scaling regional account management

## Salesforce Product Fit

- Sales Cloud
- Service Cloud
- Data Cloud
- Marketing Cloud

## Suggested Outreach Angle

Lead with regional expansion and customer data unification.
```

---

### 11.5 Corporate banking relationship manager skills

Another friend works in OCBC as a corporate banking relationship manager.

She also has client accounts to research before meetings.

This workflow is similar to the Salesforce sales workflow, but tailored to banking relationship management.

#### Research needs

Before meeting a client, the RM may need to know:

- Company background
- Leadership team
- Recent news
- Financial health
- Expansion plans
- Financing needs
- Risk indicators
- Industry pressures
- Potential banking products to offer

#### Possible skills

- Company research skill
- C-suite research skill
- Financial news monitoring skill
- Industry risk research skill
- Meeting brief generation skill
- Relationship opportunity mapping skill
- Follow-up email draft skill

#### Example output

```markdown
# Corporate Banking Meeting Brief

## Client

Acme Manufacturing Pte Ltd

## Business Overview

- Sector: Advanced manufacturing
- Region: Singapore and Malaysia
- Recent activity: Expanding production capacity

## Leadership Notes

- CEO has spoken about regional growth
- CFO appears focused on cost control and working capital

## Potential Banking Needs

- Working capital financing
- Trade finance
- FX risk management
- Equipment financing

## Meeting Questions

1. Are you planning further regional expansion this year?
2. Are supply chain payment cycles affecting working capital?
3. Are FX movements impacting margins?

## Suggested RM Angle

Position OCBC as a regional growth and working capital partner.
```

---

## 12. Karpathy’s autoresearch and the Three-File Contract

Andrej Karpathy’s **autoresearch** project is a significant milestone in agentic coding.

Repo:

- [karpathy/autoresearch](https://github.com/karpathy/autoresearch)

The setup is simple but powerful.

The agent is given:

- A predefined goal
- A program file or training script
- A scoring function
- Permission to run experiments

In Karpathy’s example, the goal was to reduce validation loss and train a better small language model.

He tried this on a small LLM model called **nanochat**.

The agent’s task was to run different experiments and reduce the validation loss on evaluation.

### The Three-File Contract

Karpathy’s setup can be understood as a Three-File Contract.

#### 1. `program.md` — Human Direction

This file contains the research priorities, ideas, and constraints in plain English.

It tells the agent what matters.

Example:

```markdown
# Research Direction

Goal: Reduce validation loss.

Priorities:

- Try architecture improvements first.
- Avoid increasing parameter count too much.
- Keep training time reasonable.
- Log every experiment clearly.

Constraints:

- Do not modify the evaluator.
- Keep experiments reproducible.
```

#### 2. `train.py` — AI Playground

This is the actual PyTorch implementation the AI is allowed to rewrite.

The agent can modify the training code, try experiments, and improve the model.

Examples of things it might try:

- Optimizer changes
- Learning rate schedules
- Architecture tweaks
- Regularization
- Tokenization changes
- Batch size changes
- Training loop improvements

#### 3. Immutable Evaluator — Scoring Function

This is the part the agent cannot modify.

It automatically grades the experiment.

Examples:

- Validation loss
- `val_bpb`
- Accuracy
- Click-through rate
- Conversion count
- Revenue per visitor
- Retention rate

The evaluator creates the feedback loop.

The agent can try changes, run the evaluator, observe the score, and iterate.

---

## 13. The new human skill: problem formulation

The important human skill becomes less about manually writing every line of code and more about designing the game the agent is playing.

That means learning to define:

- The problem
- The constraints
- The allowed action space
- The scoring function
- The feedback loop
- The success criteria
- The things the agent must not modify

This is where the work gets interesting.

The agent can optimize, but the human has to define the arena.

If you define the arena badly, the agent will optimize the wrong thing very efficiently. Which is just a faster way to shoot yourself in the foot.

---

## 14. Taking the autoresearch pattern beyond ML

The autoresearch structure is not limited to ML research.

The same pattern applies anywhere there is a feedback signal.

The general form:

```text
Human goal + editable strategy/code/content + immutable scoring function = agent optimization loop
```

### Advertising

Scoring functions could include:

- Click-through rate
- Cost per click
- Conversion rate
- Subscription conversion count
- Return on ad spend

An agent could generate ad copy, landing page variants, or audience hypotheses, then optimize based on analytics.

### Social media

An agent could manage a social media account while having access to analytics.

Feedback signals could include:

- Engagement rate
- Follower growth
- Click-throughs
- Saves
- Shares
- Comments
- Newsletter signups

This is the same structure as autoresearch:

```text
Human goal + editable content strategy + immutable analytics feedback = agent optimization loop
```

### Business analytics

Other possible scoring functions:

- Lead conversion
- Sales calls booked
- Trial activation
- Retention
- Upsell rate
- Churn reduction
- Support ticket resolution time

The broader idea:

> If a workflow has measurable feedback, an agent can potentially experiment against that feedback loop.

But the human must define constraints and guardrails carefully.

---

## 15. Practical tips for working with agents

### Tip 1: Stop treating one chat as a place to hold all memories

Agents do not work that way.

They have context window limitations.

A single chat should not become a giant dumping ground for everything you want the agent to remember.

Instead, treat one chat as one bite-sized task.

Bad pattern:

```text
I keep one massive chat for my entire project forever.
```

Better pattern:

```text
I start a fresh chat for a specific task, give it the relevant context, and ask it to produce a durable artifact.
```

### Tip 2: Turn repeated tasks into skills

Once you guide an agent through a task successfully, ask it to package the workflow as a skill.

Example:

```text
Package this workflow as a skill so you can repeat it next time with new variables.
```

Next time, you can describe the task again or invoke the skill with a slash command, depending on your harness.

Examples:

```text
/daily-health-report
/shopping-research
/account-research
```

The mental model:

```text
Chat = task execution
Skill = reusable workflow memory
Markdown files = durable knowledge/memory
```

### Tip 3: Use `CLAUDE.md` or `AGENTS.md` for durable agent instructions

If you need an agent to “remember” durable project-level instructions, put them in the appropriate instruction file.

For Claude Code:

```text
CLAUDE.md
```

For other agent harnesses:

```text
AGENTS.md
```

These files act like project-level system prompts loaded into the chat session.

They are good for stable instructions like:

- Project architecture
- Coding conventions
- Test commands
- Deployment rules
- Important constraints
- Repository-specific workflows

Example `AGENTS.md`:

```markdown
# Agent Instructions

## Project Overview

This repository contains a Next.js app with a FastAPI backend.

## Rules

- Do not modify database migrations without asking.
- Always run tests before finalizing code changes.
- Prefer small, reviewable commits.

## Commands

- Frontend tests: `npm test`
- Backend tests: `pytest`
- Typecheck: `npm run typecheck`
```

### Tip 4: Store knowledge as markdown files

For other memory or context, save information as files.

Examples:

- Sales account research
- Company profiles
- Meeting briefs
- Research notes
- Client histories
- Product comparisons
- Experiment logs

Knowledge bases and memory are commonly treated as markdown files.

This is related to Andrej Karpathy’s LLM wiki idea.

Simple pattern:

```text
Write durable knowledge into markdown.
Let agents read the relevant files when needed.
Do not rely on one giant chat to remember everything.
```

---

## 16. Global skills vs project-scoped skills

Claude Code saves global skills by default to:

```text
~/.claude/skills
```

A global skill is available across Claude Code sessions.

But sometimes you only want a skill available inside one project.

In that case, ask Claude to save the skill scoped to the project repo.

It will create a project-level folder such as:

```text
.claude/
```

and save the skill there.

This is useful when:

- The skill is specific to one repository
- The skill depends on project-specific files
- You want to share the skill with other developers
- You want the repo itself to package agent workflows

This is why some modern repos include folders like:

```text
.claude/
.codex/
.openclaw/
```

These folders often contain agent-specific instructions, skills, or workflow definitions.

---

## 17. Suggested learning path

If you are new to agentic coding, try this sequence.

### Step 1: Use a coding agent normally

Start with a developer-focused harness like:

- Codex
- Claude Code
- OpenClaw
- Hermes

Ask it to perform a small task:

```text
Read this repo and explain how authentication works.
```

Then ask it to make a small change:

```text
Add a small feature and write tests for it.
```

### Step 2: Notice repeated workflows

Pay attention to repeated tasks:

- Bug fixing
- PR review
- Research
- Meeting prep
- Data analysis
- Daily summaries
- Shopping comparison
- Sales account research

### Step 3: Turn one workflow into a skill

After one successful workflow, ask:

```text
Turn what we just did into a reusable skill.
```

### Step 4: Build a plugin

Once you have several related skills, bundle them into a plugin.

Example:

```text
sales-research-plugin/
  company-research/
  c-suite-research/
  tech-stack-research/
  proposal-drafting/
  email-drafting/
```

### Step 5: Add orchestration

Create a parent skill that spawns subagents and coordinates the workflow.

Example:

```text
Run company research, C-suite research, and tech stack research in parallel.
Then synthesize the findings into a meeting brief and email draft.
```

---

## 18. Mental model summary

A compact way to think about the stack:

```text
Model = intelligence
Harness = environment + permissions + tools
Tools = ways for the agent to act
MCPs = APIs for agents
CLIs = compact tool interfaces
Skills = reusable SOPs
Plugins = bundled skill systems
Subagents = parallel workers with fresh context
Markdown files = durable memory and knowledge
Evaluators = feedback loops for optimization
```

The future of agentic coding is not just better models.

It is better workflows.

The leverage comes from designing systems where agents can:

- Understand the goal
- Access the right tools
- Work with the right amount of context
- Follow reusable workflows
- Delegate subproblems
- Produce durable artifacts
- Improve against feedback loops

The shift is:

```text
Do not only ask agents to complete tasks.
Design workflows that let agents repeatedly complete tasks well.
```

---

## 19. Links and resources

### Agentic coding and workflow plugins

- Superpowers — https://github.com/obra/superpowers
- Get Shit Done / GSD — https://github.com/gsd-build/get-shit-done
- BMAD — Breakthrough Method for Agile Development — https://github.com/bmad-code-org/BMAD-METHOD
- oh-my-claude-code — https://github.com/yeachan-heo/oh-my-claudecode

### Non-software skill examples

- Trader Monty Claude Trading Skills — https://github.com/tradermonty/claude-trading-skills
- gstack by Garry Tan — https://github.com/garrytan/gstack

### Autoresearch and markdown knowledge bases

- Karpathy Autoresearch — https://github.com/karpathy/autoresearch
- Karpathy LLM Wiki gist — https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- Rohit LLM Wiki gist — https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2

---

## Closing note

Agentic engineering is about designing the operating system around the model.

The model matters, obviously.

But the real leverage comes from the surrounding system:

- Skills
- Plugins
- CLIs
- MCPs
- Subagents
- Markdown knowledge bases
- Constraints
- Scoring functions
- Feedback loops

That is the practical frontier: not just smarter agents, but better-designed agent workflows.
