# Jordan's Dev Playbook
> A founder's toolkit for building with AI. Terminal · GitHub · Claude Code · Xcode · Cowork · Dispatch · Creative Stack

A living reference document. Not a tutorial — a system. Update it as you build. Applicable to any idea.

---

## Table of Contents
1. [Terminal — the foundation](#1-terminal--the-foundation)
2. [GitHub — version control](#2-github--version-control)
3. [Claude Code — AI in the terminal](#3-claude-code--ai-in-the-terminal)
4. [Claude Desktop Code Tab — GUI for Claude Code](#4-claude-desktop-code-tab--gui-for-claude-code)
5. [Xcode — Apple's dev environment](#5-xcode--apples-dev-environment)
6. [Cowork — desktop automation](#6-cowork--desktop-automation)
7. [Dispatch — scheduled automations](#7-dispatch--scheduled-automations)
8. [Creative Toolstack — AI image and asset production](#8-creative-toolstack--ai-image-and-asset-production)
9. [Cloning and Running a Project](#9-cloning-and-running-a-project)
10. [New Machine Setup Checklist](#10-new-machine-setup-checklist)
11. [Best Practices & Learnings](#11-best-practices--learnings)

---

## 1. Terminal — the foundation

Everything in this stack runs through Terminal. It's how you talk to your Mac directly — installing tools, navigating files, running Claude Code, pushing to GitHub. All of it.

**Open Terminal:** `Cmd + Space` → type "Terminal" → Enter  
**New tab:** `Cmd + T`

### Navigation

```bash
pwd                  # print working directory — "where am I right now?"
ls                   # list files in current folder
ls -la               # list ALL files including hidden ones (dotfiles like .env)
cd ~/Desktop         # change directory. ~ always = your home folder (/Users/yourname)
cd ..                # go up one level
cd ../..             # go up two levels
mkdir projects       # make a new folder called "projects"
```

### File operations

```bash
touch notes.txt      # create a blank file
cat notes.txt        # print file contents to screen
cp file.txt copy.txt # copy a file
mv old.txt new.txt   # rename or move a file
rm file.txt          # delete a file — no undo, no trash
rm -rf foldername    # delete a folder and everything in it — be careful
```

### Copying files with spaces in the path

```bash
cp ~/Claude\ Projects/Jordan\'s\ Dev\ Playbook/file.md ~/projects/jordan-notes/
# spaces must be escaped with backslashes, or wrap the whole path in quotes
```

### Power habits

| Shortcut | What it does |
|----------|-------------|
| `Tab` | Autocomplete file/folder names — fastest habit to build |
| `↑ arrow` | Cycle through command history |
| `Ctrl + C` | Kill any running process — your escape hatch |
| `Ctrl + L` | Clear the screen (keeps session, cleans view) |
| `Ctrl + A` | Jump to start of line |
| `Ctrl + E` | Jump to end of line |

### First thing on a new Mac

```bash
xcode-select --install
```

Installs Apple's Command Line Tools — required for Git, Homebrew, Node, and Claude Code. Do this before anything else.

---

## 2. GitHub — version control

**Git** = version control system running locally on your machine.  
**GitHub** = the cloud where repos live and teams collaborate.

### First-time setup

```bash
git --version                                    # confirm git is installed (need 2.x+)
git config --global user.name "Your Name"        # set identity — do once
git config --global user.email "you@email.com"   # must match GitHub account email
git config --list                                # confirm settings took
```

### SSH key setup (authenticate without passwords)

SSH keys are how your machine proves its identity to GitHub without typing a password every time.

```bash
ls ~/.ssh                                  # check for existing keys
ssh-keygen -t ed25519 -C "you@email.com"  # generate new key if none exists
                                           # hit Enter through all prompts
cat ~/.ssh/id_ed25519.pub                  # copy the entire output
```

GitHub → Settings → SSH and GPG Keys → New SSH Key → paste the full line including `ssh-ed25519` prefix and email at the end.

```bash
ssh -T git@github.com   # test — should return "Hi [username]! You've successfully authenticated."
```

### Daily git workflow

```bash
git status           # what files have changed? Run this constantly
git pull             # pull latest before you start working — avoid conflicts
git add .            # stage all changed files
git add file.txt     # or stage a specific file
git commit -m "short description of what changed"
git push             # push commit to GitHub
git log --oneline    # see recent commit history
git diff             # see exactly what changed in unstaged files
```

**The loop:**
```
edit files → git add . → git commit -m "message" → git push
```

### Branching — for shared repos with CI/CD pipelines

When working on a shared repo with automated deployments, branching rules are non-negotiable. Confirm the rules with your collaborator before touching any files.

**Common pattern:**
- `main` branch = production (live site) — never push directly
- Any other branch = staging (preview environment) — auto-deploys on push
- Pushing a branch triggers a staging build for review
- Merging to main triggers a production deploy

**Workflow:**

```bash
git pull                              # always pull latest first
git checkout -b your-branch-name      # create and switch to a new branch
# make changes
git add .
git commit -m "what you changed"
git push origin your-branch-name      # push branch — triggers staging
```

> ⚠️ **Always use `git push origin your-branch-name` on shared repos — never plain `git push` on main.**

### jordan-notes repo

Your personal reference repo. Lives at `github.com/kidneyman10/jordan-notes`. Clone at `~/projects/jordan-notes/`.

**Push playbook updates:**
```bash
cp ~/Claude\ Projects/Jordan\'s\ Dev\ Playbook/jordan-dev-playbook.vX.md ~/projects/jordan-notes/
cd ~/projects/jordan-notes
git add . && git commit -m "update playbook" && git push
```

---

## 3. Claude Code — AI in the terminal

Claude Code is an agentic CLI — run it inside a project directory and it reads your codebase, writes code, runs commands, and iterates. The most direct way to use Claude for development work.

### Install

```bash
node --version                              # need v18+ — install via brew install node if missing
npm install -g @anthropic-ai/claude-code    # install Claude Code globally
claude --version                            # verify install
```

### Basic usage

```bash
cd ~/projects/your-project                  # ALWAYS navigate into project directory first
claude                                      # launch interactive session
claude "explain what this codebase does"    # pass a task inline
claude --help                               # see all flags and options
```

### Key patterns

**Give it a goal, not a task:**
```
"Add a product card component that shows image, name, price, and add-to-cart button"
```
Not: "Create a file called ProductCard.jsx"

**CLAUDE.md — standing instructions for any project:**
Create a `CLAUDE.md` file in the project root. Claude Code reads it every session. Include:
- What the project is and what it does
- Tech stack and key dependencies
- Coding conventions and file structure
- What not to touch

```bash
touch CLAUDE.md   # create it in project root
```

**Always review before committing:**
```bash
git diff          # see exactly what Claude changed before staging
```

> 💡 Claude Code bills against Anthropic API credits — separate from your Claude.ai subscription.

---

## 4. Claude Desktop Code Tab — GUI for Claude Code

The Code tab in the Claude Desktop app runs the same Claude Code engine with a graphical interface. Not a replacement for Terminal — a complement to it.

### What it adds over Terminal

| Feature | Value |
|---------|-------|
| Visual diff review | See exactly what Claude changed before accepting |
| Built-in browser preview | Claude runs your dev server and sees rendered output to iterate |
| Session sidebar | Manage multiple sessions, track context usage |
| File attachments | Drag files directly into the prompt |
| Parallel sessions | Multiple Claude instances on different branches simultaneously |
| Dispatch integration | Assign tasks from your phone, return to completed work |

### When to use Desktop vs Terminal

- **Desktop Code tab** — vibe coding, visual iteration, reviewing diffs, browser preview
- **Terminal** — quick commands, scripting, automation, raw CLI control
- Both can run simultaneously on the same project — they share `CLAUDE.md` and configuration

### Starting a session

Open Claude Desktop → Code tab → select your project folder. Uncheck **worktree** when starting out — manage branches manually until you're comfortable.

### To move a Terminal session into Desktop:
```bash
/desktop    # saves session and opens it in the Desktop app
```

---

## 5. Xcode — Apple's dev environment

Two distinct pieces:
- **Command Line Tools** — lightweight, Terminal-based, required for everything
- **Full Xcode app** — GUI IDE, required for iOS development (~13GB)

### Setup

```bash
xcode-select --install         # install Command Line Tools first
xcode-select -p                # verify — should return /Library/Developer/CommandLineTools
```

Install full Xcode from the **Mac App Store** when ready for iOS work. Start with SwiftUI, not UIKit.

### Homebrew — install alongside

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

After install, add to PATH (Apple Silicon Macs — required or brew won't be found):
```bash
echo >> /Users/jordan/.zprofile
echo 'eval "$(/opt/homebrew/bin/brew shellenv zsh)"' >> /Users/jordan/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv zsh)"
```

Install tools:
```bash
brew install node
brew install git
```

---

## 6. Cowork — desktop automation

Cowork is Anthropic's desktop automation tool — Claude with eyes on your screen and hands on your apps. Use it for repetitive multi-step desktop workflows you'd otherwise do manually.

**Install:** Claude Desktop app → Cowork tab.

### What it handles

- Batch rename and organize files
- Cross-app workflows — pull from one app, process, drop into another
- File prep pipelines — resize, rename, sort before uploading to external services
- Research aggregation across sources

### Playbook sync job (build this)

Name: "Sync Playbook to GitHub" — manual trigger:
```bash
cp ~/Claude\ Projects/Jordan\'s\ Dev\ Playbook/jordan-dev-playbook.vX.md ~/projects/jordan-notes/
cd ~/projects/jordan-notes
git add .
git commit -m "update playbook"
git push
```

> Start with small, specific tasks. Automate what's already working, not what's still being figured out.

---

## 7. Dispatch — scheduled automations

Dispatch lets you schedule and trigger Claude-powered automations. Cron jobs with AI. Available in Claude Desktop.

**Trigger types:**
- Time-based: "every day at 8am"
- Event-based: "when a file lands in this folder"
- Webhook: triggered from an external app or API
- Mobile: assign tasks from your phone, return to completed work

**Use cases:**
- Morning briefings and summaries
- Weekly data pulls and reports
- Recurring content pipeline triggers
- PR monitoring and CI failure detection

**Prereq:** Get comfortable with Cowork first. Dispatch is the "set it and forget it" layer — you need to know what workflows are worth automating before scheduling them.

---

## 8. Creative Toolstack — AI image and asset production

For building visual products, merchandise, marketing assets, and content.

### The stack

| Tool | Role | Cost |
|------|------|------|
| Midjourney | Art generation — highest quality output | ~$10/month |
| Ideogram | Text-in-image — accurate typography in designs | ~$15/month |
| Canva | Layout, text overlay, export | Free / Pro |
| Magnific AI | Upscaling to print resolution | ~$39/month — skip if using Ideogram 4K/8K |

**Lean stack: $25/month (MJ + Ideogram). Magnific optional.**

### Midjourney vs Ideogram — when to use each

- **Midjourney** — art quality, style, composition. Cannot reliably render accurate text.
- **Ideogram** — precise text rendering in images. Use MJ output as image reference for style matching.
- **Together:** MJ for the art → Ideogram with MJ as reference + text prompt = best of both

### Asset production workflow

```
Concept → Midjourney (art only, no text) → Ideogram (upload MJ as reference, add text prompt)
→ Download at 4K or 8K → Ready for use
```

### Print-on-demand workflow (e.g. Printful)

```
Design in Printful → configure product → publish → import to store admin → live
```

### Print file specs (Printful)

- Format: **PNG**
- Minimum: **4500 x 5400px** for front chest print
- Resolution: **300 DPI**
- Background: **transparent**
- Color: **RGB**

> Ideogram at 4K or 8K output meets print specs. No Magnific needed at those resolutions.

---

## 9. Cloning and Running a Project

### Before you clone — confirm with your collaborator:
- SSH clone URL: `git@github.com:[username]/[repo].git`
- `.env` file (environment variables — API keys, secrets — never in GitHub)
- Tech stack — determines the run command
- Branching rules — how they want changes handled

### Clone sequence

```bash
mkdir ~/projects && cd ~/projects
git clone git@github.com:[username]/[repo].git
cd [repo]
ls -la                # see all files including hidden ones
cat README.md         # READ THIS FIRST — it tells you how to run it
```

### Install and run

```bash
npm install           # install dependencies — once after clone
npm run dev           # start local dev server — typically localhost:3000
```

### Environment variables

```bash
touch .env            # create env file
# paste in variables from collaborator
```

`.env` files are never committed to GitHub. They contain live secrets. Handle out of band — iMessage, email, etc.

**Works without `.env`:** Frontend changes, layout, copy, design, component work.  
**Breaks without `.env`:** Any external service call — database, payments, email, APIs.

### Full local setup sequence

```
SSH keys → git clone → npm install → add .env → npm run dev → claude
```

---

## 10. New Machine Setup Checklist

Work through in order. Each step is a dependency for the next.

- [ ] `xcode-select --install` — Command Line Tools
- [ ] Install Homebrew — `brew.sh`
- [ ] Add Homebrew to PATH (Apple Silicon — see Section 5)
- [ ] `brew install node` — verify with `node --version` (need v18+)
- [ ] `git config --global user.name "Your Name"`
- [ ] `git config --global user.email "you@email.com"`
- [ ] `ssh-keygen -t ed25519 -C "you@email.com"` — generate SSH key
- [ ] Add public key to GitHub — Settings → SSH and GPG Keys
- [ ] `ssh -T git@github.com` — test connection
- [ ] `npm install -g @anthropic-ai/claude-code` — verify with `claude --version`
- [ ] `git clone git@github.com:kidneyman10/jordan-notes.git` — clone reference repo
- [ ] Clone project repos
- [ ] `npm install` in each project
- [ ] Add `.env` files from collaborators
- [ ] `npm run dev` — confirm localhost loads

---

## 11. Best Practices & Learnings
*Captured in the field. Tell Claude to add new entries as you learn.*

---

### Terminal

**Password input is invisible by design**
Terminal never shows characters when typing passwords — not even dots. Type blind, hit Enter. It's working.

**File paths are case and space sensitive**
`Claude\ Projects` ≠ `claude projects`. One wrong character = file not found. Use Tab to autocomplete paths.

**Homebrew PATH must be manually set on Apple Silicon**
After installing Homebrew, the three PATH commands must be run or `brew` won't be recognized. See Section 5.

---

### GitHub & Git

**Cloning is passive — you're safe**
Cloning copies the repo to your machine. No changes, no risk. Explore freely after cloning.

**Always use `git push origin branch-name` on shared repos**
Plain `git push` on main can deploy directly to production on repos with CI/CD pipelines. Always be explicit.

**Confirm branching rules before first commit**
Every shared repo has its own conventions. Ask before touching anything.

---

### npm & Node

**Deprecation warnings are safe to ignore**
Warnings like `querystring@0.2.0 is Legacy` are normal — outdated but functional.

**Never run `npm audit fix --force` on a shared repo**
Can introduce breaking changes. Check with your collaborator first.

**npm install is idempotent**
Safe to run anytime. Only necessary after first clone or when `package.json` changes.

---

### Claude Code & Desktop

**Give Claude goals, not tasks**
"Build a product card with image, name, price, and add-to-cart" beats "create ProductCard.jsx".

**Always `git diff` before committing**
Review what Claude changed before staging. Fast doesn't mean perfect.

**CLAUDE.md is your force multiplier**
A well-written CLAUDE.md means less re-explaining context every session. Invest in it early on any project.

**Desktop Code tab vs Terminal**
Use Desktop for visual work and iteration. Use Terminal for scripting and automation. Run both simultaneously — they share config.

**Leave worktree unchecked when starting out**
Worktree is powerful for parallel work but adds complexity. Manage branches manually until comfortable.

---

### Creative Stack

**Midjourney + Ideogram > either alone**
MJ for art, Ideogram for text. Upload MJ output as image reference in Ideogram to get text rendered in matching style.

**Ideogram 4K/8K meets print specs**
No Magnific needed if downloading at 4K or higher. Saves $39/month.

**Remove all text from MJ prompts**
Midjourney cannot reliably render accurate text. Generate art only, add all text in Ideogram or Canva.

---

### Toolstack Costs (as of April 2026)

| Tool | Monthly Cost | Notes |
|------|-------------|-------|
| Midjourney | $10 | Art generation |
| Ideogram | $15 | Text-in-image |
| Magnific AI | $39 | Skip — Ideogram 4K covers it |
| Canva Pro | $15 | Optional |
| Claude Pro/Max | varies | Core tool |

**Lean monthly stack: ~$25/month to build and ship.**

---

*Last updated: April 2026 — v3*
