# AGENTS.md

## What this repo is

Source of truth for Wazoo agent skills. Each skill lives in
`skills/<category>/<name>/SKILL.md` and is installed with:

```bash
npx skills add wazootech/wazoo-skills --skill=<name>
```

## How to work here

- Every skill directory must contain a `SKILL.md` with `name` and
  `description` in YAML frontmatter.
- Keep skills self-contained and concrete: exact endpoints, exact commands,
  and realistic values.
- Verify every command against the live platform before including it.
- When you add or change a published skill, update its page on
  docs.wazoo.dev under "Agents, skills, and MCP".
- Keep files as LF. `.gitattributes` enforces this.
