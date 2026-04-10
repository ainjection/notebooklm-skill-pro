# NotebookLM Skill Pro

Connect your Claude agent to Google NotebookLM. Add sources, query notebooks, generate branded slide decks, feed research into your video pipeline, and keep auth alive automatically.

Fork of [notebooklm-skill](https://github.com/robonuggets/notebooklm-skill) by RoboNuggets, enhanced by AI Injection. Powered by [notebooklm-py](https://github.com/teng-lin/notebooklm-py) by Teng Lin.

## Improvements Over Original

| Feature | Original | Pro v2 |
|---------|----------|--------|
| Slide templates | 1 (blackboard) | 4 (blackboard, corporate, neon tech, gradient modern) + AI Injection theme |
| Pipeline integration | None | NotebookLM to video pipeline workflow |
| Report formats | Basic | Full format reference with usage tips |
| Agent patterns | None | Cron templates for daily research, weekly content ideas |
| Multi-agent | None | Research/Content/Production agent pattern |
| Batch workflows | None | Series research workflow for content batches |

---

## What This Does

- **Query notebooks** — ask your NotebookLM questions from inside Claude
- **Add sources automatically** — URLs, PDFs, files, raw text — no manual copy-paste
- **Generate slide decks** — branded to your colors, from within Claude
- **Auto-refresh auth** — optional headless cookie refresh so you never have to re-login

---

## Requirements

- Python 3.9+
- A Google account with access to [NotebookLM](https://notebooklm.google.com)
- Claude (claude.ai/code) or any Claude agent setup

---

## Quick Start

**Step 1 — Install notebooklm-py**

```bash
pip install "notebooklm-py[browser]"
playwright install chromium
```

**Step 2 — Install this skill**

Paste the contents of `INSTALL_PROMPT.md` into your Claude agent. It will set everything up for you.

**Step 3 — Login once**

```bash
python scripts/nlm.py login
```

A browser opens. Sign into Google, wait for the NotebookLM homepage, press ENTER. Done.

**Step 4 — Test it**

```bash
python scripts/nlm.py list
```

Your notebooks should appear.

---

## What's Included

```
notebooklm-skill/
├── SKILL.md              — The skill file your agent reads
├── scripts/
│   ├── nlm.py            — CLI wrapper for notebooklm-py
│   └── refresh_auth.py   — Headless cookie refresh (optional)
├── INSTALL_PROMPT.md     — Copy-paste prompt to install via Claude
└── README.md             — This file
```

**Not included:** notebooklm-py itself. That's Teng Lin's library — install it via pip (see above). This skill is just the wrapper and workflow on top of it.

---

## Slide Generation

The skill includes a slide prompt template for generating branded presenter decks inside NotebookLM. When you ask Claude to generate slides, it will ask for your brand colors first — so the output matches your style, not ours.

See `SKILL.md` for the full template and instructions.

---

## Keeping Auth Alive

Google session cookies expire every 7–30 days. Two options:

- **Simple:** Re-run `python scripts/nlm.py login` when it breaks (~30 seconds)
- **Automatic:** Use `scripts/refresh_auth.py` on a schedule — headless, no browser window, no manual steps

See the **Keeping Auth Alive** section in `SKILL.md` for setup instructions.

---

## Community

This skill was shared from the [RoboNuggets](https://robonuggets.com) community — a group building and selling AI agents.

If you build something cool with this, share it in the community.

---

## Credits

- [notebooklm-py](https://github.com/teng-lin/notebooklm-py) by Teng Lin — the library that makes this possible
- Skill, CLI wrapper, and headless refresh by RoboNuggets
