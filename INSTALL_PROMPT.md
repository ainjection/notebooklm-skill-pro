# Install Prompt

Copy and paste the text below into your Claude agent to install the NotebookLM skill.

---

```
Install the RoboNuggets NotebookLM skill from GitHub.

Steps:
1. Clone https://github.com/robonuggets/notebooklm-skill into my skills directory at ~/.claude/skills/notebooklm/ (create the directory if it doesn't exist)
2. Confirm the following files are present:
   - SKILL.md
   - scripts/nlm.py
   - scripts/refresh_auth.py
3. Check if notebooklm-py is installed by running: python -c "import notebooklm; print('ok')"
   - If not installed, run: pip install "notebooklm-py[browser]"
   - Then run: playwright install chromium
4. Check if auth already exists at ~/.notebooklm/storage_state.json
   - If not, run: python ~/.claude/skills/notebooklm/scripts/nlm.py login
     (This opens a browser — sign into Google, wait for NotebookLM to load, press ENTER)
5. Verify everything works by running: python ~/.claude/skills/notebooklm/scripts/nlm.py list
   - If successful, list my notebooks and confirm the skill is ready to use
   - If it fails, tell me the error so we can fix it

Once complete, confirm: "NotebookLM skill installed. You can now ask me to query notebooks, add sources, and generate slides."
```

---

## Notes for the user

- You need Python installed and accessible from your terminal
- You need a Google account with access to [notebooklm.google.com](https://notebooklm.google.com)
- The login step opens a real browser window — this is normal and only happens once
- After install, your agent reads `SKILL.md` automatically if you've added the skills directory with `--add-dir`
