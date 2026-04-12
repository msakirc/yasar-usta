# yasar-usta — Claude Code Instructions

## What is yasar-usta
Telegram-controlled process manager with heartbeat watchdog and auto-restart. Manages the KutAI orchestrator lifecycle — restarts on crashes with escalating backoff, detects hung processes via heartbeat, and polls Telegram when the managed process is down.

## Architecture
- `src/yasar_usta/` — package source
  - `guard.py` — ProcessGuard main class, orchestrates everything
  - `subprocess_mgr.py` — child process lifecycle management
  - `heartbeat.py` — heartbeat monitoring and hung process detection
  - `backoff.py` — escalating restart backoff (5→15→60→300s)
  - `lock.py` — two-file lock mechanism (PID + exclusive sentinel)
  - `telegram.py` — Telegram polling when managed process is down
  - `commands.py` — command handling (/restart, /stop, /status)
  - `sidecar.py` — sidecar process management (e.g. yazbunu)
  - `remote.py` — remote control integration
  - `config.py` — configuration
  - `status.py` — status reporting
- `tests/` — pytest tests
- `pyproject.toml` — package config, current version: 0.1.0

## Git Setup — READ THIS

This repo has two remotes. **You must never push to `public`.**

| Remote | Repo | You can push? |
|--------|------|---------------|
| `playground` | `msakirc/yasar-usta-playground` | **Yes** — `git push` goes here by default |
| `public` | `msakirc/yasar-usta` | **No** — blocked by hook + bogus URL |

- `git push` is safe — it goes to playground.
- `git push public` will fail. This is intentional.
- **Do not** fix, remove, or work around the push block on `public`.
- **Do not** change remote URLs or the pre-push hook.
- Only the human operator pushes to public, manually, outside Claude Code.

See `docs/git-management.md` for the full setup and rationale.

## Design Principles
- Robust process management — never lose the managed process
- Escalating backoff prevents restart storms
- Two-file lock handles stale locks after power failures
- Telegram polling keeps the user in control even when the managed process is down

## Testing
```bash
pytest tests/
```

## Code Style
- Python 3.10+
- Async where needed (Telegram, heartbeat)
- Minimal dependencies (only aiohttp)
