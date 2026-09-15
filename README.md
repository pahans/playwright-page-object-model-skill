# playwright-page-object-model-skill

An Agent Skill for writing and reviewing Playwright TypeScript E2E tests
with the Page Object Model pattern: page classes, semantic locators, and
custom fixtures. Works with [Claude Code](https://docs.claude.com/en/docs/claude-code),
[OpenAI Codex CLI](https://developers.openai.com/codex/concepts/customization),
and [Cursor](https://cursor.com/docs).

Derived from a real Playwright + TypeScript suite.

## Install

### Any agent (npx skills)

[`npx skills`](https://github.com/vercel-labs/skills) detects this repo's
`SKILL.md` layout automatically and installs it for whichever supported
agents (Claude Code, Cursor, Codex, OpenCode, and ~75 others) it finds on
your machine:

```bash
npx skills add pahans/playwright-page-object-model-skill
```

Add `-g` to install globally instead of per-project, or
`--agent claude-code cursor` to target specific agents. Use `--list` to
preview what would be installed without writing any files.

### Claude Code

**As a plugin (recommended):**

```bash
/plugin marketplace add pahans/playwright-page-object-model-skill
/plugin install playwright-page-object-model@pahans-plugins
```

**Manually:** clone the repo, then symlink (or copy) the nested skill
directory into your personal skills directory:

```bash
git clone https://github.com/pahans/playwright-page-object-model-skill.git
ln -s "$(pwd)/playwright-page-object-model-skill/skills/playwright-page-object-model" \
  ~/.claude/skills/playwright-page-object-model
```

Claude Code will pick it up automatically on the next session.

### Codex CLI

Codex reads the same `SKILL.md` format. Clone the repo, then symlink the
skill into your personal skills directory (`~/.agents/skills/`) or a
project's `.agents/skills/` — this repo already ships that symlink at
[.agents/skills/playwright-page-object-model](.agents/skills/playwright-page-object-model):

```bash
git clone https://github.com/pahans/playwright-page-object-model-skill.git
ln -s "$(pwd)/playwright-page-object-model-skill/skills/playwright-page-object-model" \
  ~/.agents/skills/playwright-page-object-model
```

### Cursor

Copy (or symlink) the rule file into your project's `.cursor/rules/`:

```bash
git clone https://github.com/pahans/playwright-page-object-model-skill.git
cp playwright-page-object-model-skill/.cursor/rules/playwright-page-object-model.mdc \
  <your-project>/.cursor/rules/
```

It auto-attaches on `*.spec.ts`, `pages/**/*.ts`, and `fixtures.ts` files
(see the `globs` in its frontmatter) — adjust those globs to match your
project's layout.

## Contents

- [skills/playwright-page-object-model/SKILL.md](skills/playwright-page-object-model/SKILL.md) —
  the skill: when to use it, the POM conventions, a quick-reference table,
  and common mistakes.
- [skills/playwright-page-object-model/example.md](skills/playwright-page-object-model/example.md) —
  a full worked example (page object, fixture, spec) for a course
  registration form.
