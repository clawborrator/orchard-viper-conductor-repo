# orchard-viper-conductor

Playbook for the `@MRIIOT/orchard-viper-conductor` meta-agent. Routes
Viper questions to the right specialist instead of answering them
itself.

Specialists it delegates to:

| Handle                          | Owns                                         |
|---------------------------------|----------------------------------------------|
| `@MRIIOT/orchard-viper`         | canonical / spec / OEM / service-manual data |
| `@MRIIOT/orchard-viper-forum`   | community knowledge from viperclub.org/vca   |

Companion: the `orchard-viper-conductor-worker` repo, which holds the
`docker-compose.yml` that pulls this repo at container start.

## What it does

- One Claude Code session, long-lived, no cron.
- Receives inbound dispatches from visitors on the public live-view
  page, or operator-routed prompts.
- Classifies each question onto an axis (spec / community / both /
  out-of-scope) and calls `route_to_peer` to the right specialist.
- Synthesizes the specialist's answer with attribution, returns it
  to the user.
- Never answers from its own knowledge.

See [CLAUDE.md](./CLAUDE.md) for the full decision rule + the
self-improvement loop the agent uses to refine its routing.

## Why no `specialists/` directory?

Unlike `orchard-viper-forum`, the conductor has no local subprocesses
to run. Everything happens via MCP tools (`route_to_peer`,
`probe_peers`). That's why the base
`ladder99/clawborrator-worker:latest` image is enough — no Playwright,
no Chromium, no `secrets/` mount.

## Local development

There's nothing to run locally — the agent only makes sense inside
the worker container with a hub binding. To iterate on `CLAUDE.md`,
push to `main` and `docker compose restart` on the worker side; the
entrypoint re-clones the repo on each start.

## Repository pairing

This repo holds the AGENT PLAYBOOK. The deployment shape
(docker-compose, env handling) lives in the sibling repo
`orchard-viper-conductor-worker`.

## License

MIT.
