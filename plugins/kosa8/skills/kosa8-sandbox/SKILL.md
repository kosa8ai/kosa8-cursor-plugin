---
name: kosa8-sandbox
description: Run untrusted, generated or risky commands and code in a sealed kosa8 Cloud sandbox (a microVM) instead of the user's machine; use checkpoints, rewind and fork for risky or exploratory work.
---

# kosa8 sandbox

kosa8 Cloud gives you sealed Linux machines (microVMs with their own kernel)
through the `kosa8` MCP server. Nothing you run there can touch the user's
computer, files or credentials.

## When to use

- Running code you wrote or downloaded and have not reviewed: install scripts,
  unknown packages, test suites of an unfamiliar repo.
- Anything that needs root, a clean Linux, or a throwaway environment.
- Trying several approaches in parallel, or a risky step you may want to undo.

Use the user's own terminal instead for ordinary edits and commands in their
project that they expect to run locally.

## How

1. `sandbox_create` (optionally a `template` from `templates_list`). A sized or
   image sandbox may return `state: creating`: poll `sandbox_get` until
   `running`.
2. Put files in with `file_write`, run with `sandbox_exec` (`command` for a
   shell string, or `argv`). Work in `/workspace`. For anything longer than
   about a minute, pass `background: true` and poll `sandbox_exec_status`.
3. Python: `code_run` keeps variables between calls.
4. A web app or API: `preview_create` gives a port an https URL; share
   `preview_link` with the user.
5. Before a risky step: `checkpoint_create`; if it goes wrong,
   `sandbox_rewind`. To try approaches side by side: `sandbox_fork`.
6. When done: `sandbox_delete`, unless the user wants to keep it
   (`sandbox_sleep` keeps its state and frees its compute).

## Rules

- Tell the user what you are about to run before running it, and show the
  output that matters.
- Never put the user's secrets into a sandbox unless they ask you to.
- `sandbox_delete`, `snapshot_delete` and `file_delete` cannot be undone: only
  delete what you created in this task, or what the user named.
- If a tool says payment is required or a plan limit is reached, tell the user
  the message and the link it gives, word for word; do not try to work around it.
