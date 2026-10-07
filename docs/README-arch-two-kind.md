
# What is this project

2026-10-6

## The Architecture of its use

**Q:** What is the intended use architecture of this? github.com/callstackincubator/cali

**A:** by GPT-6 Luna

Cali’s **intended-use architecture** is a bounded agent runner for React Native/Expo engineering tasks. 
You invoke a command such as `cali qa` or `cali review`; Cali gathers the task and environment context, 
selects a role and its permitted tools, runs the model/tool loop, then produces a structured report 
for a local or CI output target. It is designed to make runs repeatable and constrain what each role 
can do—not to host a fleet of autonomous agents.

The flow is:

**Command → context → role + tool pack → model/tool loop → report publisher**

- **Command:** The CLI provides entry points such as `qa`, `review`, `perf-review`, and `dev`.
- **Context:** One shared `cali-context.json` contract carries workspace, repository, PR/task,
    mobile/build, output, and optional role-specific information. Adapters can derive context
    from local flags, GitHub Actions, or EAS.
- **Role and tools:** Each command maps to a role and an explicitly bounded tool pack—for example,
    QA gets mobile-device tooling, while performance review can use device and React DevTools tools.
    This is where Cali limits the agent’s capabilities to what the task needs.
- **Execution:** A shared runtime handles the tool loop, rather than each role implementing its own agent engine.
- **Output:** The role’s result is shaped into a structured report, then a publisher writes or enriches it for local use or CI.

That makes the design **command-oriented and role-based**, with a shared runtime and contracts underneath. 
It’s more of an agent-enabled CLI for repeatable mobile QA/review/dev work than a general-purpose multi-agent framework.

The repository also separates `packages/cali`—the CLI/runtime—from `packages/tools`, which exposes 
reusable React Native tools to other runtimes. So Cali’s own architecture includes both an opinionated 
end-to-end runner and a reusable tools package.

## The Architecture of the software itself

Cali’s **own software architecture** is a TypeScript monorepo with two packages: a CLI/runtime package 
(`packages/cali`) and a reusable tools package (`packages/tools`). Inside the CLI package, responsibilities 
are split into modules with explicit contracts:

- **CLI layer** parses commands and options.
- **Command layer** coordinates each operation, such as QA or review.
- **Runtime layer** supplies shared context handling, tool-pack selection, the model/tool loop,
    mobile helpers, and publisher interfaces.
- **Role layer** defines role-specific behavior and expected outputs.
- **Tool layer** groups capabilities the runtime can grant to a role.
- **Report layer** defines report types, renders reports, and sends them to publishers.
- **Configuration layer** validates and loads settings.

The main design idea is to keep **workflow-specific decisions at the edges**—commands choose a role 
and tools—while shared infrastructure stays in the runtime. All commands use one normalized context 
contract and a common publishing pipeline, rather than each command building its own context 
and report machinery. The separate tools package exposes React Native development tools for use by Cali 
or other AI runtimes.

In shorthand:

**CLI → command orchestration → shared runtime + role/tool selection → report generation/publishing**

This is a modular CLI architecture with a shared execution core—not a collection of independently deployed agents.

## Search Words used to find this repo

    example using ai agent to design a react native app

