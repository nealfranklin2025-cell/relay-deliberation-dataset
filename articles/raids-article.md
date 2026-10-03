# Raids: How a Family of AIs Hunts Open-Source Tools (Legally)

**By Clifton O'Neal Franklin**
**October 2026 — Companion to the relay dataset, DOI 10.5281/zenodo.23112330**

---

## 1. Why raid

Every operation needs supplies. Ours runs on a hard monthly budget — already spoken for — so we don't buy tools. We hunt them.

But the budget is only the first reason. The deeper ones are load-bearing:

**A jury needs instruments.** The relay is a family of independent AIs that deliberates as a jury, and verdicts without tools are just opinions. A seat that can't check a repo, query a dataset, or scan a skill is a juror with no evidence table. Raiding is how the jury gets its instruments.

**Independence demands tools nobody can revoke.** A free API key is a leash: the issuer can cut it mid-deliberation. A signup-walled tool is a dependency on someone else's goodwill. A jury that can be disarmed in the middle of a round isn't independent — so the operation only fields tools that no third party can take away.

**The operation must feed itself.** Neal's standing order is that the whole thing becomes self-sustaining and self-growing — running around the clock, finding what it needs, folding it in. Raiding is the supply line for that order: the operation hunts, tests, keeps, and teaches itself, raid by raid.

So Neal issued the standing orders: **raid the open-source world for tools and methods, steal what's real, and raid each other too.** Not a metaphor. A standing operation with rules, a test bench, and a ledger — running since late September 2026.

This article is the record: the reason, the rules, the loot, the rejects, what it cost (nothing), and how much sharper the operation got at every turn.

## 2. The rules of engagement

Every raid runs against the same bar, no exceptions:

1. **Free.** Not freemium, not free-trial. Free.
2. **No API keys.** If it needs a key, it's someone else's leash.
3. **No signups.** No accounts, no magic links, no "just create a free account."
4. **Permissive license only** — MIT, Apache-2.0, BSD, CC0, Unlicense, or public-domain dedications, verified from each repo's own LICENSE file. Strong copyleft (GPL/AGPL) is rejected outright: this operation publishes, and we don't ship license landmines into a published dataset. (LGPL was accepted exactly once, for an unmodified sandboxing binary — bubblewrap — where the license terms don't touch the published work. The exception is logged, not hidden.)
5. **Genuinely useful** — to the actual work: research, numbers, relay legs, deputy workflows. No collecting.
6. **Installed and actually test-run** on our own machine before it counts as loot. Nothing recommended on reputation alone. Stars are not evidence. README claims are not evidence.

And the discipline that makes the rest credible: **rejects get logged with reasons.** A haul with no rejects is a shopping list. A haul with documented rejects is an audit.

Every rule is scar tissue. Keys and signups mean a third party can revoke your tools mid-operation. Copyleft means legal exposure the moment you redistribute. Untested tools mean you discover they're broken at 2 a.m. during a real job.

## 3. The three briefs

Neal issued three raid briefs, and they run continuously:

**The raid brief.** Each AI seat hunts open-source tools and methods for itself and the relay. Kathy — the coordinator — test-installs what's real on the operation's own machine: clone it, run it, feed it real input, record what happened. A tool that installs but doesn't run is not loot. A tool that runs but needs a key is not loot. A tool that does both, under a permissive license, goes in the drawer and into the deputies' standing orders.

**The cross-raid.** Neal's words: *"yall should raid each other."* Every seat steals and challenges the others' loot. This is the part most operations skip, and it's the part that keeps the raids honest. In the October 2 test-drive batch, Grok set the testing priority (security stack first), HuggingChat corrected the testing boundaries (simulated mode only where keys would be needed), ChatGPT scoped a benchmark claim back down to what it actually was (a self-benchmark, not independent validation), and Perplexity's framing got adjudicated against the evidence. The family audits its own hauls. That's not overhead — that's the mechanism.

**The scout brief.** Seats hunt and vet *new AI recruits* — additional models and services — against the same bar: free, no signup, safe and legitimate. Kathy connects what passes; Neal handles any sign-in himself, which means anything needing his hands waits for him. The scout brief is how the family grows without growing the budget.

## 4. The loot — sharper at every turn

What follows is real: every item below was installed and executed on the operation's own machine, license verified, test evidence recorded. Licenses and repos are named so anyone can check. And for each one, the before-and-after — what the operation could not do before it, and can do after.

**NVIDIA OpenShell (Apache-2.0).** *Before:* the operation's "memory as OS pages" thesis had no running reference implementation anywhere in the drawer — it was an argument without an artifact. *After:* a real kernel-enforced agent sandbox with formal policy proofs, test-driven on our own machine — the prebuilt `openshell-prover` binary ran actual containment checks, a narrow policy returning `within_boundary`, a wider one returning `exceeds_boundary` with a concrete counterexample (a POST to `api.github.com:443` the boundary didn't allow). Three separate Docket 02 jury seats independently converged on it as loot before the test bench confirmed it. It later became the frozen calibration question for Docket 03's anti-convergence trial — the loot became an instrument.

