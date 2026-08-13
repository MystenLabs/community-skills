# Sui Community Skills

Community-contributed agent skills for building on Sui. Install them into Claude Code, Cursor, Codex, and [40+ other AI coding agents](https://skills.sh) via the `skills` CLI.

> These skills are maintained by the community. For official Mysten Labs skills, see [MystenLabs/skills](https://github.com/MystenLabs/skills).

## Install

```bash
# Browse available community skills
npx skills add mystenlabs/community-skills --list

# Install a specific skill
npx skills add mystenlabs/community-skills --skill your-skill-name

# Install all community skills
npx skills add mystenlabs/community-skills --all
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for full guidelines.

### Quick start

```bash
# Copy the template
cp -r template/ your-skill-name/

# Edit the skill definition
$EDITOR your-skill-name/SKILL.md

# Add supporting reference files
touch your-skill-name/setup.md
touch your-skill-name/core.md

# Open a PR
```

## Promotion to official skills

Community skills that demonstrate consistent quality and coverage may be promoted to the official [MystenLabs/skills](https://github.com/MystenLabs/skills) repo. See [CONTRIBUTING.md](CONTRIBUTING.md#promotion-to-official-skills) for criteria.

## Resources

- [Official Sui skills](https://github.com/MystenLabs/skills)
- [skills.sh listing](https://skills.sh)
- [Agent Skills spec](https://github.com/vercel-labs/skills)
- [Sui Developer Docs](https://docs.sui.io)
