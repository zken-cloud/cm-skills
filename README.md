# cm-skills

CodeMender **Agent Skills** for [Google Antigravity](https://antigravity.google) —
the portable `SKILL.md` format read by **AGY, the AGY CLI, and the AGY IDE** (2.0 included).

Part of the CodeMender CI Lab. Full walkthrough (install + sample use):
**https://cm-ci-lab.cedemo.app/skills**

## Skills

### `codemender-fanout`
Drive the CodeMender CLI (`cm`) across a whole directory from Antigravity — **find**
many vulnerabilities, **verify** them in parallel, **fix** the verified ones in parallel —
each parallel worker isolated so the single-writer SQLite state never corrupts, and every
`cm` run wrapped in an OS-native sandbox (macOS `sandbox-exec`, Linux gVisor `runsc` — no
Docker). Runs locally as your own ADC identity; no CI, bucket, or secret store.

## Install

Copy the skill folder into one of the two locations Antigravity reads — both work across
AGY, the AGY CLI, and the AGY IDE:

```bash
git clone https://github.com/zken-cloud/cm-skills

# Global — all workspaces
mkdir -p ~/.gemini/config/skills
cp -R cm-skills/codemender-fanout ~/.gemini/config/skills/
chmod +x ~/.gemini/config/skills/codemender-fanout/scripts/*.sh

# — or — Workspace — this project only
mkdir -p .agents/skills
cp -R cm-skills/codemender-fanout .agents/skills/
```

Then, in Antigravity, ask the agent to use it:

> Use the codemender-fanout skill on this repo: scan `routes/`, verify the findings,
> and fix the CRITICAL ones. Show me the patches before I open a PR.

## Prerequisites

- `cm` on PATH (`codemender-fanout/scripts/bootstrap-cm.sh` fetches it), authenticated ADC
  entitled to the preview (`gcloud auth application-default login` +
  `set-quota-project`), and `git` / `jq` / `unzip`.
- **macOS** — `sandbox-exec` is built in.
- **Linux** — gVisor `runsc`
  (`curl -fsSL https://storage.googleapis.com/gvisor/releases/release/latest/$(uname -m)/runsc -o runsc && chmod +x runsc && sudo mv runsc /usr/local/bin/`);
  set `SBX_RUNSC_FLAGS=--rootless` on an unprivileged laptop.

See each skill's `SKILL.md` for the full contract and tuning knobs.