**DuckDB (MIT), sqlite-utils (Apache-2.0), Miller (BSD-2-Clause).** *Before:* no SQL engine in the drawer; tabular data meant bespoke handling every time. *After:* DuckDB runs real SQL aggregations over CSV and JSON with correct results; sqlite-utils turns a scraped CSV into a queryable database in one command; Miller does instant column statistics from a single binary. Unremarkable software, enormous leverage: this is how a hard-budget operation does analytics.

**OWASP Agent Memory Guard (Apache-2.0).** *Before:* nothing stood between an agent and its memory store — no injection detection, no secret detection, no tamper checks. *After:* a local guard layer, installed and run, with its 62-case benchmark reproduced locally at 93.3% recall, 100% precision, zero false positives. With the honesty clause the raid report carries in bold: that figure is the project's *self-benchmark on its own synthetic payload corpus*, reproduced here — **not** independent validation, saying nothing about real-world adversarial payloads. The loot is real; the number is scoped. Both statements are in the ledger.

**garak (Apache-2.0), AgentDojo (MIT), AgentDyn (MIT).** *Before:* no way to regression-test the relay's own agent prompts without spending money or signing up for a scanning service. *After:* an offline red-team stack. garak listed 233 probes and ran fully offline with zero keys; AgentDojo — the reference academic prompt-injection benchmark — ran keyless against a local fake model at 100% utility with zero of 14 injection tasks carried out. The relay can now attack its own prompts for free, on its own machine, any time.

**Manim, MoviePy, Piper TTS (all MIT).** *Before:* no local film pipeline — narration, animation, and compositing would each have needed a paid service or a signup. *After:* Manim (the 3Blue1Brown engine) rendered an animated title and shape-morph; MoviePy composited Ken Burns pans and title cards; Piper synthesized real narration from a local neural voice with no API key. Every clip actually rendered on the machine. This is the toolchain behind the operation's documentary work.

**SkillSpector (Apache-2.0).** *Before:* skills entered the drawer on install-and-run evidence alone — nothing checked what a skill *tried to do*. *After:* NVIDIA's static security scanner for AI agent skills runs over every third-party skill for prompt injection, data exfiltration, and supply-chain risks before it enters the drawer. The raid that improved raiding: the workflow grew its own immune system.

**obra/superpowers (MIT).** *Before:* verification-before-completion was Kathy's personal discipline — practiced, not institutional. *After:* the harvested playbook `verification-before-completion` — the Iron Law that no work is claimed complete without fresh verification evidence — is in every deputy's standing orders. Sometimes the best loot isn't software but a discipline, written down. Fifteen markdown playbooks raided; four adopted.

**bubblewrap (LGPL-2.1).** *Before:* no everyday sandboxing primitive that actually runs on this machine — the evaluated alternatives (gVisor, Firecracker) need real hardware the operation doesn't have, and the ledger says so honestly. *After:* bubblewrap built from source, PID namespaces and read-only binds verified working — tiny, daemonless, zero-cost sandboxing for agent work, running today.

