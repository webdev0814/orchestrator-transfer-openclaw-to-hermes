# Orchestrator Transfer Runbook: OpenClaw to Hermes

Use placeholders such as `<face-host>`, `<brain-host>`, `<private-ip>`, `<webhook-secret-file>`, and `<backup-script>`. Never publish real hostnames, tokens, IP addresses, chat IDs, usernames, or personal memory.

## Phase gates

1. **Phase 0 — Hermes preflight**
   - Enable Hermes webhook only on a private network interface.
   - Store webhook secret outside config when possible.
   - Subscribe routes for migrated jobs.
   - Verify valid HMAC returns 200 and invalid HMAC returns 401.

2. **Phase 1 — OpenClaw outbound + SSH shim**
   - Install a shared webhook secret as `0600 root:root`.
   - Install a notifier with raw hex `X-Webhook-Signature`.
   - Add spool fallback for failed webhook delivery.
   - Install forced-command SSH shim with literal whitelist.
   - Use a source-address restriction in `authorized_keys`.
   - Verify success, failure, deny, metacharacter rejection, and non-private-source rejection.

3. **Phase 1.1 — Shim timeout hardening**
   - Wrap every whitelisted command in `timeout --kill-after=10`.
   - Keep command-specific bounds, e.g. short shell utilities 10s, backups longer, known-slow diagnostics bounded.
   - Do not widen command whitelist during this patch.

4. **Phase 2A — Pattern A reporting only**
   - Existing OpenClaw cron remains the executor.
   - Replace direct cron command with wrapper that runs existing script and posts result.
   - Hermes remains passive recipient only.
   - Verify manual success, backup failure event, and Hermes-down spool/replay.
   - Require 3 clean scheduled runs, then passive soak to day 7.

5. **Phase 2B — Remote trigger**
   - Locked until soak passes and owner approves.
   - Hermes triggers via SSH shim; OpenClaw executes and reports result.

## Required rollback file before soak

Place a rollback one-pager next to any wrapper, e.g. `wrapper.ROLLBACK.md`, covering:

- Restore old crontab line.
- Disable wrapper without deleting it.
- Drain or preserve spool.
- Verify rollback.

## Baseline metrics

Capture a JSON baseline before scheduled soak:

- backup size per agent/service
- wrapper duration
- notify success reference
- tolerance threshold
- cron and watchdog lines

## Do-not-do list during soak

- Do not start Phase 2B.
- Do not widen sudoers or SSH shim whitelist.
- Do not move memory source-of-truth.
- Do not alter unrelated cron entries.
