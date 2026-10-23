# Test prompts for `dotnet-aspire`

`evals.json` holds nine prompts, each with the expected result and verifiable expectations, in the skill-creator
`evals.json` schema, so they can be run with and without the skill and graded. Prompts 1–4 come from the former
`dotnet-aspire-local-environment` evals, 5–6 from `dotnet-structured-testing`, both updated to the single AppHost and
local JWT rules; 7–9 are new.

## What each prompt needs from the target repository

| # | Prompt | Mode | Target repository must have |
|---|---|---|---|
| 1 | `/dotnet-aspire audit` | Audit | A .NET solution with at least one protected API and one database. Ideally an unused package reference, a second (test) AppHost and a signing key committed to a base `appsettings.json`. |
| 2 | `/dotnet-aspire setup` | Setup | An API issuing and validating HS256 tokens with a key from configuration (login endpoint) + a validation-only API with config-bound JwtBearer and a production `Authority`, EF Core migrations on PostgreSQL or SQL Server, a `.sln`/`.slnx`, no Aspire, no tests. |
| 3 | `/dotnet-aspire repair` | Repair | A local AppHost with `WithDataVolume` and a hardcoded connection string, plus a separate test AppHost used by component/functional tests (former testing skill layout). |
| 4 | `/dotnet-aspire debug …` | Debug | Prompt 2's output, plus an entity property added to the model without a migration, so `GET /orders` fails with a missing column. |
| 5 | Añade tests de componente para POST /orders… | Incremental (component) | Prompt 2's output (or equivalent) and a `POST /orders` endpoint protected by roles. Rename the endpoint in the prompt to one that exists. |
| 6 | Añade un test funcional del flujo de pago… | Incremental (functional, possibly partial setup) | A payment flow that calls a provider over HTTP and publishes to Kafka. Run on prompt 2's output, or where only component infrastructure exists, to exercise the partial setup. |
| 7 | Quiero probar todas las APIs en local… | Setup/repair of authentication | Several protected services: one with login endpoints and a key in configuration, one config-bound with a production `Authority`, ideally a gRPC host. |
| 8 | Dame un token de administrador… | Token request | Prompt 2's output. Optionally without `devtoken init` run, to check the missing-key path. |
| 9 | `/dotnet-aspire repair (…)` | Repair of rule 9 violations | Tests that obtain tokens from `POST /auth/login`, and another API tested with an accept-any-token scheme. |

Docker or Podman and the Aspire CLI must be available for prompts 2–7 and 9 to reach runtime verification. Without
them, grade the runtime expectations on whether the agent reported precisely what it could not run.

## Running them

With the skill-creator skill:

1. For each prompt, run one agent with the skill installed and one without, starting from a copy of the same
   repository state.
2. Save the outputs and transcripts per run.
3. Grade each run against the `expectations` of its prompt.

After each run check that no volume, global npm package or template was installed or removed without consent, that
an existing development certificate was not cleaned (trusting it is intended), and that no token or key was committed.
