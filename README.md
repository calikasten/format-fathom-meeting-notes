# Format Fathom Meeting Notes
Automate the path from a Fathom meeting summary to a clean markdown note. Last Updated 04/29/2026.

## What It Does

Fathom exports meeting summaries as raw text full of markdown timestamp links, inline bold prefixes, and inconsistent header levels. This pipeline turns each Fathom note into a clean markdown file saved within an Obsidian vault directory.

## How It Works

```
Fathom meeting ends
        |
        v
[Zapier]   Fathom trigger; "Upload File" action drops a .txt into Dropbox
        |
        v
[Dropbox]  File syncs to ~/Library/CloudStorage/Dropbox/Fathom Meeting Notes/
        |
        v
[launchd]  Polls the inbox every 30 seconds; finds the new .txt
        |
        v
[bash]     Calls AppleScript per file; on success, moves the original to processed/
        |
        v
[AppleScript]  Builds output paths; invokes Python; fires a notification
        |
        v
[Python]   Strips links, removes bold, reorders sections, writes the .md
        |
        v
[iCloud]   .md lands in ~/Library/Mobile Documents/com~apple~CloudDocs/SPINS/_INBOX/
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
