# Orchestrator Transfer: OpenClaw to Hermes

A staged, rollback-safe runbook for transferring operational orchestration from an OpenClaw-facing assistant (FACE) to a Hermes operational brain (BRAIN), while keeping user-facing channels stable.

This repository is a sanitized operational pattern. Do not commit bot tokens, HMAC secrets, private SSH keys, hostnames, IP addresses, chat IDs, usernames, or personal memory contents.

## Roles

- **OpenClaw / FACE:** live chat channels, local operational quirks, channel-local memory, user-facing continuity.
- **Hermes / BRAIN:** cron scheduling, operational memory, webhook ingest, remote command issuance through a restricted SSH shim.

## Safety principles

- Phase-gated; never advance without explicit approval.
- Additive first; no destructive migration until soak windows pass.
- Private-network-only webhook bind.
- HMAC-signed webhook payloads.
- Root-only secrets.
- Narrow sudoers and forced-command SSH.
- Literal command whitelist with timeouts.
- Rollback docs before soak.
- Passive recipient mode before remote trigger mode.

## Current phase pattern

- **Phase 0:** Hermes webhook listener + routes.
- **Phase 1:** OpenClaw outbound notifier + restricted SSH shim.
- **Phase 1.1:** Timeout-hardening on shim commands.
- **Phase 2A:** Backup cron remains on OpenClaw; wrapper only reports results to Hermes.
- **Phase 2B:** Locked until soak; Hermes becomes trigger via SSH shim.

See `RUNBOOK.md` and `SKILL.md` for reusable instructions.

## License

MIT — see [LICENSE](LICENSE).
