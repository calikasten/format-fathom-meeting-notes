# Format Fathom Meeting Notes
Automate the path from a Fathom meeting summary to a clean markdown note. Last Updated 04/29/2026.

## What It Does

Fathom exports meeting summaries as raw text full of markdown timestamp links, inline bold prefixes, and inconsistent header levels. This pipeline turns each Fathom note into a clean markdown file saved within an Obsidian vault directory.

## How It Works

| Source | Action | Description |
|---|---|---|
| Fathom | Trigger | Meeting ends |
| Zapier | Event | Gets AI summary notes from most recent Fathom meeting |
| Zapier | Event | Creates a new .txt file with AI summary notes in file body and title as `YYYY-MM-DD {Meeting Name}` |
| Zapier | Event | New file saved to Fathom Meeting Notes folder in Dropbox
| Dropbox | Trigger | Files synced to folder in local file system |
| launchd | Event | Polls the Fathom Meeting Notes folder every 30 seconds watching for new files |
| launchd | Trigger | Finds newly uploaded .txt file of meeting notes and calls the AppleScript with the file path |
| AppleScript | Event | Script runs; builds destination output path and calls Python script for proper formatting
| Python | Event | Formats meeting notes and saves formatted file to destination output |
| launched | Event | Moves original .txt file to `processed` folder so that notes aren't re-processed accidentally |

## Installation

Clone the repo and run the installer:

```
git clone https://github.com/<your-username>/fathom-notes-pipeline.git
cd fathom-notes-pipeline
./install.sh
```
