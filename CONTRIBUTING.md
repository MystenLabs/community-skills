# Contributing

Community skills follow the same format as [official Mysten Labs skills](https://github.com/MystenLabs/skills). This ensures compatibility with the `skills` CLI and a clean promotion path to the official repo.

## Skill structure

Each skill is a directory at the repo root containing:

```
your-skill-name/
├── SKILL.md              # Required — frontmatter + routing + rules
├── reference-file-1.md   # Optional — loaded on demand by the agent
└── reference-file-2.md   # Optional
```

Use the `template/` directory as a starting point:

```bash
cp -r template/ your-skill-name/
```

## SKILL.md format

Your `SKILL.md` must include YAML frontmatter with `name` and `description`:

```yaml
---
name: your-skill-name
description: >
  Trigger-style description of when this skill should activate.
  Be specific — this is what the agent uses to decide whether to load the skill.
---
```

Below the frontmatter, follow this structure:

1. **Opening paragraph** — what the skill covers and what mistakes it prevents
2. **Source constraint** — canonical documentation URLs the skill is derived from
3. **Reference files** — for each `.md` file: path, "Load when" trigger, "Covers" summary
4. **Routing guide table** — maps tasks to which reference files to load
5. **Skill content** — key concepts, rules, and common mistakes (always loaded)

See the [template](template/SKILL.md) and [official skills](https://github.com/MystenLabs/skills) for examples.

## PR checklist

- [ ] Skill directory is at the repo root with a lowercase, hyphenated name
- [ ] `SKILL.md` has `name` and `description` frontmatter
- [ ] `description` is written as a trigger rule ("Use when...")
- [ ] Opening paragraph describes what the skill covers
- [ ] Reference files exist for each section listed under "Reference files"
- [ ] Routing guide table matches your actual reference files
- [ ] Skill does not duplicate an [official skill](https://github.com/MystenLabs/skills)

## What makes a good community skill

- **Focused scope.** One skill per capability. If two things have different triggers, they should be separate skills.
- **Sui-specific.** Assume the user is building on Sui. Call out where Sui differs from other chains.
- **Grounded in sources.** All facts must be verifiable against linked documentation.
- **Practical.** Cover what developers actually need, not theoretical completeness.

## Promotion to official skills

Community skills may be promoted to [MystenLabs/skills](https://github.com/MystenLabs/skills) when they meet these criteria:

- Covers a domain not already handled by an official skill
- Follows all conventions from the official repo
- Positive community usage and feedback
- Reviewed and approved by the Mysten Labs skills team

To request promotion, open an issue in [MystenLabs/skills](https://github.com/MystenLabs/skills/issues) linking to your community skill.
