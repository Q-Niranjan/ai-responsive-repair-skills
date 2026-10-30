# AI Responsive Repair Skills

An agent skill that finds and fixes responsive layout bugs (overflow, clipping, truncation, broken breakpoints) in any web frontend. It works with Cursor, Claude Code, Codex, Windsurf, and similar tools.

**Scan → Plan → Approve → Fix → You test → Repeat.** It never edits code without your approval.

## Install

```bash
git clone https://github.com/Q-Niranjan/ai-responsive-repair-skills.git /tmp/rrs

# Cursor
mkdir -p .cursor/skills && cp -r /tmp/rrs/skills/responsive-repair .cursor/skills/

# Claude Code
mkdir -p .claude/skills && cp -r /tmp/rrs/skills/responsive-repair .claude/skills/
```

For other tools, paste `SKILL.md` into your rules file (`AGENTS.md`, `.windsurfrules`, `.clinerules`) and keep `references/` next to it.

## Usage

**Full application**
```
/responsive-repair scan the whole application for responsive issues on mobile, tablet, and desktop.
```

**Specific page**
```
/responsive-repair app/dashboard/page.tsx scrolls sideways on mobile.
```

**Specific component**
```
/responsive-repair components/StatsCard.tsx labels get cut off below 400px.
```

Approve the plan (all or some batches), test at the suggested viewports, and report back.

## Customize

Edit the files in `skills/responsive-repair/references/`: `checklist.md`, `patterns.md`, `viewports.md`, `report-templates.md`, and `stack-detection.md`.

## License

MIT
