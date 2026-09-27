---
name: preview
description: >-
  Publish a temporary HTTPS demo URL for a local web app using the preview CLI.
  Use when implementation is done and the project needs a demo, when the user
  asks to preview / share / demo the app, or when finishing UI or full-stack
  work that should be reviewed in a browser. Also use to stop or clean up a
  preview after the user finishes reviewing.
---

# preview

Expose a localhost HTTP port on a temporary HTTPS hostname with the `preview` CLI. The CLI does not start apps, install deps, or touch git — you do that, then point `preview` at the port.

Install the CLI from https://github.com/harshalranjhani/preview-cli if it is missing on the machine.

## When to use

Apply this skill when **all** are true:

1. The change is implemented enough to demo (or the user asked for a preview).
2. The repo has (or you can start) a runnable HTTP app on localhost.
3. A public HTTPS URL would help the user review.

Skip when there is nothing to render in a browser (CLI-only, library, infra-only) unless the user asks anyway.

## Workflow

### 1. Start the app

- Use the project's normal dev command (`pnpm dev`, `npm run dev`, `vite`, `flask run`, etc.).
- Bind to localhost (`127.0.0.1`). Identify the listening HTTP port.
- Wait until the port accepts connections before publishing.

### 2. Choose TTL and name

| Situation | `--ttl` |
| --- | --- |
| Quick look / small UI tweak | `30m` |
| Normal feature review (default if unsure) | `2h` |
| Longer review session the user will keep open | `4h` |
| User explicitly wants no expiry | `0` |

Do **not** use `--ttl 0` unless the user asks. Prefer shorter TTLs so forgotten routes expire.

Pick a short DNS-safe `--name` for the task (`auth-form`, `pricing-page`). Prefer lowercase letters, digits, and hyphens.

### 3. Publish

```bash
preview http <port> --name <task-name> --ttl <ttl> --json
```

Parse JSON for `url`, `id`, and `expires_in` / `expires_at`.

On success, tell the user:

- the HTTPS **URL**
- the preview **ID**
- when it **expires**

Keep the app process and preview running while they review. Do not commit or push just because the preview succeeded.

### 4. After review

When the user is done reviewing, approves, or says to tear down:

```bash
preview stop <id>
```

Then stop the development server if you started it.

If they want changes, keep working; reuse the same preview when the port is unchanged, or create a new one with a fresh `--name` / TTL after restarting the app.

## Commands

```bash
preview http 3000 --name auth-form --ttl 2h --json
preview list --json
preview status <id> --json
preview stop <id>
preview stop --project          # all previews for the current project
preview doctor                  # if create fails on this machine
```

Optional project file (safe to commit):

```bash
preview init                    # writes .preview.yaml with project name
```

## Host / framework gotchas

If the URL loads but the app rejects the request:

- Allow the preview hostname (Vite `server.allowedHosts`, Django `ALLOWED_HOSTS`, Rails `config.hosts`, Next trusted hosts, etc.).
- Keep the app on `127.0.0.1`; only Caddy should be public.

## Safety

- Only publish normal app HTTP ports. Never expose databases, Redis, admin-only ports, or non-HTTP services.
- Do not put DNS tokens or server secrets in `.preview.yaml` or the repo.
- One preview per task is fine; several may share a port. Stopping one does not stop the app or other previews.

## Prerequisites

`preview` must be installed and the server configured (`preview doctor` should pass). If the binary is missing, point the user at https://github.com/harshalranjhani/preview-cli — do not invent Caddy or DNS setup.
