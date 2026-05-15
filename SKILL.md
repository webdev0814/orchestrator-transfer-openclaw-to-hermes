---
name: orchestrator-transfer-openclaw-to-hermes
description: Use when planning, executing, auditing, or continuing a staged orchestrator transfer from an OpenClaw-facing assistant to a Hermes operational brain, including webhook handoff, notifier scripts, restricted SSH shims, cron migration, rollback, watchdogs, soak gates, and memory split deferral.
---

# Orchestrator Transfer: OpenClaw to Hermes

Use this skill for staged handoff work where OpenClaw stays the user-facing channel layer and Hermes becomes the operational orchestrator.

## Non-negotiables

- Never advance phases without explicit owner approval.
- Preserve agent identities: do not alter names, IDs, workspaces, agentDirs, identity files, or persona files unless explicitly requested.
- Never print or commit bot tokens, HMAC secrets, private keys, hostnames, IP addresses, chat IDs, usernames, or personal memory content.
- Prefer additive changes first; delay deletion until soak windows pass.
- Keep rollback instructions next to installed wrappers.
- Hermes remains passive until remote-trigger phase is explicitly unlocked.

## Phase model

1. **Phase 0:** Hermes webhook listener, HMAC routes, private-network-only bind.
2. **Phase 1:** OpenClaw outbound notifier, root-only secret, spool fallback, restricted SSH shim.
3. **Phase 1.1:** Timeout-wrap all whitelisted shim commands.
4. **Phase 2A:** Pattern A cron reporting: OpenClaw cron still runs; wrapper posts results to Hermes.
5. **Phase 2B:** Hermes trigger via SSH shim; locked until soak passes.
6. **Later:** curated memory split, never blind migration.

## Verification pattern

For each phase, verify both happy path and failure path:

- valid HMAC succeeds
- invalid HMAC fails
- spool on outage, replay after recovery
- SSH whitelist allows only intended literals
- metacharacters denied
- source restriction enforced
- logs record RUN/EXIT/DENY without secrets
- no unrelated listeners, crons, skills, or memory are modified

## Watchdogs and soak

Before a soak:

- create rollback doc
- capture baseline metrics
- install automatic watchdog/alerting
- schedule confirmation checks

Do not declare a phase fully complete until scheduled runs and passive soak pass.

## References

For detailed reusable runbook text, read `references/runbook.md`.
