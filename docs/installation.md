# Installation

The skill is a directory: `SKILL.md` at the root, reference files under `references/`. Any Claude surface that supports agent skills can run it; only the location differs.

## Claude Code

```bash
git clone https://github.com/<you>/fable-method.git
mkdir -p ~/.claude/skills
cp -r fable-method ~/.claude/skills/fable-method
```

You'll know it worked when `/fable-method` appears in the slash-command list of a new session (restart Claude Code if it doesn't).

For a single project instead of globally, use `<project>/.claude/skills/fable-method`.

## Cowork / Claude Desktop

Two routes:

1. **Settings → Capabilities → Skills** — add the skill folder directly.
2. **`.skill` package** — zip the directory so `SKILL.md` sits at the archive root and rename to `fable-method.skill`:

   ```bash
   cd fable-method && zip -r ../fable-method.skill SKILL.md references/
   ```

   Opening the file in the Claude desktop app shows a "Save skill" install button.

You'll know it worked when the skill shows up in the available-skills list of a new conversation.

## claude.ai (web, no filesystem)

The protocol runs in degraded mode: paste `SKILL.md` (and any reference file you need) into a Project's knowledge. The memory loop is manual on this surface — the skill emits journal entries as copy-paste blocks and you keep them in Project knowledge yourself. This is documented in `references/memory.md` § Platform resolution.

## Bootstrapping the memory tiers

Nothing to do manually. On the first substantive task in a folder, the skill offers to create:

- `fable-memory/` (global tier) — `INDEX.md`, `journal/`, `playbook/`. In Claude Code this lives at `~/.claude/fable-memory/`; in Cowork, at the root of your connected folder.
- `.fable/PROJECT.md` (project tier) — facts and quirks specific to the working folder.

Both are plain markdown, human-auditable, and safe to delete if you change your mind. In Cowork, connect a stable parent folder (e.g. `Documents/Projects/`) rather than individual subfolders so one `fable-memory/` serves everything.

## Making it the default

Skills trigger on matching intent, but the recall/record loop pays off most when it runs on all substantive work. Add to your global preferences or `CLAUDE.md`:

> I use the fable-method skill as my default working protocol. Invoke it at the start of any substantive work — anything multi-step, producing durable artifacts, or likely to span sessions — and in any folder that already has `.fable/PROJECT.md`. Never skip its Record step.

## Pitfalls you'll actually hit

- **Skill triggers but memory never persists (Cowork):** you didn't connect a folder, so there is nowhere durable to write. Connect a folder before expecting the learning loop to compound.
- **`INDEX.md` grows past ~200 lines:** the skill will offer consolidation — accept it. An unbounded index degrades retrieval, which is the whole point of the system.
