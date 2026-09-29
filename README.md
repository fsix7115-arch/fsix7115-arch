# ARYX

I build small tools for problems that are annoying to diagnose and boring to
fix, and I write down what actually happened rather than what was supposed to.

## Projects

**[devcheck](https://github.com/fsix7115-arch/devcheck)** — is your dev machine
healthy, or just populated? No dependencies, read-only, safe in CI.

The failure it exists for: Cloudflare answers Groq with `error code: 1010` to
any client that does not send a browser `User-Agent`, which includes
`urllib` and `curl`'s default. The tool is installed, the key is valid, the
call still fails, and the error says nothing about keys, so it reads as "my
credentials are wrong" and sends you off to regenerate a working key. devcheck
sends the same request twice, bare and with a User-Agent, and reports the
difference as a property of your network rather than your account.

It also flags version floors that actually break workflows, and reports real
free disk space, which explains more strange failures than it has any right to.

**[freellm-probe](https://github.com/fsix7115-arch/freellm-probe)** — find out
which free LLM APIs work from your machine, right now. No dependencies.

Every free-LLM aggregator README has a provider table, and the table is wrong
within a month. It was written on the author's machine, in their country, on
their IP, on a day when the keys worked. Four things break that no README
mentions:

- Groq returns Cloudflare `error code: 1010` to any client that does not send
  a browser `User-Agent`. Python's `urllib` does not, so the error reads as
  "your key is bad." It isn't.
- A valid Gemini key can list 50 models and successfully call none of them.
  `gemini-2.0-flash` returns *"no longer available to new users"* on a key
  that authenticates fine.
- `llama-3.3-70b-versatile` is in every Groq tutorial. It 404s now. The catalog
  endpoint is the source of truth, not the docs.
- The free tier is a shared pool: roughly one call in four returns a 503.

**[delta-bot](https://github.com/fsix7115-arch/delta-bot)** — a no-lookahead
backtesting engine for crypto perpetuals on Delta Exchange India.

The headline result is ETHUSD 1d, order block entries gated by sentiment:
Sharpe **0.94** out-of-sample over six years, 0.85 at doubled costs, 129
trades, -14.9% max drawdown. The same strategy printed 2.88 on two years of
data. Both numbers are in the README, because a backtest that only shows the
flattering one is a sales pitch.

Most of that repo is not finding strategies. It is finding the places where a
backtest lies to you: a pattern detector that read three candles into the
future, a Sharpe denominator that shrank every time a strategy sat flat
(Sharpe 680), funding charged twice per settlement. If a Sharpe above 3 shows
up, I assume a bug until I can explain it.

**[agentforge-compliance-hub](https://github.com/fsix7115-arch/agentforge-compliance-hub)** —
open-source compliance, governance and audit platform for AI agent fleets.
Next.js 14 + Prisma, SQLite locally and PostgreSQL in Docker. 40 unit tests,
3 end-to-end tests, CI on every push.

Every agent action is written to an append-only table where
`hash = SHA256(previousHash + canonicalActionData)`, so a deleted or edited row
breaks the chain and the break index is reported. A YAML policy engine runs on
each action — operators describe the *allowed* condition, so
`field: tokenCost, operator: lte, value: 10` reads as "permitted while under
$10". Policy evaluation, risk scoring, budget checks and approval decisions are
deterministic and need no AI API key, so the whole thing runs offline.

[ASSUMPTIONS.md](https://github.com/fsix7115-arch/agentforge-compliance-hub/blob/main/ASSUMPTIONS.md)
lists what is *not* built yet — NextAuth, a BullMQ worker, OpenTelemetry, SMTP
invites, PDF export. I would rather ship an honest gap list than a feature
matrix where every box is green.

**[AgentForge](https://github.com/fsix7115-arch/AgentForge)** — a self-hosted AI
agent server in Go: one binary, about 10 MB, no dependencies, and it runs on a
phone. 51 tests with a CI guard that fails if the test count ever drops, plus
cross-compiles for android/arm64, linux/arm64, darwin/arm64 and windows/amd64.
Verified running on an actual Android 15 device in Termux.

Not the same project as agentforge-compliance-hub despite the name. That one is
a governance control plane; this one is the runtime it would govern. They
compose, but neither depends on the other.

**[trend2repo-duo](https://github.com/fsix7115-arch/trend2repo-duo)** — two
agents that scout GitHub/HN/Reddit for trends and generate a complete
repository blueprint from each one: schema, routes, tasks, prompts and starter
files.

## What I care about

Verifying claims against the machine instead of the documentation. Both
projects here exist because the docs were confidently wrong and I only found
out by running the thing.

## Tooling

Python standard library and Go, no frameworks, two CPU cores. The two small
tools run on a bare Python install with nothing to install first; the larger
projects use Next.js and Prisma.

Everything published is MIT licensed. Every project has a CI workflow that runs
on each push, and the READMEs carry the test counts rather than a claims
summary — where a number is unflattering, it stays in.
