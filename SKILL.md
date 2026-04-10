---
name: notebooklm-pro
description: Query, manage, and automate Google NotebookLM from your AI agent. Add sources, generate branded slide decks, run research, export to video pipeline, and keep auth alive automatically.
version: 2.0
updated: 2026-04-10
---

# NotebookLM Skill Pro - by AI Injection

Connect your AI agent to Google NotebookLM. Add sources automatically, query your notebooks, generate branded slide decks, and keep your session alive without manual re-login.

Built on top of [notebooklm-py](https://github.com/teng-lin/notebooklm-py) by Teng Lin.

---

## Prerequisites

### 1. Install notebooklm-py

```bash
pip install "notebooklm-py[browser]"
```

The `[browser]` extra installs Playwright, needed for the one-time login. All queries run without a browser after that.

**If pip is unavailable or the package is outdated**, install from a specific GitHub release tag:

```bash
# Get the latest release tag
LATEST_TAG=$(curl -s https://api.github.com/repos/teng-lin/notebooklm-py/releases/latest | grep '"tag_name"' | cut -d'"' -f4)
pip install "git+https://github.com/teng-lin/notebooklm-py@${LATEST_TAG}"
```

> Do NOT install from `git+https://github.com/teng-lin/notebooklm-py` without a tag — the main branch may contain unstable code.

### 2. Install Playwright's Chromium browser (one-time)

```bash
playwright install chromium
```

---

## Auth Setup (One-Time)

```bash
python scripts/nlm.py login
```

This opens a browser window. Sign into your Google account, wait until you see the NotebookLM homepage, then press ENTER in the terminal. Your session is saved to `~/.notebooklm/storage_state.json`.

**After this, your agent can access NotebookLM without any manual login.**

### Verify it worked

```bash
python scripts/nlm.py list
```

You should see your notebooks listed.

---

## Core Commands

All commands run through `python scripts/nlm.py`.

### Notebooks

```bash
# List all notebooks
python scripts/nlm.py list

# Create a new notebook
python scripts/nlm.py create "My Research"

# Describe a notebook (AI summary of contents)
python scripts/nlm.py describe NOTEBOOK_ID
```

### Sources

```bash
# Add a URL (articles, YouTube videos, etc.)
python scripts/nlm.py add-source --notebook-id NOTEBOOK_ID --url "https://example.com/article"

# Add a local file (PDF, markdown, txt, docx)
python scripts/nlm.py add-source --notebook-id NOTEBOOK_ID --file "/path/to/doc.pdf"

# Add raw text
python scripts/nlm.py add-source --notebook-id NOTEBOOK_ID --title "My Notes" --text "Content here..."

# List sources in a notebook
python scripts/nlm.py sources --notebook-id NOTEBOOK_ID
```

### Querying

```bash
# Ask a question (set active notebook first, or pass --notebook-id)
python scripts/nlm.py ask "What are the main concepts?" --notebook-id NOTEBOOK_ID
```

### Local Library (save notebook metadata)

```bash
# Add to local library with a name and description
python scripts/nlm.py library-add --notebook-id NOTEBOOK_ID --name "Claude Docs" --description "All Claude documentation"

# Set as the active (default) notebook
python scripts/nlm.py library-activate claude-docs

# List library
python scripts/nlm.py library-list
```

### Audio & Reports

```bash
# Generate deep-dive audio (podcast style)
python scripts/nlm.py generate-audio --notebook-id NOTEBOOK_ID

# Generate a briefing doc
python scripts/nlm.py generate-report --notebook-id NOTEBOOK_ID

# Generate a study guide
python scripts/nlm.py generate-report --notebook-id NOTEBOOK_ID --format study_guide

# Generate a blog post
python scripts/nlm.py generate-report --notebook-id NOTEBOOK_ID --format blog_post
```

### Artifacts

```bash
# List all generated artifacts (audio, reports, etc.)
python scripts/nlm.py artifacts --notebook-id NOTEBOOK_ID
```

---

## Slide Generation

### Before generating slides — ask the user for their brand colors

**Agent instruction:** Before running any slide generation, always ask the user:

> "What are your brand colors? I need:
> - Primary color (main accent — e.g. `#FF6B2C`)
> - Secondary color (supporting — e.g. `#FFBE2E`)
> - Background color (e.g. `#000000` for black, `#1A1A2E` for dark navy)
> - Text color (e.g. `#F5F5F5` for off-white)
>
> If you don't have hex codes, describe them and I'll pick the closest match."

Once you have the colors, substitute them into the slide prompt template below.

---

### Slide Prompt Template (Blackboard Style)

Use this as a **Focus Prompt** inside NotebookLM (Studio → Focus). The agent fills in the placeholders before sending.

```
Create a [NUMBER]-slide presenter deck for "[VIDEO TITLE]."

Design: Dark blackboard on [BACKGROUND_COLOR]. TITLES: bold slab serif (Rockwell or Roboto Slab), stamped/typeset, color [PRIMARY_COLOR]. Body text: handwritten chalk-style, color [TEXT_COLOR]. Each slide features one key glossy 3D object that floats above the chalk background. Chalk dashed connector lines link 3D elements to surrounding sketch labels. Every slide: dashed border in [PRIMARY_COLOR] + [CORNER_ICON] icon top-right corner. [PRIMARY_COLOR] PRIMARY, [SECONDARY_COLOR] SECONDARY, [TEXT_COLOR] text, [BACKGROUND_COLOR] background.

Slide 1: [SLIDE_1_DESCRIPTION]
Slide 2: [SLIDE_2_DESCRIPTION]
...

CRITICAL: Every slide — slab serif TITLE in [PRIMARY_COLOR] at top, dashed [PRIMARY_COLOR] border, [CORNER_ICON] icon top-right. Chalk body text. [BACKGROUND_COLOR] background. One 3D object per slide. [PRIMARY_COLOR] PRIMARY.
```

**Placeholders to fill:**
| Placeholder | What to fill |
|-------------|-------------|
| `[NUMBER]` | How many slides |
| `[VIDEO TITLE]` | The topic/title of the content |
| `[PRIMARY_COLOR]` | User's primary brand color (hex) |
| `[SECONDARY_COLOR]` | User's secondary brand color (hex) |
| `[BACKGROUND_COLOR]` | Background color (hex) |
| `[TEXT_COLOR]` | Text color (hex) |
| `[CORNER_ICON]` | Icon that appears on every slide (e.g. lightning bolt, star, logo description) |
| `[SLIDE_N_DESCRIPTION]` | Per-slide: what the slide covers, what 3D object appears, what labels surround it |

### Per-slide description format

Each slide description should be ~150 characters. Be specific:

```
Slide 2: [TOPIC]. 3D [OBJECT] floating [POSITION]. Chalk labels: "[LABEL 1]," "[LABEL 2]," "[LABEL 3]." [PRIMARY_COLOR] dashed arrow pointing to [KEY ELEMENT].
```

**3D object suggestions by topic:**
- Data / knowledge → glowing books or filing cabinet
- Actions / automation → terminal / monitor
- Time / scheduling → clock or calendar
- Mobile / notifications → smartphone
- AI / thinking → brain model
- Cloud / remote → glowing cloud
- Steps / process → numbered podium

### How to submit the focus prompt

Once filled in, run:

```bash
python scripts/nlm.py generate-report --notebook-id NOTEBOOK_ID --format custom --prompt "YOUR FILLED PROMPT HERE"
```

Or paste it directly into NotebookLM → Studio → Focus prompt.

> **Note:** NotebookLM has a ~5,000 character limit on focus prompts. If your prompt is too long, shorten the per-slide descriptions.

---

## Keeping Auth Alive (Optional)

Google session cookies expire after 7–30 days. There are two approaches:

### Option A — Simple (re-login when it breaks)

When you get an auth error, just run:

```bash
python scripts/nlm.py login
```

Takes 30 seconds. Fine for occasional use.

### Option B — Headless Auto-Refresh (recommended for agent setups)

`scripts/refresh_auth.py` silently refreshes your cookies using the persistent browser profile — no browser window, no manual steps. Run it every few days before cookies expire.

```bash
python scripts/refresh_auth.py          # Refresh cookies
python scripts/refresh_auth.py --check  # Just check if they're valid
```

**To automate this**, add a scheduled task or cron job:

```
# Cron example — refresh every 3 days at 5:30am
30 5 */3 * * python /path/to/scripts/refresh_auth.py
```

**For Claude Code agent setups**, add this to your agent's `cron-registry.json`:

```json
{
  "id": "notebooklm-auth-refresh",
  "name": "NotebookLM cookie refresh (every 3 days)",
  "cron": "30 5 */3 * *",
  "prompt": "Run: python /path/to/scripts/refresh_auth.py — if it fails, alert the user to run notebooklm login manually.",
  "enabled": true
}
```

> **How it works:** Your first login saves a full browser profile to `~/.notebooklm/browser_profile/`. Google keeps this session alive much longer than raw cookies because the browser handles token rotation automatically. The refresh script uses this profile headlessly to export fresh cookies — no human interaction needed.

---

## Troubleshooting

**Auth error / "Authentication expired"**
Run `python scripts/refresh_auth.py` first. If that fails, run `python scripts/nlm.py login` to do a full re-login.

**`nlm` command not found in terminal**
Use `python scripts/nlm.py` instead. This is a Windows PATH issue with certain Python installations (common with Windows Store Python). The scripts work fine when called with `python` directly.

**"No notebook specified"**
Either pass `--notebook-id YOUR_ID` or set an active notebook first:
```bash
python scripts/nlm.py library-activate your-notebook-name
```

**Slide generation times out**
NotebookLM can take 3–6 minutes to generate custom reports. If the command times out, check your artifacts list — the generation likely completed on NotebookLM's side:
```bash
python scripts/nlm.py artifacts --notebook-id NOTEBOOK_ID
```

**Playwright / Chromium not found**
Run `playwright install chromium` to install the browser.

**Windows: "UnicodeEncodeError" on output**
Set `PYTHONUTF8=1` in your environment, or run `$env:PYTHONUTF8="1"` in PowerShell before running the script.

---

---

## Additional Slide Prompt Templates (NEW in v2)

### Clean Corporate (Light)

```
Create a [NUMBER]-slide professional deck for "[TITLE]."

Design: Clean white background [BACKGROUND_COLOR]. Titles: bold sans-serif (Inter 800), color [PRIMARY_COLOR]. Body: Inter 300, color #333. Accent line under each title in [PRIMARY_COLOR]. One key data visualization or icon per slide. Minimal layout with generous whitespace. Bottom-right: small page number in [PRIMARY_COLOR].

Slide 1: [DESCRIPTION]
...
```

### Neon Tech (Dark)

```
Create a [NUMBER]-slide tech presentation for "[TITLE]."

Design: Deep dark background [BACKGROUND_COLOR]. Titles: Space Grotesk 700, color [PRIMARY_COLOR] with subtle glow effect. Body: Space Grotesk 300, color #ccc. Each slide has a glowing neon accent line in [PRIMARY_COLOR]. Tech-inspired grid pattern in background at 5% opacity. Code snippets in monospace with [SECONDARY_COLOR] syntax highlighting.

Slide 1: [DESCRIPTION]
...
```

### Gradient Modern

```
Create a [NUMBER]-slide modern deck for "[TITLE]."

Design: Dark gradient background from [BACKGROUND_COLOR] to #1a1a2e. Titles: Outfit 800, gradient text from [PRIMARY_COLOR] to [SECONDARY_COLOR]. Body: Outfit 300, color #aaa. Glassmorphism cards with blur backdrop and thin border. Rounded corners everywhere. Soft shadow depth.

Slide 1: [DESCRIPTION]
...
```

### AI Injection Default

```
Create a [NUMBER]-slide deck for "[TITLE]."

Design: Black background #0a0a0a. Titles: bold, color #ff6600 (AI Injection orange). Body: light gray #aaa. Accent: #c8ff00 (lime green) for highlights and data. Cards: #111 background with #222 border. One icon or visual per slide. Clean, minimal, dark tech aesthetic.

Slide 1: [DESCRIPTION]
...
```

---

## Content Pipeline Integration (NEW in v2)

### NotebookLM to Video Pipeline

Use NotebookLM as a research engine, then feed output into your video pipeline:

1. **Create a notebook** for your video topic
2. **Add sources** (YouTube URLs, articles, PDFs)
3. **Query** to extract key points: `ask "Summarize the 5 most important points"`
4. **Generate a report** as the base for your video script
5. **Feed the report into your video pipeline** (Remotion, edge-tts, etc.)

```bash
# Step 1-3: Research
python scripts/nlm.py create "AI Agents Video"
python scripts/nlm.py add-source --notebook-id ID --url "https://youtube.com/watch?v=xxx"
python scripts/nlm.py ask "What are the 5 key takeaways?" --notebook-id ID

# Step 4: Generate script base
python scripts/nlm.py generate-report --notebook-id ID --format blog_post

# Step 5: Use the output as voiceover script input
```

### NotebookLM to Slide Deck to Video

1. Generate slides in NotebookLM with your brand prompt
2. Download the slides as images
3. Use in Remotion as scene backgrounds with voiceover

### Batch Research Workflow

For content series, create one notebook per episode:

```bash
# Create notebooks for a 5-part series
for i in 1 2 3 4 5; do
  python scripts/nlm.py create "Series Part $i"
done

# Add the same base sources to all
for ID in id1 id2 id3 id4 id5; do
  python scripts/nlm.py add-source --notebook-id $ID --url "https://base-source.com"
done
```

---

## Report Format Reference (NEW in v2)

All available report formats for `generate-report`:

| Format | Output | Best for |
|--------|--------|----------|
| `study_guide` | Structured Q&A with key concepts | Learning content, courses |
| `blog_post` | Long-form article | Video scripts, written content |
| `briefing` | Executive summary | Quick overviews, meeting prep |
| `faq` | Question and answer pairs | Help docs, community content |
| `timeline` | Chronological events | History content, project updates |
| `custom` | Your own prompt | Branded slides, specific formats |

### Custom Format Tips

- Keep custom prompts under 5,000 characters
- Be specific about visual style (colors, fonts, layout)
- Include per-slide descriptions for best results
- Use the `--format custom --prompt "..."` flag

---

## Agent Integration Patterns (NEW in v2)

### Cron: Daily Research Digest

Add to your agent's cron registry to get daily research summaries:

```json
{
  "id": "daily-research",
  "name": "Daily research digest",
  "cron": "0 9 * * *",
  "prompt": "Query my active NotebookLM notebook for any new insights. Summarize the top 3 findings and send to my messaging channel.",
  "enabled": true
}
```

### Cron: Weekly Content Ideas

```json
{
  "id": "weekly-content",
  "name": "Weekly content ideas from research",
  "cron": "0 10 * * 1",
  "prompt": "Based on my NotebookLM sources, suggest 5 video ideas for this week. Include a one-line hook and target audience for each.",
  "enabled": true
}
```

### Multi-Agent Pattern

- **Research Agent**: Manages NotebookLM notebooks, adds sources, runs queries
- **Content Agent**: Takes research output, writes scripts, generates slides
- **Production Agent**: Takes scripts and slides, renders videos with Remotion

Each agent has its own workspace but shares NotebookLM auth via `~/.notebooklm/`.

---

## Credits

- **notebooklm-py** by [Teng Lin](https://github.com/teng-lin/notebooklm-py) -- the library that makes this possible
- **Original skill** by [RoboNuggets](https://robonuggets.com) -- CLI wrapper, headless refresh, slide generation
- **Pro features** by [AI Injection](https://github.com/ainjection) -- additional templates, pipeline integration, agent patterns
