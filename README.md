# NB Robotics 8767 — Scouting App
**2026 FRC REBUILT Season**

This repo contains two individual scouting tools for FRC robotics:
- **Pre-Scouting** (`pre_scout/`) - pulls team data from The Blue Alliance and Statbotics API's before the event, autopopulates this data every hour.
- **Match-Scouting** (`docs/`) - a mobile web app for live in-match scouting, and a merge script to combine CSV results.
- **Dependency** - this repo requires python! Please download the latest version of python at [Python](https://www.python.org/downloads/)

Fork this repository for your own use!

---

## Repo Structure

```
├── pre_scout/
│   ├── generate_assignments.py      # Assign teams for each scouter
│   ├── generate_scouting_sheet.py   # Pre-event data script
├── docs/
│   ├── index.html                   # Match scouting web app (served via GitHub Pages)
│   └── merge_scouting.py            # Merges exported CSVs into a single xlsx
├── match_results/                   # Drop scouter CSVs here before merging (gitignored)
├── .gitignore
└── README.md
```

---

## pre_scout folder

### Setup (first time)

1. **Install dependencies:**
    ```bash
    pip install requests openpyxl statbotics
    ```

2. **Get a TBA API key:**
    - Go to [thebluealliance.com/account](https://www.thebluealliance.com/account)
    - Sign in and generate a Read API key
    - Use this API key as a repository secret under the name "TBA_KEY2"

3. **Edit the script for your event:**
    - Go to line 21 of the `generate_scouting_sheet.py` file and edit for your event key.
    - Go to line 24 of the `generate_assignments.py` file and edit for your event key.

### Running the scripts

Github Actions will automatically run the scripts on commit to the repo! 

This will generate a `.xlsx` file in the base repo root named after the event, simply import to google sheets!

### What's in the spreadsheet (generate_scouting_sheet.py)
 
| Sheet | Contents |
|---|---|
| **Overview** | All teams with rank, OPR, EPA, auto/teleop/endgame breakdown |
| **EPA (Statbotics)** | Sorted by EPA End descending |
| **OPR (TBA)** | Sorted by OPR descending |
| **Rankings (TBA)** | Sorted by event rank |
| **2nd Pick List** | Teams sorted by OPR with a DNP column for alliance selection |
 
> **Note:** OPR and rankings won't be available until the event has started. The script will skip them and fill in `N/A`.

### What's in the spreadsheet (generate_assignments.py)
 
 | Sheet | Contents |
|---|---|
| **Full Schedule** | Each match in order with assigned scouters |
| **Scouter tabs** | Each scouter has a tab with solely their teams to scout |
| **Load summary** | Summary of how many teams each scouter will scout |

> **Note:** Schedule is not available before competition!

### Automatic updates via GitHub Actions
 
The workflow runs every hour and on every push. It automatically re-runs the script and commits the updated `.xlsx` to the repo. To trigger it manually:
 
1. Go to your repo on GitHub
2. Click **Actions → Run Scouting Script → Run workflow**
 
For this to work, make sure your TBA API key is saved as a repository secret:
- **Settings → Secrets and variables → Actions → New repository secret**
- Name: `TBA_KEY2`
- Value: your API key

---

## docs folder

### Accessing the web app

The match scouting app is hosted on Github Pages at:
```
https://<your-username>.github.io/<your-repo-name>/
```

Share this link with all scouters before the event. It works on any phone browser.

### Using the app
 
Each scouter is responsible for tracking **one robot per match**.
 
**Before the match:**
- Enter the **Match #** and **Team #** you are scouting
- Select your **Alliance** (Red or Blue)
- Enter your **name** in the Scouter field (only needs to be done once — it persists)
 
**During Auto (first 20 seconds):**
- Tap **+** for each fuel scored in the Hub
- Toggle **Mobility** — did the robot leave its starting zone?
- Toggle **Auto Tower Climb** — did it attempt/succeed a Level 1 climb?
 
**During Teleop:**
- Set **Est. Balls/Sec** — your best estimate of the robot's shooting rate
- Set **Accuracy %** — how often shots are going in (increments of 5%)
- Select the robot's **Preferred Field Zone** (Trench, Bump, or Both)
 
**During Endgame:**
- Select the **Tower Climb** level achieved (None / L1 / L2 / L3)
 
**After the match:**
- Rate **Defense** (1–5 stars) — how effective was their defense?
- Rate **Driver Skill** (1–5 stars) — overall driving quality
- Toggle **Played Defense?**
- Add any **Notes** (penalties, fouls, strategies, mechanical issues)
- Tap **⚡ SAVE ENTRY** — the match # will auto-increment for the next match
 
> **Tip:** Data is saved to your phone's local storage. It will persist if you refresh the page, but clearing your browser data will wipe it. Export regularly!

### Exporting your data
 
At the end of a session (or whenever you want to back up):
 
1. Tap **⬇ EXPORT CSV** — a CSV file will download to your phone
2. Send the file to the scouting lead via AirDrop, iMessage, email, etc.
 
To clear your saved entries after exporting, tap **🗑 CLEAR ALL ENTRIES**. You'll be prompted to confirm before anything is deleted.
 
---

## Merging scouting data
 
Once you've collected CSVs from all scouters:
 
### Setup (first time only)
```bash
pip install pandas openpyxl
```
 
### Steps
 
1. Create a folder called `match_results` in the repo root (if it doesn't exist)
2. Drop all the CSV files from your scouters into `match_results/`
3. Run:
   ```bash
   python docs/merge_scouting.py match_results
   ```
4. The output file `scouting_merged.xlsx` will appear in the repo root
 
### What's in the merged spreadsheet
 
| Sheet | Contents |
|---|---|
| **All Entries** | Every row from every scouter, sorted by match then team |
| **By Team** | Same data sorted by team number then match |
| **Summary** | Per-team averages: auto fuel, balls/sec, accuracy, endgame pts, defense, driver skill |
 
The Summary sheet is sorted by **Avg Balls/Sec** descending, making it easy to identify the highest-output shooters at a glance.
 
### Automatic merging via GitHub Actions
 
If you commit your CSVs to the `match_results/` folder, the GitHub Actions workflow will automatically run the merger and commit the updated `scouting_merged.xlsx`. 
 
---

## Quick Reference
 
| Task | Command |
|---|---|
| Generate Assignments | `python pre_scout/generate_assignments.py` |
| Run pre-scout script | `python pre_scout/generate_scouting_sheet.py` |
| Merge match CSVs | `python docs/merge_scouting.py match_results` |
| Access scouting app | Open GitHub Pages URL on phone |
| Trigger Actions manually | GitHub → Actions → Run workflow |