# Awesome AI-Native Engineering [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

<!--lint disable double-link-->

This roadmap helps engineering teams build an efficient work process with AI agents. Each item describes one practice. The chapters go from a simple setup to a complex one: each builds on the chapters before it and gives agents more autonomy, from one supervised agent to fleets supervised by exception. The In practice line under each item lists what a team has in place when it follows the practice, so a team can assess its maturity against the whole list. The Agentic AI Toolbox at the end lists tools by category.

## Contents

- [Foundations](#foundations)
- [Agentic Loop](#agentic-loop)
- [Specifications and Knowledge Management](#specifications-and-knowledge-management)
- [Shared Harness](#shared-harness)
- [Automated Verification](#automated-verification)
- [Autonomous Execution](#autonomous-execution)
- [Measurement and Improvement](#measurement-and-improvement)
- [Agentic AI Toolbox](#agentic-ai-toolbox)


## Foundations

### 1. AI Usage Policy

The team must follow the company's AI use policy and use only the AI tools, models, MCP servers, and Skills the company has approved, with the data the policy allows. The policy applies to everyone who builds with agents. Enterprise accounts, managed settings, and organization-level policies enforce it.

**In practice:** the team has the approved list of AI tools, models, MCP servers, and data in one place every builder can find, and the managed settings and enterprise accounts that enforce it.

### 2. Product Exploration

The team uses agents to learn how the product works from its code, with questions like "How does feature X work?" or "Which services handle a checkout request?". Agents also generate a repository map, a tech stack summary, and component diagrams on demand.

**In practice:** the team has a generated repository map, tech stack summary, or component diagram, and a saved session with architecture questions answered from the code.

### 3. Agent Instructions

The team keeps agent instructions in the repo, so every agent starts with the same context. The main file is short and works as a table of contents that links to the docs a task needs. Smaller files cover a single directory or workflow. The team adds a rule only after it sees an agent fail, because stale or conflicting rules make agents less reliable. One owner removes rules that no longer apply.

**In practice:** the team has an AGENTS.md, CLAUDE.md, Copilot instructions, or similar file in the repo that links to the deeper docs, a named owner, and a commit history that shows rules removed as well as added.

### 4. Sandboxing and Permissions

Agents run in a dev container, VM, cloud sandbox, or an OS-level sandbox like sandbox-runtime, with scoped credentials, limited write paths, and a network allowlist. The team gives agents more autonomy step by step: low-risk commands and paths run without a prompt, each run has a budget, and privileges, production, publishing, and deleting still need approval. The permission config lives in the repo. The team keeps session logs, because later practices use them as evidence. [Claude Code](https://code.claude.com/docs/en/security), [Codex](https://developers.openai.com/codex/agent-approvals-security), and [Copilot](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent) document their controls.

**In practice:** the team has a checked-in permission config where low-risk commands need no prompt and production, publishing, and deleting need approval, the sandbox setup, and retained session logs as the audit trail of each run.

## Agentic Loop

### 5. Agentic Loop

The team gives the agent an outcome, acceptance criteria, and the checks it must pass. The agent then works on its own: it changes code, builds, lints, tests, reads the failures, and fixes them. One command runs build, lint, and tests in minutes and gives the same result every time. Lint and test errors say how to fix the problem, so the agent gets the fix together with the error. An iteration limit stops a loop that goes nowhere. The team reviews any edits to tests, snapshots, and check configuration, because an agent that cannot pass a check sometimes changes the check.

**In practice:** the team has one checked-in command for build, lint, and tests, an iteration limit in the agent config, and session logs where the agent ran from brief to passing checks with no human message in between.

### 6. Hook-Enforced Guardrails

Guardrails must work even when the agent ignores an instruction. Hooks run inside the session on every tool call: they block forbidden commands, run formatters and fast checks after edits, and keep a task open until the required checks pass. The same checks also run in CI (#16), since only agents that load a hook get its protection. [Claude Code hooks](https://code.claude.com/docs/en/hooks) and [Codex hooks](https://developers.openai.com/codex/hooks) provide them.

**In practice:** the team has at least one hook in version control that blocks a forbidden action or runs a formatter, linter, test, or audit after the agent's changes, and the same check in CI.

### 7. Requirements-Based Testing

Agents write unit, integration, contract, and E2E tests from the requirements and the observed behavior, never from the code they just wrote. A test that mirrors the implementation hides the same bug. Mutation testing shows whether the tests can actually fail. Fixtures use only fake or masked data.

**In practice:** the team has merged PRs with tests that name the requirement or spec they cover, fixtures generated by the agent, and a mutation testing report or a recorded bug those tests caught.

### 8. Browser and Device Verification

When an agent changes a UI, it checks the result in the running app with Playwright MCP, Chrome DevTools MCP, or a simulator. It clicks through the flow and reads console and network errors before it reports the task as done. One command starts the app for each working copy, with its own ports and database. Checks worth repeating become Playwright or Maestro scripts in CI. The team uses vision-based computer control only where there is no DOM or accessibility API.

**In practice:** the team has Playwright MCP, Chrome DevTools MCP, or a simulator in the agent config, a start command that runs the app per working copy, PRs whose agent runs include browser screenshots or traces, and Playwright or Maestro scripts in CI.

### 9. Evidence-Based Troubleshooting

Agents troubleshoot from real logs, traces, CI output, and database schemas. By default their access is scoped and read-only, or works on a throwaway copy. The team strips secrets and personal data before they reach the model, and production writes and migrations need explicit approval. Sentry and Grafana expose telemetry to agents; DBHub gives scoped database access.

**In practice:** the team has a scoped read-only credential or MCP server config for logs, traces, and the database, and a resolved incident or merged fix where the agent's diagnosis cites the actual log or trace.

## Specifications and Knowledge Management

### 10. Spec-Driven Development

The spec is the source of truth. It is a Markdown file in the repo that humans and agents change together, and the code follows it. A feature spec covers the happy path, error and edge cases, acceptance criteria, and the design decisions with the options considered. Specs stay short and grow with the work, because a long design document slows delivery. Agents remember nothing between sessions and lose track as the context fills, so every session starts by loading the spec. For each task, the agent writes a plan with acceptance criteria from the spec and changes no code until someone approves the plan. When code or tests prove the design wrong, the team updates the spec. Spec Kit, Kiro, Tessl, and BMAD-METHOD give this a repeatable flow.

**In practice:** the team has Markdown specs in the repo with the happy path, error cases, acceptance criteria, and design decisions, a commit history that shows them changing with the code, an agent instruction that loads the spec at session start, and approved plans the agent derived from specs before editing.

### 11. Architecture Governance

The repo holds the rules the code must follow: a reference architecture, coding standards, and an ADR for every non-trivial decision. Structural tests and custom lint rules enforce what code can check, and an agent review covers the rest (#16). Agents read the rules before a change and update the ADR in the same PR. A scheduled check (#20) flags rules the code no longer follows.

**In practice:** the team has a reference architecture and ADRs in the repo, structural tests or lint rules that enforce them, plans that cite them, and at least one agent diff a conformance check rejected.

### 12. Project Knowledge Base

Agents drift away from the product when they lack its context. One searchable knowledge base holds what specs (#10) and ADRs (#11) do not: the vision and roadmap, current priorities, API references, and onboarding guides. Agent instructions link to it (#3), and an MCP server exposes it (#14) when it lives outside the repo. The agent updates the pages a change affects in the same PR, and a scheduled check (#20) flags stale pages. DORA lists [AI-accessible internal documentation](https://dora.dev/ai/roi/report/) as a foundation for return on AI.

**In practice:** the team has a knowledge base with vision, priorities, API references, and onboarding guides, the agent instruction or MCP config that reaches it, and the output of the stale-page check.

## Shared Harness

### 13. Workflow Skills

A Skill turns a repeated workflow into steps every agent on the team runs the same way. It is a SKILL.md file the agent loads only when a task needs it, in an open format that works across agents. The team writes its own Skills for build steps, scaffolding, and PR prep. It also loads reviewed third-party Skills such as Context7 or Superpowers, pins their versions, and skips large frameworks unless they clearly beat a simpler setup.

**In practice:** the team has a SKILL.md it wrote and uses, a pinned list of external Skills, and a record of one Skill evaluated and rejected.

### 14. MCP Integrations

MCP connects agents to data and external systems through an open standard. A CLI, a script, or a Skill is often simpler and uses fewer tokens, so the team uses MCP when it needs a structured tool contract, an auth boundary, or shared access. Every MCP server runs with the team's access, so the team treats it as code it runs: only servers from the approved list (#1), pinned versions, unused tools and scopes turned off, and approval for destructive actions (#4).

**In practice:** the team has an MCP server config with pinned versions and disabled scopes, the credentials and permissions documented per server, and a record of one case where a CLI or Skill was chosen over MCP.

### 15. Code Intelligence

Text search misses call sites and leads agents to invent APIs. Agents use a language server, AST tools, and semantic search to resolve symbols, types, and references. On very large repos, a code graph of functions, imports, and call chains answers "what calls this?" over MCP in a few hundred tokens. CodeGraph, Potpie, and codebase-memory-mcp build such graphs; Serena exposes language-server tools over MCP.

**In practice:** the team has a language server, semantic search, or AST tool in the agent config, a prebuilt code graph for repos too big for grep-and-read, and measured token or tool-call savings.

## Automated Verification

### 16. Automated Verification

Automated review and QA run in layers before a human sees a diff. The deterministic layer is CI the agent cannot edit, static analysis, structural tests (#11), contract and E2E tests, and branch protection. In the agent layer, a context that did not write the code reviews the diff against the spec (#10), checks it against the ADRs (#11), and checks that the tests cover the requirements (#7). The team calibrates the reviewer agent against human reviews and keeps it only where evals (#24) show it catches defects. The team drops human review one category of change at a time, once the defect log shows that the automated layers catch what human reviewers catch. The goal is to release to production with a verification setup the team trusts without human review.

**In practice:** the team has branch protection and CI config the agent cannot edit, configured spec, architecture, and test-coverage reviewers, a defect log per layer that includes defects found after merge, a written rule for which changes merge without human review, and session logs where a feature went from brief to merged through automated review and QA with no human interaction.

### 17. Security Review and Threat Modeling

Code review and tests often miss authorization gaps, injection points, and exposed secrets. The team uses AI to trace trust boundaries and suggest abuse paths, then confirms the findings with [Codex Security](https://developers.openai.com/codex/security), CodeQL, Semgrep, and targeted tests.

**In practice:** the team has a security review or threat-model report on a meaningful feature or diff, and the fix, test, or written risk acceptance that followed.

### 18. Supply Chain and Prompt Injection Defense

An agent can invent a package name that attackers then register with malware (slopsquatting), or pull in a compromised dependency. The team checks that every suggested dependency exists and is the right package, then pins and scans it. Untrusted content such as web pages, issues, and MCP tool results must not trigger a tool that writes, publishes, or deletes without approval (#4).

**In practice:** the team has dependency verification and scanning in CI, a test that shows untrusted content cannot trigger a destructive action, and a record of a made-up or malicious dependency caught.

## Autonomous Execution

### 19. Long-Running Execution

A run that lasts hours is the agentic loop (#5) with state that outlives the session. It has a turn or token budget, a stop condition, and a definition of progress. Sessions end through crashes and handoffs, so the agent keeps its state in a progress file that it updates and re-reads. A new session starts with a clean context and a clear handoff, instead of a summary of the old chat.

**In practice:** the team has the budget and stop condition in the run config, a progress file the agent updated across sessions, and session logs with duration of a long or multi-session run that finished without manual intervention.

### 20. Background and Cloud Delegation

Background and cloud agents take a brief and return a PR. They also run on a schedule or a repo event to triage issues, investigate failing CI, or open maintenance PRs (#21). The team delegates only work it can verify. On [GitHub Copilot](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations), [Codex cloud](https://developers.openai.com/codex/app/automations), Cursor, or Claude Code on the web, every unattended run is sandboxed (#4), narrow in scope, and has a budget, and it ends in a draft that goes through verification (#16).

**In practice:** the team has the schedule or event trigger config of at least one unattended agent, and the draft PRs, issue updates, or reports it produced, with their verification record before merge or release.

### 21. Maintenance Automation

Code analysis and code graph tools (#15) measure the health of the codebase, and their findings go into a technical debt backlog. A scheduled agent (#20) works through the backlog: it opens refactoring PRs, runs codemods with ast-grep, GritQL, jscodeshift, or Comby, and fixes the framework, dependency, and security-patch upgrades that Renovate, Dependabot, or Snyk open. For an upgrade, the agent reads the changelog, updates the call sites, and runs the tests. The PRs merge under the review rule in #16.

**In practice:** the team has a technical debt backlog fed by code analysis, the schedule config of the maintenance agent, and merged refactoring, upgrade, and security-patch PRs it opened.

### 22. Parallel Execution

Each parallel agent works in isolation. A worktree gives it its own checkout and branch, and Portless or Aspire's isolated mode give it its own databases, ports, credentials, and caches. Each agent has one goal, a defined output, and files no other agent edits. Before merging, the team rebases each branch, runs the full checks, and sends the result through verification (#16). The team runs only as many parallel agents as it can review.

**In practice:** the team has a worktree or cloud setup for parallel agents with isolated ports and databases, merged branches from parallel runs, and a record of one task where the split saved time and one where it did not.

### 23. Agent Hierarchies

A lead agent splits a goal from the spec (#10) into tasks that each fit one context window, hands them to worker sub-agents (#22), and routes their output through verification (#16). Handoff chains between agents stay short, because long chains fail more often. Humans set the intent through specs and budgets and step in on failures, escalations, and anomalies. Every run has a trace (#24) and hard limits on cost and blast radius (#4). The [Steps of AI Adoption](https://claude.ai/code/artifact/bfdfaef9-bc62-4dfe-ba9e-c58a26c9accf) classification maps the path from one supervised agent to hundreds.

**In practice:** the team has a lead-agent run that dispatched worker and reviewer sub-agents from one spec, a trace or dashboard covering every run, and an escalation record handled instead of a diff-by-diff review.

## Measurement and Improvement

### 24. Traces and Evals

Traces record what each run did: tool calls, Skills loaded, hooks that blocked an action, iterations, and tokens. The harness exports them over OpenTelemetry or writes them to a log. Evals measure how well the agent workflow completes tasks, meaning its instructions, Skills, tools, and model. The team keeps 20 to 50 tasks from real work and past failures and re-runs them whenever the model, prompts, or tools change. Agents behave differently from run to run, so the team compares results across several runs. When a new model ships, the team reviews the harness and removes parts one at a time to see which still help.

**In practice:** the team has traces with tool, Skill, and hook events for its runs, an eval dataset built from real tasks and failures, results across several runs, and a regression the evals caught or a harness part they showed was no longer needed.

### 25. Token Economics

Tokens are the cost unit of agent work, and a full context window also lowers recall, so the team manages what the model sees and what it costs together. Instructions stay short and the toolset small, agents fetch information only when they need it, hooks filter command output, and prompt caching and model routing are on. The measure is task success per token. The team knows its budget and sees usage per tool, since parallel agents multiply spend. Compression tools like Headroom and RTK help, and the team checks task quality as well as token savings.

**In practice:** the team has a token budget, a usage view per tool, and before-and-after measurements of cost, latency, and task success for at least one context change.

### 26. Session Retros

A retro Skill reads the transcript of a finished session, finds where the agent lost time or made mistakes, and proposes a harness change for each finding: an instruction (#3), a lint rule or test, a hook (#6), a Skill update (#13), a tool change, or wider read access. Nothing changes until someone approves a proposal. A mechanical mistake gets an automated check rather than a new instruction. Each change records the failure that justified it, and the team removes it when it no longer applies. The team runs a retro after a long or difficult session, and a cross-session review every month. [Matt Pocock's `/retro`](https://www.aihero.dev/skills-retro) reviews one session; [retro-skill](https://github.com/netresearch/retro-skill) adds cross-session audits and checks whether past work was later reverted or failed CI.

**In practice:** the team has retro output from real sessions, the approved changes to instructions, checks, hooks, or Skills it produced, each with the failure that justified it, and a later session log without the same problem.

### 27. Delivery Metrics

The team measures its work process by delivery outcomes: lead time for changes, change failure rate, the revert rate of agent-written PRs, and the share of PRs merged without human edits. The numbers come from the issue tracker, the repo, and CI, with a baseline taken before each harness change. The team reads them together with token cost (#25) and eval results (#24), because a faster or cheaper process that ships more defects is a step back.

**In practice:** the team has a report with lead time, change failure rate, and revert rate of agent-written PRs, a baseline from before the last harness change, and a harness decision that these numbers led to.

#### Further reading

- [DORA: research and capabilities behind software delivery performance](https://dora.dev/)
- [Anthropic: Harness design for long-running agent applications](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- [Martin Fowler: Harness engineering for coding agents](https://martinfowler.com/articles/harness-engineering.html)
- [LangChain: The Anatomy of an Agent Harness](https://www.langchain.com/blog/the-anatomy-of-an-agent-harness)
- [Sebastian Raschka: Components of a Coding Agent](https://magazine.sebastianraschka.com/p/components-of-a-coding-agent)
- [Marmelab: The State of AI Harness Engineering 2026](https://marmelab.com/blog/2026/09/24/the-state-of-ai-harness-engineering-2026.html)
- [Addy Osmani: Agentic Autonomy Levels](https://addyosmani.com/blog/agentic-autonomy-levels/)

## Agentic AI Toolbox

### AI coding agents

- [Claude Code](https://claude.com/claude-code) - Anthropic's agentic coding CLI and IDE integration.
- [Cursor](https://cursor.com) - AI code editor with an agent mode and cloud agents.
- [Codex](https://github.com/openai/codex) - Agentic coding from OpenAI.
- [GitHub Copilot](https://github.com/features/copilot) - GitHub's AI pair programmer.
- [Gemini CLI](https://github.com/google-gemini/gemini-cli) - Google's Gemini CLI.
- [Google Antigravity](https://antigravity.google) - Google's agent-first development environment.
- [Devin](https://devin.ai) - Cognition's autonomous coding agent. Cognition's Windsurf editor is now Devin Desktop.
- [Cline](https://cline.bot/) - Open-source coding agent.
- [OpenCode](https://github.com/anomalyco/opencode) - Open-source coding agent for the terminal, desktop, and IDE.
- [Orbi](https://github.com/orbi-build/orbi) - Open-source runner that takes a labeled GitHub issue to a pull request, gates the merge on an independent review session against the acceptance criteria, and cuts the tagged release.

### Sandboxes and permissions

- [Dev Containers](https://containers.dev) - Open specification for containerized development environments, supported by editors, CI, and cloud agents.
- [Docker Sandboxes](https://docs.docker.com/ai/sandboxes/) - Isolated sandboxes from Docker for running coding agents with controlled file and network access.
- [E2B](https://github.com/e2b-dev/E2B) - Open-source secure cloud sandboxes for running agent-generated code.
- [sandbox-runtime](https://github.com/anthropics/sandbox-runtime) - Anthropic's OS-level sandbox for agents, MCP servers, and any command: filesystem and network allowlists through Seatbelt on macOS and bubblewrap on Linux, no containers (beta).

### Testing and browser/device automation

- [Agent QA](https://github.com/vostride/agent-qa) - QA agent that runs web and mobile regression tests written in natural language.
- [show-your-work](https://github.com/iamumeransari/claude-show-your-work) - Claude Code plugin where the agent records its own UI checks with Playwright and plays them in the terminal, before and after side by side.
- [Playwright](https://playwright.dev) - Cross-browser E2E framework.
- [Pact](https://pact.io) - Consumer-driven contract testing for service integration.
- [Diffblue Cover](https://www.diffblue.com) - AI unit test generator for Java/JVM.
- [Trunk Flaky Tests](https://trunk.io/products/flaky-tests) - Detect, quarantine, and track flaky tests across CI.
- [Maestro](https://github.com/mobile-dev-inc/Maestro) - Declarative E2E automation for mobile and web applications.
- [Testcontainers](https://testcontainers.com) - Throwaway databases and services in containers for integration tests the agent can run locally.
- [Stryker](https://stryker-mutator.io) - Mutation testing for JavaScript, .NET, and Scala; shows whether tests can fail.
- [Playwright MCP](https://github.com/microsoft/playwright-mcp) - Browser automation MCP from Microsoft.
- [Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp) - Google's MCP server that lets agents drive Chrome and read console, network, and performance data.
- [mobile-mcp](https://github.com/mobile-next/mobile-mcp) - MCP server for iOS and Android automation on simulators, emulators, and devices.
- [Browserbase Stagehand](https://github.com/browserbase/stagehand) - Browser automation SDK with Playwright-style methods and AI-driven actions.
- [Browser Use](https://github.com/browser-use/browser-use) - Open-source library that lets LLMs drive real browsers.
- [Anthropic Computer Use](https://platform.claude.com/docs/en/docs/agents-and-tools/computer-use) - Claude's screen, keyboard, and mouse control for agent-driven UI automation.

### Observability and incident response

- [Rootly](https://rootly.com) - Incident platform with AI agents for investigation and retrospectives.
- [incident.io](https://incident.io) - On-call and incident platform with AI summaries and investigation copilots.
- [PagerDuty AIOps](https://www.pagerduty.com) - AI-driven alert noise reduction and incident automation.
- [Sentry](https://sentry.io) - AI-assisted root-cause analysis with an MCP server for agent queries.
- [Grafana MCP](https://github.com/grafana/mcp-grafana) - Grafana's official MCP server for dashboards, metrics, logs, and alerts.
- [Datadog MCP Server](https://docs.datadoghq.com/bits_ai/mcp_server/) - Datadog's MCP server for querying monitors, logs, traces, and metrics.
- [Spike](https://spike.sh) - Incident management and on-call platform with a remote MCP server for AI agents.

### Database and data

- [Chat2DB](https://github.com/OtterMind/Chat2DB) - AI-assisted SQL client supporting major relational and analytical databases.
- [MCP Toolbox for Databases](https://github.com/googleapis/mcp-toolbox) - Google's open-source MCP server for databases, with connection pooling, auth, and observability.
- [DBHub](https://github.com/bytebase/dbhub) - Token-conscious database MCP server for PostgreSQL, MySQL, SQL Server, and more, with a read-only mode.
- [Postgres MCP Pro](https://github.com/crystaldba/postgres-mcp) - PostgreSQL MCP server with configurable read-only or read/write access, index tuning, and query plans.
- [dbt Wizard](https://www.getdbt.com/product/dbt-copilot) - AI agent from dbt Labs that generates models, tests, docs, and semantic definitions from natural language.

### Cloud and infrastructure

- [Kagent](https://github.com/kagent-dev/kagent) - CNCF Kubernetes-native framework for building and operating agents with tools for Kubernetes, Prometheus, Grafana, Istio, Helm, and other cloud-native systems.
- [AWS MCP Servers](https://github.com/awslabs/mcp) - AWS's open-source MCP servers for documentation, infrastructure, and services.
- [Azure MCP Server](https://github.com/microsoft/mcp) - Microsoft's catalog of official MCP servers, including Azure.
- [gcloud MCP](https://github.com/googleapis/gcloud-mcp) - Google Cloud's MCP server over the gcloud CLI.
- [Kubernetes MCP Server](https://github.com/containers/kubernetes-mcp-server) - MCP server for Kubernetes and OpenShift from the containers project.

### Spec-driven development

- [Spec Kit](https://github.com/github/spec-kit) - GitHub's toolkit for spec-driven development with AI agents.
- [Kiro](https://kiro.dev) - AWS's spec-first IDE.
- [Tessl](https://tessl.io) - Platform for managing specs and Skills, with a Skills registry.
- [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) - Development method in which role-based agents write a project brief and spec documents before coding.

### Documentation, ADRs, and architecture as code

- [Mintlify](https://mintlify.com) - AI-assisted docs platform for developer documentation.
- [MADR](https://github.com/adr/madr) - Markdown ADR template.
- [architecture-decision-record](https://github.com/architecture-decision-record/architecture-decision-record) - ADR templates and real-world examples, good input for an agent drafting one.
- [humanizer](https://github.com/blader/humanizer) - Agent Skill that removes signs of AI-generated writing from docs and other text.
- [Structurizr](https://github.com/structurizr/structurizr) - C4 model diagrams from a text DSL, so agents can read and update the architecture.
- [LikeC4](https://likec4.dev) - Architecture-as-code language and toolkit for C4-style diagrams kept in the repo.
- [C4-PlantUML](https://github.com/plantuml-stdlib/C4-PlantUML) - C4 model diagrams in PlantUML text.

### Agent instructions, Skills, and memory

- [AGENTS.md](https://agents.md/) - Open format for repo-level agent instructions.
- [Anthropic Skills](https://github.com/anthropics/skills) - Anthropic's public collection of reusable Agent Skills.
- [Superpowers](https://github.com/obra/superpowers) - Composable Skills framework with sub-agent patterns.
- [awesome-copilot](https://github.com/github/awesome-copilot) - Community-contributed instructions, agents, Skills, and configs for GitHub Copilot, reusable as examples.
- [Context7](https://github.com/upstash/context7) - Up-to-date library documentation for AI agents, loadable selectively.
- [Skills.sh](https://skills.sh/) - Directory of open Agent Skills with an install CLI, from Vercel Labs.
- [Agent OS](https://github.com/buildermethods/agent-os) - Injects your codebase standards and specs into agent instructions.
- [gstack](https://github.com/garrytan/gstack) - Opinionated Claude Code setup with role-based Skills for planning, design review, QA, and release.
- [Google Skills](https://github.com/google/skills) - Google's Agent Skills for Google Cloud and other Google products.
- [claude-mem](https://github.com/thedotmack/claude-mem) - Persistent, compressed context carried across coding-agent sessions.

### MCP servers for team systems

- [MCP Registry](https://registry.modelcontextprotocol.io/) - Official registry for discovering published MCP servers.
- [MCP Reference Servers](https://github.com/modelcontextprotocol/servers) - Educational reference implementations; evaluate and harden them before production use.
- [Awesome MCP Servers](https://github.com/punkpeye/awesome-mcp-servers) - Community-curated collection of MCP servers.
- [GitHub MCP](https://github.com/github/github-mcp-server) - Official GitHub MCP server.
- [GitLab MCP](https://docs.gitlab.com/user/gitlab_duo/model_context_protocol/mcp_server/) - GitLab's official MCP server for issues, merge requests, and pipelines.
- [Atlassian Rovo MCP Server](https://www.atlassian.com/platform/remote-mcp-server) - Atlassian's remote MCP server for Jira and Confluence.
- [Linear MCP](https://linear.app/docs/mcp) - Linear's official MCP server for issues and projects.
- [Notion MCP](https://github.com/makenotion/notion-mcp-server) - Official Notion MCP server for pages and databases.
- [Slack MCP](https://docs.slack.dev/ai/mcp-server/) - Slack's official MCP server for channels and messages.
- [Figma Dev Mode MCP](https://developers.figma.com/docs/figma-mcp-server/) - Figma's developer MCP for design context and canvas access.
- [MCP Inspector](https://github.com/modelcontextprotocol/inspector) - Visual testing tool for MCP servers; check what a server exposes before the team uses it.
- [Docker MCP Catalog and Toolkit](https://docs.docker.com/ai/mcp-catalog-and-toolkit/) - Curated MCP servers run in containers, with a gateway for credentials and isolation.

### Code intelligence and code graphs

- [Serena](https://github.com/oraios/serena) - MCP toolkit that gives agents language-server tools: symbols, references, and semantic edits.
- [CodeGraph](https://github.com/colbymchenry/codegraph) - Pre-indexed code knowledge graph that syncs on code changes, queried over MCP.
- [codebase-memory-mcp](https://github.com/DeusData/codebase-memory-mcp) - Code intelligence MCP server that indexes a codebase into a graph of symbols, calls, and dependencies.
- [Potpie](https://github.com/potpie-ai/potpie) - Code knowledge graph with agents that answer questions about a codebase.
- [CodeGraphContext](https://github.com/CodeGraphContext/CodeGraphContext) - MCP server and CLI that index local code into a graph database.
- [SCIP](https://github.com/scip-code/scip) - Code intelligence protocol and index format, originally from Sourcegraph.

### Code review

- [CodeRabbit](https://coderabbit.ai) - AI code review bot for GitHub/GitLab PRs.
- [Greptile](https://greptile.com) - AI code reviewer with repo-wide context.
- [PR-Agent](https://github.com/The-PR-Agent/pr-agent) - Community-maintained open-source agent for PR descriptions, reviews, and walkthroughs.
- [Ellipsis](https://ellipsis.dev) - Platform to deploy, manage, and observe cloud coding agents; includes AI PR review with auto-fix.

### Dependency and security automation

- [Dependabot](https://github.com/dependabot) - GitHub's dependency update bot.
- [Renovate](https://github.com/renovatebot/renovate) - Cross-platform dependency update tool.
- [Snyk](https://snyk.io) - Security platform with AI-assisted fix suggestions for vulnerable dependencies.
- [Trivy](https://github.com/aquasecurity/trivy) - Vulnerability, misconfiguration, secret, and SBOM scanner for repositories and infrastructure.
- [Gitleaks](https://github.com/gitleaks/gitleaks) - Secret scanner for repositories, files, and commit history.
- [Semgrep](https://github.com/semgrep/semgrep) - Static analysis for many languages with security rules the agent can run locally and in CI.
- [CodeQL](https://codeql.github.com) - GitHub's semantic code analysis engine for security queries.
- [Socket](https://docs.socket.dev) - Supply-chain security that checks packages for malware, typosquats, and risky behavior before install.
- [Syft](https://github.com/anchore/syft) - CLI that generates a software bill of materials (SBOM) from code and containers.
- [OWASP Threat Dragon](https://github.com/OWASP/threat-dragon) - Open-source threat modeling tool with diagrams and threat lists.

### Agents in CI and automations

- [Claude Code Action](https://github.com/anthropics/claude-code-action) - GitHub Action that runs Claude Code on issues, PRs, and schedules.
- [Codex Action](https://github.com/openai/codex-action) - GitHub Action that runs OpenAI Codex in CI workflows.
- [Run Gemini CLI](https://github.com/google-github-actions/run-gemini-cli) - GitHub Action that runs the Gemini CLI for issue triage, PR review, and scheduled tasks.

### Codemods and refactoring
- [ast-grep](https://github.com/ast-grep/ast-grep) - Structural search, lint, and rewrite CLI on tree-sitter, with a rule catalog.
- [GritQL](https://github.com/biomejs/gritql) - Query language for structural codemods, now maintained by Biome (formerly Grit, acquired by Honeycomb in 2025).
- [jscodeshift](https://github.com/facebook/jscodeshift) - Meta's AST codemod toolkit for JavaScript and TypeScript.
- [Comby](https://comby.dev) - Language-agnostic structural search-and-replace for multi-file refactoring.
- [OpenRewrite](https://github.com/openrewrite/rewrite) - Automated mass refactoring with a large recipe catalog, strongest on the JVM.
- [Codemod Registry](https://codemod.com/registry) - Ready-made codemods for framework and library migrations, run through the Codemod CLI.

### Fleets and orchestration

- [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) - Anthropic's SDK for building sub-agents and orchestrated workflows.
- [OpenAI Agents SDK](https://github.com/openai/openai-agents-python) - Lightweight multi-agent orchestration with handoffs and tracing.
- [LangGraph](https://github.com/langchain-ai/langgraph) - Graph-based framework for stateful, multi-agent workflows.
- [Claude Code agent teams](https://code.claude.com/docs/en/agent-teams) - Orchestrate teams of Claude Code sessions that share tasks and report back.
- [Conductor](https://conductor.build) - Workspace for running a team of coding agents in parallel, one worktree per task.
- [Superset](https://superset.sh) - Orchestration workspace for any coding agent, with a worktree per task and every session in one place.
- [parallel-code](https://github.com/johannesjo/parallel-code) - Run Claude Code, Codex, and Gemini side by side, each in its own Git worktree.
- [Portless](https://github.com/vercel-labs/portless) - Stable named URLs for localhost services, worktree-aware (Vercel Labs).
- [Aspire](https://aspire.dev/) - Isolated multi-service local dev/test stacks; now language-agnostic (formerly .NET Aspire).

### Agent traces and evals

- [OpenAI Agent Evals](https://developers.openai.com/api/docs/guides/agent-evals) - Traces, graders, datasets, and evaluation runs for agent workflows.
- [Langfuse](https://github.com/langfuse/langfuse) - Open-source tracing, prompt management, and evaluation platform for LLM applications.
- [Arize Phoenix](https://github.com/Arize-ai/phoenix) - Open-source observability and evaluation for AI applications and agents.
- [promptfoo](https://github.com/promptfoo/promptfoo) - Open-source evals and red-teaming for prompts, agents, and RAG, runnable in CI.
- [Braintrust](https://www.braintrust.dev) - Evals, tracing, and prompt management platform for agents.
- [OrcaReplay](https://github.com/Continuum-AI-Corp/OrcaReplay) - Records a coding-agent run below the harness and replays it offline against the recorded bytes, turning a past run into a repeatable case.

### Token consumption

- [ccusage](https://github.com/ccusage/ccusage) - Local usage and cost analysis for Claude Code and other coding agents.
- [Headroom](https://github.com/headroomlabs-ai/headroom) - Local-first, reversible compression for agent context, tool outputs, logs, files, RAG chunks, and conversation history; available as a library, proxy, wrapper, or MCP server.
- [Caveman](https://github.com/JuliusBrussee/caveman) - Agent response compression (~70% output token reduction).
- [RTK](https://github.com/rtk-ai/rtk) - CLI proxy for input token filtering on common dev commands.
- [NextReset](https://nextreset.ai/) - Independent Codex reset-history tracker with official incident sources and a browser-local personal timer.
- [Ponytail](https://github.com/DietrichGebert/ponytail) - Skill that stops the agent over-building: it writes only the code the task needs, which cuts output tokens.

### Session retros

- [retro](https://github.com/mattpocock/skills/tree/main/skills/engineering/retro) - Matt Pocock's Skill that reviews a session and proposes harness changes, such as navigation pointers, automated checks, coding standards, and cheaper tool use, ranked by severity.
- [retro-skill](https://github.com/netresearch/retro-skill) - Claude Code plugin that routes session findings to memory, project rules, or Skill PRs after approval, with cross-session audits and an outcome mode that checks whether a past session's work was reverted, rejected, or failed CI.

### Local inference

- [Ollama](https://github.com/ollama/ollama) - Run open models locally behind an OpenAI-compatible API.
- [oMLX](https://github.com/jundot/omlx) - LLM inference server for Apple Silicon with continuous batching and SSD caching, managed from the macOS menu bar.

## Contributing

Contributions are welcome. Read the [contribution guidelines](CONTRIBUTING.md) before proposing a roadmap item or Toolbox entry. The roadmap is kept intentionally short and practical, so additions should be actively maintained, clearly useful, and grounded in a concrete engineering workflow.
