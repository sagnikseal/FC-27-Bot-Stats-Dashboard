# FC 27 Clubs — Bot G&A Dashboard

A static dashboard for tracking EA Sports FC 27 Clubs AI bot **Matches Played**, **Goals**, and **Assists**, powered by Google Forms, Google Sheets (published as CSV), and GitHub Pages.

## Files

- `index.html` — Self-contained dashboard (Tailwind CSS, Chart.js, Papa Parse) configured to fetch your published Google Sheet CSV on load.
- `README.md` — Setup and maintenance notes.

## How It Works

1. Submit match stats via your linked **Google Form** (`Bot Name`, `Matches Played`, `Goals`, `Assists`).
2. Responses append automatically to your **Google Sheet** (`Form Responses 1`).
3. **GitHub Pages** serves `index.html`, which fetches the published CSV URL (`DEFAULT_CSV_URL`) and renders KPIs, charts, and the bot leaderboard.

## Updating the CSV URL

If you ever link a new sheet or tab, update `DEFAULT_CSV_URL` inside `index.html` with the new **File → Share → Publish to the web → CSV** link and commit the change.
