# dvwa-bruteforce-detection 🔍

A compact, forensic-style project that demonstrates how to detect brute-force login activity against a DVWA (Damn Vulnerable Web Application) instance by analyzing web server access logs.

This repo includes an example Apache access log, a processed timeline of detected events, and screenshots illustrating failed and successful login attempts. The aim is to show how simple log analysis and timeline-building techniques can expose brute-force behavior.

---

## What's included ✅

- `data/raw_logs/access.log` — example Apache access log captured from a DVWA instance.
- `data/processed/Detection Timeline (from access.log).xlsx` — a spreadsheet showing extracted / analyzed events and a timeline derived from the raw log.
- `screenshots/` — images showing the DVWA login page and example failed attempts (useful for reports / presentations).

There are no analysis scripts included in this repo; the analysis was performed manually and via spreadsheet. See the Usage section for suggested commands and a quick-analysis recipe.

## Project goals 🎯

- Demonstrate how to detect brute-force login attempts in web server logs.
- Provide a minimal dataset and timeline visualization to practice forensic workflows.
- Offer reproducible example commands and next steps for building automated detection scripts.

## Log patterns used to detect failed/successful attempts

When analyzing the included Apache `access.log` you'll see common patterns that can be used to infer login success vs failure:

- Repeated `POST /dvwa/login.php` followed by `GET /dvwa/login.php` indicates repeated login POSTs that redirect back to the login page — common for failed login attempts.
- `POST /dvwa/login.php` followed by a `GET /dvwa/index.php` indicates a successful login (the app redirected to the index page after successful auth).

These patterns are useful for quick forensic triage and automated detection rules.

## Quick analysis examples (command-line)

Below are small shell examples to extract and inspect login-related lines from `access.log`. On Windows PowerShell just run the same commands translated to PowerShell as noted.

1) Show all login-related lines (Bash / WSL / macOS / Linux):

```bash
grep "/dvwa/login.php" data/raw_logs/access.log
```

PowerShell equivalent:

```powershell
Select-String -Path data\raw_logs\access.log -Pattern '/dvwa/login.php'
```

2) Extract POSTs and look for repeated sequences (Bash):

```bash
grep 'POST /dvwa/login.php' data/raw_logs/access.log | cut -d' ' -f1,4,5,6,7,8,9
```

3) Find POSTs that were followed by an index.php GET (likely successful logins):

```bash
grep 'POST /dvwa/login.php' -n data/raw_logs/access.log
# note the resulting line numbers and see the next few lines — if the next GET is /dvwa/index.php it's likely a success
```

For a robust, reproducible workflow use a simple Python or pandas script to parse, group by remote IP, and analyze sequences/timestamps.

## Suggested next steps / improvements 🔧

- Add a Python or Jupyter notebook parser to turn `access.log` into a structured CSV (fields: timestamp, IP, method, url, status, user-agent), then detect sliding-window bursts of failed attempts.
- Integrate into a SIEM or ELK/OSquery pipeline for live detection and alerts.
- Create visualizations (time-series plots per IP, heatmaps) from the processed excel timeline.

## How to reproduce locally — minimal guidance

1. Install XAMPP / Apache & set up DVWA locally (or use a captured access.log file).
2. Place the server access log at `data/raw_logs/access.log`.
3. Use one of the CLI examples above or load the file into a spreadsheet/Notebook to build a timeline.

## Contributing 🙌

If you'd like to add detection scripts, notebooks, or automation — please open an issue or submit a PR. Useful additions:

- a parser script to export CSV/JSON
- a Jupyter Notebook with step-by-step analysis and plots
- automated tests and a small sample dataset generator

## License

This repository does not include a license file. If you want permissive reuse please add a LICENSE (e.g., MIT) or state the desired license in a new PR.

---

If you'd like, I can also add a reproducible script / notebook to parse `access.log` and detect the failed- vs successful-login sequences automatically — would you like me to implement that next? ✅
