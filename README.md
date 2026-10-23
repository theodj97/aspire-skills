<div align="center">

# 🚀 AspireSkill

**One Agent Skill to run and test any .NET repository with Aspire.**

One AppHost for local development and tests · real infrastructure · ephemeral databases with schema + seed ·
dashboard telemetry · unit, component and functional tests · locally signed JWTs, no identity provider.

[![Agent Skills](https://img.shields.io/badge/agent%20skills-skills.sh-000000?logo=vercel&logoColor=white)](https://skills.sh)
[![.NET](https://img.shields.io/badge/.NET-512BD4?logo=dotnet&logoColor=white)](https://dotnet.microsoft.com)
[![Aspire](https://img.shields.io/badge/Aspire-512BD4?logo=dotnet&logoColor=white)](https://aspire.dev)
[![xUnit v3](https://img.shields.io/badge/xUnit-v3-2D9CDB?logo=xunit&logoColor=white)](https://xunit.net)
[![Docker](https://img.shields.io/badge/Docker%20%2F%20Podman-2496ED?logo=docker&logoColor=white)](https://www.docker.com)

</div>

---

## ✨ What it does

`dotnet-aspire` is a skill for AI coding agents (built for Claude Code) that **audits, creates, repairs and validates**
how a .NET repository it has never seen runs locally and is tested. It merges and supersedes
`dotnet-aspire-local-environment` (DotnetAspireLocalDev) and `dotnet-structured-testing` (DotnetTestingSkills).

| | |
|---|---|
| 🔎 **Discovery first** | Solution, SDK, executables, dependencies, databases, telemetry, tests and every protected API's authentication come from evidence. A gap report and plan come before any change. |
| 🗂 **Clear folders** | `src/` application code · `tests/` every test · `local/` everything local: the AppHost, database initializer, seed, the `devtoken` tool. |
| 🧩 **One AppHost, three modes** | `local/aspire/{App}.AppHost` serves `aspire run` (Local mode) and the component and functional test fixtures (Component and Functional modes). One topology, no copies. |
| 🗄 **From scratch, every run** | No volumes, no persistent containers: every start creates the databases, applies the schema and (Local mode) the synthetic seed. |
| 🔐 **Local JWT, no identity provider** | Every protected API, with or without login endpoints, trusts a local issuer through the configuration keys it already reads. `devtoken` signs tokens for profiles from the real roles and scopes; tests sign their own with a new key per run. Validation stays on; nothing calls a provider, and tests prove it. |
| 🧪 **Real tests** | Unit (NSubstitute), component (`WebApplicationFactory` + real containers) and functional (apps as Aspire resources, black box) with Respawn resets, WireMock for third parties and an authentication matrix (401 vs 403). |
| 📈 **Useful dashboard** | ServiceDefaults or existing telemetry, dependency instrumentation, `aspire otel` evidence; tests stay without telemetry. |
| 🛡 **Safe** | No committed secrets or tokens, no production access, no deleted volumes, no global installs without consent, no production code changes without consent, truthful reporting. |

---

## 📦 Install

Run inside the repository where you want the skill:

```bash
npx skills add <owner>/<repo> --skill dotnet-aspire -a claude-code
```

Or copy the folder manually and commit it:

```bash
cp -r dotnet-aspire <target-repo>/.claude/skills/
```

- The folder name must stay `dotnet-aspire` (it matches `name` in `SKILL.md`; skill names are lowercase with hyphens).
- Keep it in a hidden folder (`.claude/skills/`, `.agents/skills/`…): the `.cs` templates must not be picked up by
  .NET builds.
- For every repository on your machine, use `~/.claude/skills/` instead.
- Remove the old `dotnet-aspire-local-environment` and `dotnet-structured-testing` skills from the target repository
  when you install this one, so they do not compete.

---

## 🚀 Usage

The skill triggers on natural language (English or Spanish) and accepts explicit modes:

```text
/dotnet-aspire audit                         # discovery + gap report, no changes
/dotnet-aspire setup                         # create the environment, local JWT and test infrastructure
/dotnet-aspire repair                        # reconcile an existing setup (e.g. merge a separate test AppHost)
/dotnet-aspire debug POST /orders returns 500
Añade tests de componente para POST /orders, incluyendo autorización por rol
Dame un token de administrador para probar la API de pedidos
```

Setup and repair always present a plan and wait for confirmation before substantial changes.

> ⚠️ `argument-hint` is a Claude Code frontmatter field. Other agents ignore it, and uploading the skill to claude.ai or
> the Skills API rejects unknown keys: remove that line for those targets.

---

## 🗂 Repository layout

```text
.
├── dotnet-aspire/                      🧠 The skill
│   ├── SKILL.md                        Entry point: modes, non-negotiable rules, phases, reference map
│   ├── references/
│   │   ├── discovery.md                Repository audit, inventory, auth discovery, report templates
│   │   ├── architecture-and-layout.md  src/tests/local, naming, references, solution, packages, csproj templates
│   │   ├── apphost.md                  The single AppHost: modes, contract, resources, repair, consolidation
│   │   ├── database.md                 Strategies A–E, initializer + seed, ephemeral rule, safety, test resets
│   │   ├── local-jwt-authentication.md Trust strategies, mapping, devtoken, TestTokenIssuer, isolation, auth tests
│   │   ├── local-development.md        Local mode, configuration, tokens, agent integration, debugging loop
│   │   ├── observability.md            ServiceDefaults, instrumentation, dashboard verification, test modes
│   │   ├── integration-testing.md      Test types, conventions, fixtures, component and functional tests
│   │   ├── unit-tests.md               Unit tests
│   │   ├── messaging.md                Kafka, RabbitMQ, Service Bus, SQS
│   │   ├── external-services.md        Substitutes per mode, WireMock, MailPit, "nothing leaves the machine"
│   │   ├── validation-and-checklist.md Verification, acceptance checklist, final report
│   │   └── sources.md                  Official docs and version-specific verified behaviour
│   └── assets/                         Templates: AppHost, contract, devtoken, docs, test infrastructure
└── evals/                              🧪 Test prompts with expected results
```

---

## ✅ Requirements

| | |
|---|---|
| 🧰 **.NET SDK** | The one used by the target repository; .NET 10+ for the single-file `devtoken` tool (a console-project variant is documented for older SDKs). |
| 🚀 **Aspire CLI** | Verified with 13.6.x. Installation: <https://aspire.dev/get-started/install-cli/> |
| 🐳 **Docker or Podman** | For containers (local environment, component and functional tests). Unit tests need neither. |
| 🔌 **External services** | Third-party APIs are simulated with WireMock in tests. No identity provider and no external credentials are needed for authentication. |

---

## 🧪 Evals

`evals/` contains realistic prompts with expected results and verifiable expectations. See
[`evals/README.md`](evals/README.md) for the target repository each prompt needs.
