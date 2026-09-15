# playwright-page-object-model-skill

A Claude Code [Agent Skill](https://docs.claude.com/en/docs/claude-code) for
writing and reviewing Playwright TypeScript E2E tests with the Page Object
Model pattern: page classes, semantic locators, and custom fixtures.

Derived from a real Playwright + TypeScript suite.

## Install

### As a plugin (recommended)

```bash
/plugin marketplace add pahans/playwright-page-object-model-skill
/plugin install playwright-page-object-model@pahans-plugins
```

### Manually

Clone the repo, then symlink (or copy) the nested skill directory into your
personal skills directory:

```bash
git clone https://github.com/pahans/playwright-page-object-model-skill.git
ln -s "$(pwd)/playwright-page-object-model-skill/skills/playwright-page-object-model" \
  ~/.claude/skills/playwright-page-object-model
```

Claude Code will pick it up automatically on the next session.

## Contents

- [skills/playwright-page-object-model/SKILL.md](skills/playwright-page-object-model/SKILL.md) —
  the skill: when to use it, the POM conventions, a quick-reference table,
  and common mistakes.
- [skills/playwright-page-object-model/example.md](skills/playwright-page-object-model/example.md) —
  a full worked example (page object, fixture, spec) for a course
  registration form.
