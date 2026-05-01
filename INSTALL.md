# Installation and publishing guide

This scaffold ships with **only the new `reprompt` skill**. The other 14 skills currently live in your runtime Cowork plugin folder. To make this a true fork that includes them, copy them across before pushing.

## Step 1 - Pull in the existing 14 skills

Your existing pollen-dev-tools skills live at:

```
~/Library/Application Support/Claude/local-agent-mode-sessions/c8f59593-a833-4a2c-9995-1321a03f2fb5/96a97fbd-f616-4924-9a2c-bc71a5614499/rpm/plugin_014YNhd6abznzRbCYZUNnUxu/skills/
```

Open that path in Finder (Cmd+Shift+G in Finder, paste the path), then either:

**Option A - Finder copy.** Select all 14 skill folders (everything except `reprompt`), drag into `~/Downloads/pollen-dev-tools/skills/`.

**Option B - Terminal copy (faster).**

```bash
SRC="$HOME/Library/Application Support/Claude/local-agent-mode-sessions/c8f59593-a833-4a2c-9995-1321a03f2fb5/96a97fbd-f616-4924-9a2c-bc71a5614499/rpm/plugin_014YNhd6abznzRbCYZUNnUxu/skills"
DST="$HOME/Downloads/pollen-dev-tools/skills"

for skill in connect brainstorming executing-plans subagent-driven-development \
             senior-prompt-engineer requesting-code-review backend-patterns \
             continuous-learning braintrust-analyze writing-plans \
             using-superpowers connect-apps create-plans frontend-patterns; do
  cp -R "$SRC/$skill" "$DST/$skill"
done
```

The runtime path is the one Cowork manages. The session ID (`c8f59593-...`) and plugin ID (`plugin_014YN...`) are stable for your install but may differ on another machine. If the path doesn't exist, look in `~/Library/Application Support/Claude/local-agent-mode-sessions/` and find the most recent session folder.

## Step 2 - Push to GitHub

From inside `~/Downloads/pollen-dev-tools/`:

```bash
cd ~/Downloads/pollen-dev-tools
git add .
git commit -m "Initial fork: add reprompt skill, port 14 dev-tools skills"

# Create the repo on GitHub first (gh CLI shown; web UI also fine)
gh repo create pollen-dev-tools --public --source=. --remote=origin --push
```

If you don't have `gh` installed:

```bash
# Create a new empty repo at https://github.com/new (name: pollen-dev-tools)
git remote add origin git@github.com:evanstroeve/pollen-dev-tools.git
git branch -M main
git push -u origin main
```

Substitute `evanstroeve` with your actual GitHub username.

## Step 3 - Point Cowork at your fork

In Cowork:

1. Customize, Personal plugins
2. Find the existing `pollen-dev-tools` entry, remove it (so the new fork doesn't conflict)
3. **+**, Add marketplace
4. Paste: `evanstroeve/pollen-dev-tools` (substitute your GitHub username)
5. Sync
6. Install `pollen-dev-tools` from your fork
7. Restart Cowork

The `reprompt` skill will be available as `pollen-dev-tools:reprompt`.

## Step 4 - Verify

Test the skill explicitly:

```
/reprompt
```

or

```
ctoc this: write a Friday afternoon shift handover doc
```

Claude should restructure the request into the CTOC+ block before doing any work. If it auto-fires on regular requests (like "summarise this email"), the trigger weakening did not take. Check the description in `skills/reprompt/SKILL.md`.

## Future updates

When you tweak a skill:

```bash
cd ~/Downloads/pollen-dev-tools
# bump version in .claude-plugin/plugin.json
# add entry to CHANGELOG.md
git add . && git commit -m "..."
git push
```

Then in Cowork: Customize, your marketplace, Sync. Restart Cowork.

## Adding more skills later

Each new skill is a folder under `skills/` with a single `SKILL.md` containing frontmatter + body. No registry file to maintain; Cowork picks them up from the folder structure.

```
skills/
  my-new-skill/
    SKILL.md
```
