# Wazoo Skills

Agent skills for the Wazoo platform, built for coding agents that speak Agent
Skills (Claude Code, Cursor, OpenCode, Gemini).

## Install

```bash
npx skills add wazootech/wazoo-skills --skill=wazoo
```

Update an installed skill:

```bash
npx skills update wazoo
```

## Skills

| Skill | Category | Description |
| :---- | :------- | :---------- |
| [wazoo](skills/engineering/wazoo/SKILL.md) | engineering | Manage Worlds, run SPARQL, search context, manage tokens |

## Layout

```text
skills/
  <category>/
    <name>/
      SKILL.md
```

Each skill is a directory with a `SKILL.md` at its root. The `name` and
`description` frontmatter fields drive agent discovery.

## Contributing a skill

- Add `skills/<category>/<name>/SKILL.md` with `name` and `description` in
  YAML frontmatter.
- Keep the SKILL.md self-contained: exact endpoints, exact commands, and
  realistic values.
- Verify every command against the live platform before committing.
- Document each published skill on docs.wazoo.dev under "Agents, skills, and
  MCP".

## Reference

- [Wazoo platform docs](https://docs.wazoo.dev)
- [Wazoo on GitHub](https://github.com/wazootech)