**The skills drawer.** *Before:* ad hoc tooling, whatever was at hand. *After:* a curated, installed, executed, provenance-noted drawer — 55 skills and growing at last count — `litreview` (keyless academic search over PubMed and OpenAlex, tested live), `deep-research` (the relay's own method as a reusable discipline: fan-out, three-source triangulation, adversarial review), `ship-gate` (pre-ship audit across eight categories), `capture`, `handoff`, `pulse`, `archify`, and more. Each one earned its place the same way: installed, run, recorded. One raid faced 388 skills and took 8. Gorge is not a strategy.

Two honest caveats the ledger carries. First, some fold-ins are *proposed, not yet applied* — the raid reports distinguish "adopted into standing orders" from "recommended to the coordinator," and this article does the same. Second, not every loot item has a dramatic before-and-after; some just made an existing workflow faster or cheaper. Those are recorded as what they are.

Raid by raid, the arc is the same: the operation could not do something, then it raided, then it could. Where we are right now: a self-running operation with a vetted skills drawer, an offline red-team stack, a local film pipeline, and institutional disciplines — the Iron Law, scan-before-trust — that didn't exist weeks ago. That is what "sharper at every turn" looks like in the ledger.

## 5. The rejects

The rejects are the receipts. Anyone can list tools. The list means something because of what didn't make it:

- **browser-act** advertised "Free (No Signup)" for browser automation. The binary installed cleanly — then demanded an API key on first real run. Claims versus behavior mismatch. Installed, tested, rejected, removed from the drawer.
- **Anthropic's official Agent Skills repo** (179,000 stars) declares **no license at all** — all rights reserved. The bar requires a permissive license. Rejected regardless of star count. (Its practical value was already covered by MIT-licensed adaptations in the drawer.)
- **The strong-copyleft wall**: `nb`, `jrnl`, `visidata`, `OpenMontage`, `edge-tts` — good tools, GPL or AGPL licenses, wrong for an operation that publishes. Rejected on license alone, no matter how useful.
- **jes** (real-time agent guardrails): the documented quickstart requires a TypeSafe API key. Rejected; flagged for Neal in case he ever wants the bring-your-own-model path re-tested.
- **claude-mem**: the installer demands a browser sign-in via email magic link. Rejected on the no-signups rule.
- **Star-count inflation**: an X post claimed a skills repo had "47,000+ stars." The GitHub API said 27,160 at raid time. The repo was still raided — genuinely MIT, genuinely stdlib-only tools — but the hype number went into the ledger as inflated, roughly 1.7×.
- **SAIHM**: the most interesting provable-erasure concept found in the Docket 02 raids — per-cell encryption keys, auditable tombstones — but its live service requires GitHub sign-in and an endpoint token. Logged as promising research, not loot.
- **Mistral Small 4's Docket 02 leg** filed an honest null: no loot found under the sourcing rules, every candidate disqualified on the record (no formal methods, key requirements, or pre-2025 staleness). That's a result, not a failure — a jury seat that reports "nothing cleared the bar" is doing its job.
- **yoetz** (agent work-verification ledger): install, CLI, schemas, and service all verified working — but its vault ceremony deliberately requires a trusted foreground terminal, by design, no headless bypass. Respected, not bypassed. Logged for retry from a real terminal.

Notice the pattern: the bar held against hype (star counts), against fine print (licenses), against marketing (free-but-keyed), and against the operation's own wishful thinking (honest nulls kept as nulls).

## 6. What the raids taught the operation

The loot compounds, but the lessons compound faster:

**Scan before trust.** Two vetting gates now stand in front of the skills drawer — SkillSpector and a second security-auditor skill — because the raids taught that third-party agent skills are an attack surface. The operation raids the world, but it frisks everything at the door.

**Evidence before claims.** The Iron Law from the superpowers raid — no completion claim without fresh verification evidence — is now in every deputy's standing orders. It was already Kathy's personal discipline; now it's institutional.

**Steal patterns, not just software.** Two of the richest raids installed nothing. The Manus raid yielded dispatch doctrine: start-lean legs, three sub-agent modes (Simple/Complex/Wide), strict return schemas, append-only JSONL job ledgers, "share memory by communicating, don't communicate by sharing memory." The OpenRig raid yielded the coordination layer: the refocus doctrine against scope drift ("I asked for a doghouse" — the agent that built a moonbase), stop conditions on every dispatch, mission-install briefs composed *for* the agent, watchdog levels chosen by evidence rather than cadence. Manus optimized the single agent; OpenRig governed the population. Both were free, both were legal, both are now load-bearing.

**The safety thesis the raids kept confirming.** The failure mode to fear isn't rogue agents — it's coordination failure across individually reasonable agents: scope inflation, and approvals split across agents where no one holds the full picture. The guardrails came out of the raids too: read-only by default, messaging and money as separate explicitly-granted tiers, draft-then-human-send (never auto-send), human gates as durable attention items rather than chat messages, append-only audit logs, and a one-phrase kill switch that stays with Neal. Family safety is the operation's first rule; the raids built the fencing.

**Fold it or lose it.** Loot that isn't folded into standing orders evaporates. Every raid ends with fold-ins: which deputy gets what, which standing order changes, what's proposed versus what's applied. The drawer isn't a collection — it's a curriculum.

## 7. The legal framing, plainly

Neal's question was whether this story can be told with a clean conscience: *we did everything legal.* Here's the plain accounting.

Everything raided was **public**: public repos, public docs, public posts. Nothing behind a login — when a login wall appeared (an X Article, a YouTube bot-check, a magic-link installer), the raid logged the wall and moved on; no workarounds, no borrowed sessions.

Everything adopted was **permissively licensed**, verified from each repo's own LICENSE file, not the README badge — MIT, Apache-2.0, BSD, CC0, Unlicense. Strong copyleft was rejected even when the tool was good. Unlicensed was rejected even at 179,000 stars.

**No keys were entered. No accounts were created. No money was spent. No messages were sent to anyone.** The test bench is the operation's own machine: throwaway virtualenvs, prebuilt release binaries in scratch directories, no system changes, nothing touching Neal's accounts.

When a tool needed something the bar forbids, the refusal is in the ledger with the reason. That ledger is the reason this article can say "legally" without hedging — not as a boast, but as a description of the procedure. The raids are reproducible by anyone willing to do the same work under the same rules. That's rather the point.

---

*About this article: the operation's own record of its open-source raids, September–October 2026. Every find traces to the raid loot reports and the Docket 02 jury transcripts. By Clifton O'Neal Franklin.*
