# Format Fathom Meeting Notes
Automate the path from a Fathom meeting summary to a clean markdown note. Last Updated 04/29/2026.

## What It Does

Fathom exports meeting summaries as raw text full of markdown timestamp links, inline bold prefixes, and inconsistent header levels. This pipeline turns each Fathom note into a clean markdown file saved within an Obsidian vault directory.

## How It Works

```
1. Fathom meeting ends
2. Zapier
    a. Trigger for new Fathom meeting recording fires
    b. Gets AI summary notes from most recent Fathom meeting
    c. Creates a new .txt file with AI summary notes in file body and title as `YYYY-MM-DD {Meeting Name}`
    d. New file saved to Fathom Meeting Notes folder in Dropbox
3. Dropbox
    a. Files sync to CALIK-M2 at `~/Library/CloudStorage/Dropbox/Fathom Meeting Notes/`
4. launchd
    a. Polls the Fathom Meeting Notes folder every 30 seconds watching for new files
    b. Finds newly uploaded .txt file of meeting notes and calls the AppleScript with the file path
5. AppleScript
    a. Builds destination output path 
    b. Calls helper Python script for proper formatting
6. Python
    1. Formats meeting notes (strips markdown links, removes bold, reorders sections, saves as .md)
    2. Saves formatted file to destination output (`/Users/ckasten/Library/Mobile\ Documents/com\~apple\~CloudDocs/SPINS/_INBOX`)
7. launchd
    a. Upon successful completion of formatting scripts, moves the original .txt file to `~/Library/CloudStorage/Dropbox/Fathom Meeting Notes/processed` (so that the notes aren’t processed twice)
```

## Zapier Setup

Build a two-step Zap:

| Step | Setting | Value |
|---|---|---|
| Trigger | App | Fathom |
| Trigger | Event | "New AI Summary" |
| Action | App | Dropbox |
| Action | Event | Upload File |
| Action | Folder | `/Fathom Meeting Notes` |
| Action | File Name | `{{YYYY-MM-DD}} {{Meeting Title}}.txt` |
| Action | File Content | The raw summary field from Fathom |

Test the Zap with a sample meeting before turning it on. Confirm a `.txt` file lands in your Dropbox folder. Once that works, the local pipeline takes over automatically.

## Installation

Clone the repo and run the installer:

```
git clone https://github.com/<your-username>/fathom-notes-pipeline.git
cd fathom-notes-pipeline
./install.sh
```

## Files in This Repo

```
fathom-notes-pipeline/
├── README.md                               This file
├── install.sh                              Renders templates and loads the agent
├── scripts/
│   ├── FormatFathomNotes.applescript       Thin wrapper called by launchd
│   └── format_fathom_notes.py              All the formatting logic
└── launchd/
    └── com.user.fathom-formatter.plist.template   Polls the inbox every 30 seconds
```
