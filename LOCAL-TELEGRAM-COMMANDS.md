# Local Telegram Command Overlay

This fork has one local purpose: preserve the Telegram `/claude` and `/codex`
status commands across Hermes upgrades. It is not an installer/release-testing
fork. The cross-platform install/update workflow has therefore been removed.

## Normal upgrade procedure

When asked to upgrade Hermes, use this bounded sequence:

1. Update the Hermes checkout by the agreed upstream method.
2. Reapply and validate the local command overlay:

   ```bash
   /home/hermes/.hermes/scripts/hermes-ops/ensure-claude-codex-commands.sh
   ```

3. Confirm the script reports that both commands are present. If it reports a
   conflict, it aborts cleanly: do not force a cherry-pick or start editing the
   gateway while it is running.
4. The script requests a non-blocking gateway restart when it had to restore
   the overlay. Verify the Telegram command menu and invoke `/claude` and
   `/codex` once each.

## How to ask Hermes

Say:

> Update Hermes, reapply the local Telegram `/claude` and `/codex` overlay,
> restart safely if needed, and verify both commands in Telegram.

## What maintains it

- The local overlay is retained as commit `988cdb62bb` and as
  `~/.hermes/local-patches/claude-codex-telegram-commands.patch`.
- The restore script validates the command registry, Telegram menu placement,
  gateway handlers, and renderer wiring before it commits anything.
- A local Hermes cron watchdog runs the same restore script every 30 minutes.
  It does nothing when the overlay is intact. If an upgrade removes the
  commands, it reapplies the overlay, validates it, and safely requests a
  gateway restart.

This is deliberately narrow: it protects the two commands we use, without
running release-installer tests for operating systems and update paths we do
not maintain.
