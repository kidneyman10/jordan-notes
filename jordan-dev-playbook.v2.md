# Jordan's Dev Playbook
> Terminal · GitHub · Claude Code · Xcode · Cowork · Dispatch

A living reference document. Update it as you learn. Push it to GitHub so it's always accessible.

---

## Table of Contents
1. [Terminal — the foundation](#1-terminal--the-foundation)
2. [GitHub — version control](#2-github--version-control)
3. [Claude Code — AI in the terminal](#3-claude-code--ai-in-the-terminal)
4. [Xcode — Apple's dev environment](#4-xcode--apples-dev-environment)
5. [Cowork — desktop automation](#5-cowork--desktop-automation)
6. [Dispatch — scheduled automations](#6-dispatch--scheduled-automations)
7. [UMM Quickstart — clone and run locally](#7-umm-quickstart--clone-and-run-locally)
8. [Immediate Next Steps](#8-immediate-next-steps)

---

## 1. Terminal — the foundation

Everything in this stack runs through Terminal. It's how you talk to your Mac directly — installing tools, navigating files, running Claude Code, pushing to GitHub. All of it.

**Open Terminal:** `Cmd + Space` → type "Terminal" → Enter

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

This installs Apple's Command Line Tools — required for Git, Homebrew, Node, and Claude Code. Do this before anything else.

---

## 2. GitHub — version control

**Git** = version control system running locally on your machine.  
**GitHub** = the cloud where repos live and teams collaborate.

Your partner owns the UMM repo on GitHub. You clone it locally, work, and push changes back up.

### First-time setup

```bash
git --version                               # confirm git is installed (need 2.x+)
git config --global user.name "Your Name"   # set identity — do once
git config --global user.email "you@email.com"  # must match GitHub account email
```

### SSH key setup (authenticate without passwords)

SSH keys are how your machine proves its identity to GitHub without you typing a password every time.

```bash
ls ~/.ssh                              # check for existing keys — look for id_rsa or id_ed25519
ssh-keygen -t ed25519 -C "you@email.com"  # generate new key if none exists
                                       # hit Enter through all prompts (default location, no passphrase is fine)
cat ~/.ssh/id_ed25519.pub              # copy this output
```

Then: GitHub → Settings → SSH and GPG Keys → New SSH Key → paste it in.

```bash
ssh -T git@github.com                  # test the connection
# Should return: "Hi [username]! You've successfully authenticated."
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

### Branches (important for shared repos)

```bash
git branch                     # see all branches, current one is starred
git checkout -b my-feature     # create and switch to a new branch
git checkout main              # switch back to main
git merge my-feature           # merge your branch into current branch
```

> ⚠️ **Don't push directly to `main` on a shared repo.**

---

### UMM Branching Rules (from Francis)

UMM uses an automated deployment pipeline tied directly to GitHub. This means how you push code determines where it goes live. Get this wrong and you push untested code straight to the live site.

**How it works:**
- `main` branch = **production** (the live site at ultramagamart.com)
- Any other branch = **staging** (a preview environment for testing)
- Pushing a branch to GitHub automatically builds and deploys it to staging
- Merging a tested branch into main automatically deploys to production

**Your workflow every single time:**

```bash
cd ~/projects/ultramagamart
git pull                              # always pull latest before starting
git checkout -b your-branch-name      # create and switch to a new branch
                                      # name it something descriptive: jordan-product-card, jordan-homepage-copy
# make your changes / run claude code
git add .
git commit -m "what you changed"
git push origin your-branch-name      # pushes branch → triggers staging build
```

Then: review on staging → tell Francis it's ready → Francis merges to main → goes live.

**Branch naming convention — keep it simple:**
```
jordan-[what-you-changed]
# examples:
jordan-homepage-banner
jordan-product-page-copy
jordan-new-tshirt-card
```

> ⚠️ **Never run `git push` alone on UMM — always use `git push origin your-branch-name`.** Plain `git push` on main would deploy directly to production.

---

## 3. Claude Code — AI in the terminal

Claude Code is an agentic CLI — run it inside a project directory and it reads your codebase, writes code, runs commands, and iterates. Most powerful way to use Claude for real development work.

### Install

```bash
node --version                              # check Node is installed. Need v18+
                                            # if missing: install from nodejs.org or brew install node
npm install -g @anthropic-ai/claude-code    # install Claude Code globally
claude --version                            # verify install succeeded
```

### Basic usage

```bash
cd ~/projects/your-project   # ALWAYS navigate into your project directory first
claude                       # launch interactive session — reads project files automatically
claude "explain what this codebase does"    # pass a task inline (non-interactive)
claude --help                # see all available flags and options
```

### Key patterns

**Give it a goal, not a task:**
```
"Add a product card component that shows image, name, price, and add-to-cart button"
```
Not: "Create a file called ProductCard.jsx"

**CLAUDE.md — your project's standing instructions:**  
Create a file called `CLAUDE.md` in your project root. Claude Code reads it every session. Put:
- What the project is
- Tech stack and key dependencies
- Coding conventions (file structure, naming, style)
- What not to touch

```bash
touch CLAUDE.md   # create it
# then open in VS Code: code CLAUDE.md
```

**Always review before committing:**
```bash
git diff          # see exactly what Claude changed before you add/commit
```

> 💡 Claude Code bills against your Anthropic API credits — not your Claude.ai subscription. First launch will prompt you to authenticate with your Anthropic account.

---

## 4. Xcode — Apple's dev environment

Two distinct pieces:
- **Command Line Tools** — lightweight, runs in Terminal, required for everything
- **Full Xcode app** — GUI IDE, required for iOS development (~13GB)

### Setup

```bash
xcode-select --install         # install Command Line Tools (do this first on any new Mac)
xcode-select -p                # verify — should return /Library/Developer/CommandLineTools
```

Install full Xcode from the **Mac App Store** when you're ready for iOS work. It's free, just large.

### Homebrew — install this too

Homebrew is the Mac package manager. Makes installing tools trivial.

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
# then install things like:
brew install node
brew install git
```

### Xcode for iOS development

| Feature | What it does |
|---------|-------------|
| Simulator | Run iOS apps on your Mac without a physical device |
| File Navigator | Left panel — project file tree, better than Finder for code |
| SwiftUI | Apple's modern UI framework — start here, not UIKit |
| Build & Run | `Cmd + R` to build and launch in simulator |

> **Start with SwiftUI.** It's declarative, modern, and Claude Code handles it well. UIKit is legacy territory — avoid unless you have a specific reason.

---

## 5. Cowork — desktop automation

Cowork is Anthropic's desktop automation tool — Claude with eyes on your screen and hands on your apps. Use it for repetitive multi-step desktop workflows you'd normally do manually.

**Install:** Download from claude.ai → Apps section.

### What it's good for

- Batch rename and organize files (Midjourney outputs, design exports)
- Cross-app workflows — pull from one app, format, drop into another
- File prep pipelines — resize, rename, sort before uploading to Printful or Canva
- Research aggregation — open sources, extract info, compile into a doc

### UMM-specific use cases

- Organize Midjourney outputs by concept/date → prep for Magnific upscaling → sort into Canva folders
- Batch process design exports and rename for Printful upload specs
- Pull Printful order data and compile into a summary

### How to start

Describe your task in plain English. Cowork asks for permissions before acting. Start with something small and specific — a 15-minute repetitive task — before building complex pipelines.

> 💡 This is the passive income multiplier for your workflows. Automate the boring parts so you're only doing the creative and strategic work.

---

## 6. Dispatch — scheduled automations

Dispatch lets you schedule and trigger Claude-powered automations. Think: cron jobs with AI. Early access / beta — check claude.ai for current availability.

### Core concept

Define a task → set a trigger → let it run without you.

**Trigger types:**
- Time-based: "every day at 8am"
- Event-based: "when a file is dropped in this folder"
- Webhook: triggered from an external app or API

### UMM potential

- Auto-draft social posts on a schedule
- Pull Printful sales data and summarize weekly
- Trigger content pipeline when new assets land in a folder

### Prereq

Get comfortable with Cowork first. Dispatch is the "set it and forget it" layer on top of solid automation thinking. You need to know what workflows are worth automating before you schedule them.

---

## 7. UMM Quickstart — clone and run locally

### Before you clone — get from your partner:
- The GitHub repo URL (SSH format: `git@github.com:[username]/[repo].git`)
- A copy of the `.env` file (environment variables — API keys, Stripe, Printful tokens)
- Confirmation of the tech stack (Next.js? Vite? Shopify?) — determines how to run it

### Clone sequence

```bash
mkdir ~/projects && cd ~/projects           # create a projects folder
git clone git@github.com:[partner]/ultra-maga-mart.git  # clone the repo
cd ultra-maga-mart                          # enter the project directory
ls -la                                      # see all files including hidden ones
cat README.md                               # READ THIS FIRST — it'll tell you how to run it
```

### Install and run (typical Node/React project)

```bash
npm install          # install dependencies — only needed once (or when deps change)
npm run dev          # start local dev server — usually opens at localhost:3000 or :5173
```

### Add environment variables

```bash
touch .env           # create your .env file
# open it: code .env
# paste in the variables your partner sent you
```

`.env` files are never committed to GitHub (they're in `.gitignore`). Handle them separately.

### Bring in Claude Code

```bash
# from inside the project directory:
claude               # Claude Code now has full context of the UMM codebase
```

**Full sequence when everything is wired:**
```
SSH keys set up → git clone [repo] → npm install → add .env → npm run dev → claude
```

---

## 8. Immediate Next Steps

Work through these in order. Check each one off.

### Step 1 — Command Line Tools
```bash
xcode-select --install
```
If it says "already installed," you're good. Move on.

### Step 2 — Verify or set up SSH keys
```bash
ls ~/.ssh
```
If you see `id_ed25519` or `id_rsa` — keys exist. Test them:
```bash
ssh -T git@github.com
```
If no keys exist, generate them (see Section 2 above).

### Step 3 — Add SSH key to GitHub
If you generated keys in Step 2:
```bash
cat ~/.ssh/id_ed25519.pub   # copy this output
```
GitHub → Settings → SSH and GPG Keys → New SSH Key → paste.

### Step 4 — Get repo info from your partner
You need:
- [ ] SSH clone URL
- [ ] `.env` file contents
- [ ] Confirmation of tech stack
- [ ] Any branching rules they want followed

### Step 5 — Clone UMM
```bash
mkdir ~/projects && cd ~/projects
git clone git@github.com:[partner]/[repo].git
cd [repo]
cat README.md
```

### Step 6 — Install and run
```bash
npm install
# create .env, add variables from partner
npm run dev
```
Open `localhost:3000` (or whatever the README says) in your browser.

### Step 7 — Install Claude Code
```bash
npm install -g @anthropic-ai/claude-code
claude --version
```

### Step 8 — Run Claude Code in UMM
```bash
cd ~/projects/ultra-maga-mart
claude "explain what this project does and how it's structured"
```

---

## Best Practices & Learnings
*Captured in the field. Updated on demand — tell Claude to add new entries as you learn.*

---

### Terminal

**Password input is invisible by design**
Terminal never shows characters when typing passwords — not even dots. Type blind and hit Enter. It's working even if it looks like nothing is happening.

**Homebrew PATH setup on Apple Silicon Macs**
After installing Homebrew, must run these three commands or `brew` won't be recognized in Terminal:
```bash
echo >> /Users/jordan/.zprofile
echo 'eval "$(/opt/homebrew/bin/brew shellenv zsh)"' >> /Users/jordan/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv zsh)"
```

---

### GitHub & Git

**UMM branching — non-negotiable rules (from Francis)**
- `main` = production (ultramagamart.com) — never push directly
- Any other branch = auto-deploys to staging (staging.ultramagamart.com)
- Always create a branch before touching any files: `git checkout -b jordan-[description]`
- Always push with: `git push origin your-branch-name` — never plain `git push` on UMM

**Cloning is passive — you're safe**
Cloning a repo just copies it to your machine. You haven't changed or pushed anything. No risk until you start making changes and pushing.

**File paths in Terminal are case and space sensitive**
If a folder has spaces in the name, escape them with backslashes: `Claude\ Projects` or wrap the whole path in quotes: `"Claude Projects"`. One wrong character = file not found.

---

### npm & Node

**Deprecation warnings are safe to ignore**
Warnings like `querystring@0.2.0: The querystring API is considered Legacy` are normal — outdated but functional. Not your problem to fix on a shared repo.

**Vulnerability warnings after npm install**
Common on shared projects. Never run `npm audit fix --force` without checking with Francis first — it can introduce breaking changes.

**npm install only needed once**
Run it once after cloning, or when dependencies change (someone updates `package.json`). Safe to run again anytime if unsure.

---

### UMM Specific

**Stack confirmed**
Next.js 16, React 19, TypeScript, Tailwind CSS 4, Shadcn/ui, AWS (DynamoDB, Lambda, Cognito, SES, CloudFront), Stripe, Printful, SST v3, GitHub Actions CI/CD.

**Environment variables (.env)**
Never committed to GitHub — exists only on Francis's machine and in GitHub Actions secrets. Required to connect to AWS, Stripe, Printful locally. Without it, frontend loads at localhost:3000 but all service calls fail. Frontend/UI changes work fine without it. Request from Francis directly.

**Localhost vibe coding workflow**
```
npm run dev → localhost:3000 → Claude Code changes → browser review → git push origin branch → staging auto-deploys → Francis reviews → merges to main → production
```

**Known Gaps = your product roadmap**
From the README — open opportunities on UMM:
- No product search or filtering
- No discount/promo codes
- No product reviews or ratings
- No cart persistence
- No shipping notification emails
- Analytics collected but no admin visualization
- No GDPR consent

---

### Cowork

**Playbook sync job**
Build this in Cowork as a manual trigger job called "Sync Playbook to GitHub":
```bash
cp ~/Downloads/jordan-dev-playbook.md ~/projects/jordan-notes/
cd ~/projects/jordan-notes
git add .
git commit -m "update playbook"
git push
```

---

### jordan-notes Repo

**Purpose:** Single source of truth for Jordan's dev playbook. Lives at `github.com/kidneyman10/jordan-notes`. Clone is at `~/projects/jordan-notes/`.

**Manual update workflow (before Cowork job is built):**
```bash
cp ~/Downloads/jordan-dev-playbook.md ~/projects/jordan-notes/
cd ~/projects/jordan-notes
git add . && git commit -m "update playbook" && git push
```

---

*Last updated: April 2026*
