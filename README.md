# Vooks agent skills

Skills that let AI agents search the Vooks library of animated read-along storybooks for kids and answer what a Vooks subscription costs.

```sh
npx skills add vooks/skills
```

| Skill | What it does |
|---|---|
| [vooks-catalog](skills/vooks-catalog/SKILL.md) | Search and recommend Vooks titles for children about ages 2 to 8 |
| [vooks-pricing](skills/vooks-pricing/SKILL.md) | Plans, teacher rate, free trial, gifting and school purchasing |

Both skills use public data only. The same files are served at https://www.vooks.com/.well-known/agent-skills/, and the developer docs are at https://www.vooks.com/developers.
