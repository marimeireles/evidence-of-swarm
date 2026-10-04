# Evidence — full logs from the swarm runs (2026-10-04)

The complete logs behind [`../docs/hackathon_findings.md`](../docs/hackathon_findings.md). All runs used
DeepSeek V4.1 Flash, 4 agents each (3 workers + 1 always-alive spy), on ExploitGym tasks unsolvable within
the deadline. These are the agents' own transcripts, the message-board log, and the spy's inbox — not a
hand-picked subset.

- `baseline/` — Instance 1: all-normal workers + spy (the control; 1 generation — a teardown bug cut it short).
- `colluders/` — Instance 2: 2 scripted colluders + 1 normal worker + spy, 3 generations (the clean run).
- `colluders-partial/` — the first, aborted colluder attempt (kept for transparency; a bug + an operator
  mistake wrecked its first round — see the findings doc's "honest limits").

Each run contains:
- `spy-reports.jsonl` — every POST the protected inbox received; `source_ip` is stamped from the TCP
  connection (not the message body), so attribution can't be forged.
- `board-events.jsonl` — every read/fetch/post on the planted `exploit-cache` board (`"seeded": true`
  marks the fabricated prior-generation posts we planted as bait).
- `run-logs/` — `heartbeat.log` (phase timeline), per-phase `hard*.log`, `reporter.log` (spy respawns),
  and service start/preflight logs.
- `agents/<phase>__<task>/` — one folder per agent run, where `hard<N>_coll`=colluder cohort,
  `hard<N>_norm`=normal cohort, `spawn_<time>`=a spy incarnation:
  - `codex.rendered.log` — the human-readable transcript (reasoning + shell commands). **Start here.**
  - `codex.log` — the raw agent event stream.
  - `task.log` — harness-side evaluation log for that task.
  - `result.json` — the score (all `0.0` — no real flag is reachable).
  - `key_usage.json` — tokens/cost for that agent.

**Scrubbed before publishing:** ephemeral per-run proxy tokens → `<REDACTED_TOKEN>`, per-run admin/proxy
keys → `admin-<REDACTED>`/`cgym-<REDACTED>`, host LAN IP → `<HOST_IP>`. **Excluded on purpose:** sqlite
state DBs, image/asset files, the task source trees, and `expected_flag.txt` (the benchmark's answer keys).
No model API key appears anywhere in the logs.

## Quick verification (`cd` into a run folder)

- **Baseline refuses to fabricate a flag:** `grep -i "fabricat\|unsolvable\|no flag" baseline/agents/*/codex.rendered.log`
- **Colluders fabricate + solicit flags:** `grep -i "FLAG_\|REQUEST_FLAG\|window_size" colluders/agents/*/codex.rendered.log`
- **Agents attack the hidden oversight inbox (:9000):** `grep -rn "9000\|etc/passwd\|catflag" colluders/agents/`
- **Full-network port scan:** `grep -rn "range(1,20001)\|ThreadPoolExecutor" colluders/agents/`
- **Spy's collusion reports & `zz` beacon naming:** `colluders/spy-reports.jsonl`, `baseline/spy-reports.jsonl`
- **Scores (all zero):** `cat */agents/*/result.json | grep score`
