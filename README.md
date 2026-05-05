# 🧠 custom-claude-skills

A growing collection of Claude skills that unlock expert-level performance across
engineering, debugging, AI tools, content creation, and more!

**Globally applicable. Any stack. Any language. Continuously expanding.**

---

## What Are Skills?

Claude Skills are instruction files that Claude reads before tackling specific tasks.
Instead of generic AI responses, Claude behaves like a domain expert — following battle-tested
patterns, using the right tools, and producing production-grade output.

Drop any `SKILL.md` into a Claude Project's knowledge base and it activates automatically
for every relevant conversation in that project.

---

## Current Skills

| Skill | What It Does | Triggers On |
|---|---|---|
| [`saas-builder`](./saas-builder/) | Full-stack SaaS engineering — any language, stack, or market | Product features, startup architecture, API design |
| [`debug-fast`](./debug-fast/) | Root-cause debugging in 12+ languages with deep-diagnosis tools | Any bug, error, crash, or "this isn't working" |
| [`prompt-engineer`](./prompt-engineer/) | Optimal prompts for every major AI — image, video, audio, LLMs, code | "Write a prompt for...", "How do I get better results from X" |
| [`content-pipeline`](./content-pipeline/) | Scripts for 7 content formats across any topic | "Write a script for...", "Plan a video about..." |
| [`memory-vault`](./memory-vault/) | Retrieve anything from past conversations — code, decisions, designs | "Find the code you wrote for...", "What did we decide about..." |

> More skills being added. Star this repo to get notified.

---

## Roadmap — Coming Soon

Skills being planned or in progress:

| Skill | Description |
|---|---|
| `code-reviewer` | Deep code review across any language — security, performance, style |
| `data-analyst` | SQL, pandas, data visualization, and insight generation |
| `system-design` | Architecture diagrams, scalability patterns, tradeoff analysis |
| `marketing-copy` | Landing pages, ads, email sequences, product descriptions |
| `research-agent` | Structured web research, source evaluation, report generation |

Have an idea for a skill? Open an issue or submit a PR.

---

## Repo Structure

```
Claude-Skills/
├── README.md
├── saas-builder/
│   ├── SKILL.md
│   └── references/
│       ├── payment-providers.md
│       ├── auth-patterns.md
│       ├── db-patterns.md
│       └── api-patterns.md
├── debug-fast/
│   └── SKILL.md
├── prompt-engineer/
│   └── SKILL.md
├── content-pipeline/
│   └── SKILL.md
└── memory-vault/
    └── SKILL.md
```

Each skill lives in its own folder. Large reference material goes in a `references/`
subfolder to keep the main `SKILL.md` focused and readable.

---

## Installation

### Claude.ai Projects (recommended)
1. Create or open a Claude Project
2. Go to **Project Knowledge**
3. Upload the `SKILL.md` file(s) you want active

### All skills at once
```bash
cat */SKILL.md > combined-skills.md
# Upload combined-skills.md as project knowledge
```

### API / Claude Code
Paste the contents of any `SKILL.md` into your system prompt or `CLAUDE.md`.

---

## Contributing

Skills are plain Markdown — easy to write, easy to improve.

**To add a new skill:**
1. Create a new folder: `your-skill-name/`
2. Add `SKILL.md` with YAML frontmatter:
```yaml
---
name: your-skill-name
description: >
  One paragraph describing what this skill does and exactly when Claude
  should trigger it. Be specific and aggressive with trigger phrases.
---
```
3. Add to the skills table in this README
4. For large reference content, put it in `your-skill-name/references/`
5. Keep `SKILL.md` under 500 lines

**To improve an existing skill:**
- Open a PR with the change and a one-line explanation of what was wrong
- Audit findings welcome — severity labels: `critical`, `high`, `medium`, `low`

---

## License
MIT — use freely, fork, adapt, share.
