# FC 27 Clubs — Bot G&A Dashboard

A static, real-time analytics dashboard for tracking EA Sports FC 27 Clubs AI bot **Goals** and **Assists**, powered by Google Forms, Google Sheets (published as CSV), Chart.js, and GitHub Pages.

---

## Architecture & Data Flow

```
[ Google Form ] (Submit: Bot Name, Goals, Assists)
       │
       ▼
[ Google Sheet ] (Form Responses 1)
       │
       ▼ (Publish to Web as CSV)
[ index.html ] (Papa Parse + Chart.js + Tailwind CSS)
       │
       ▼
[ GitHub Pages ] (Live Dashboard)
```

---

## Data Input Workflow: Pure Contribution Tracking

Stats are logged via your linked Google Form. To keep the focus strictly on confirmed impact:
* **Responses are ONLY submitted when a bot makes a goal contribution ($G + A \ge 1$).**
* There is no "matches played" tracking — every logged entry represents a game where the bot scored or assisted.

### Submitted Fields
| Form Field | Description |
| :--- | :--- |
| `Timestamp` | Submission date & time (used for timeline trends and chronological tracking). |
| `Bot Name` | Name of the AI bot (e.g. `A. Morales`, `K. Lindqvist`). |
| `Goals` | Number of goals scored by this bot in this game. |
| `Assists` | Number of assists provided by this bot in this game. |

---

## Statistical Metrics & How They Are Calculated

### 1. Goal Contributions
* **Goals ($G$)**: Total goals scored across all logged entries:
  $$G = \sum \text{Goals}$$
* **Assists ($A$)**: Total assists provided across all logged entries:
  $$A = \sum \text{Assists}$$
* **Total G+A**: Combined offensive contributions:
  $$\text{Total G+A} = G + A$$
* **Contributions (Entries)**: Count of submitted entries where the bot hit the scoresheet ($G + A \ge 1$).

### 2. Impact & Performance Profiles
* **2+ G+A Games (Multi-Contribution)**:
  Number of games where the bot contributed 2 or more G+A ($G + A \ge 2$) in a single entry.
* **Dual Games (Complete Performance)**:
  Number of games where the bot achieved **both** a goal and an assist in the same entry ($G > 0 \land A > 0$).
* **Best Single Game**:
  Highest total G+A recorded in any individual entry:
  $$\text{Best Game} = \max(G + A)$$
* **Club G+A Share %**:
  Percentage share of the club's overall bot goal contributions produced by this bot:
  $$\text{Club Share \%} = \frac{\text{Total G+A}_{\text{bot}}}{\sum \text{Total G+A}_{\text{team}}} \times 100\%$$

---

## Tactical Role Profiles

Each bot is automatically assigned a role badge based on their **Goal-to-Contribution Ratio** ($G / (G + A)$):

| Role Badge | Archetype | Criteria | Description |
| :---: | :---: | :---: | :--- |
| ⚽ **Finisher** | Pure Goalscorer | $\ge 70\%$ of G+A from Goals | Lethal poacher who finishes chances in the box. |
| 🎯 **Playmaker** | Creator / Maestro | $\ge 70\%$ of G+A from Assists | Creative engine who sets up teammates. |
| 🔥 **Dual-Threat** | Complete Attacker | Balanced ($>30\%$ & $<70\%$ Goals) | All-round attacker who scores and assists with equal threat. |
| 🛡️ **Depth** | Squad Player | $G + A = 0$ | Baseline squad contributor. |

---

## Trophy Cabinet & Club Awards

Featured cards at the top of the dashboard dynamically spotlight team leaders:

* 👑 **Club MVP**: Bot with the highest overall Total G+A (and displays their club output share %).
* 👟 **Golden Boot**: Leading goalscorer.
* 🎯 **Top Playmaker**: Leading assist provider.
* 💥 **Most Explosive**: Bot with the most multi-contribution ($2+ \text{ G+A}$) games (tiebreak: highest single-game G+A).

---

## Visual Charts

1. **Club G+A Share (Donut Chart)**: Visualizes the percentage distribution of bot contributions across top performers and the rest of the squad.
2. **Goals vs. Assists by Bot (Stacked Bar Chart)**: Compares individual scoring volume versus playmaking volume side-by-side.
3. **Cumulative G+A Timeline (Line Chart)**: Tracks progressive goal contribution growth over time. Clicking a bot in the leaderboard filters this chart to show only that bot's curve.

---

## Dashboard Features

* **Interactive Bot Filtering**: Click any bot row in the Leaderboard to highlight that bot and filter both the **Cumulative G+A line chart** and the **Recent Contributions** log to that bot. Click again or use the **"✕ Clear filter"** badge to reset.
* **Auto-Refresh Toggle (2m)**: Check the **"Auto-refresh (2m)"** box in the header during club sessions to automatically fetch the latest Google Sheet data every 120 seconds.
* **Multi-Metric Sorting & Search**: Sort by `Total G+A`, `Goals`, `Assists`, `Contributions`, `2+ G+A Games`, `Dual Games`, `Best Game`, or `Club Share %`.
* **Export CSV**: Click **"Export CSV"** to download the aggregated leaderboard as `fc27-bot-leaderboard.csv`.
* **Copy Summary**: Click **"Copy Summary"** to copy a clean Markdown leaderboard table directly to your clipboard for sharing in Discord or club chats.

---

## Updating the Live Google Sheet CSV URL

1. In your Google Sheet:
   * Go to **File → Share → Publish to the web**.
   * Under **Link**, select your response sheet tab (e.g. `Form Responses 1`).
   * Select format: **Comma-separated values (.csv)**.
   * Click **Publish** and copy the generated link.
2. Inside [index.html](index.html), update `DEFAULT_CSV_URL`:
   ```javascript
   const DEFAULT_CSV_URL = "https://docs.google.com/spreadsheets/d/e/.../pub?output=csv";
   ```
3. Commit and push to `main`:
   ```powershell
   git add index.html README.md
   git commit -m "Update CSV URL"
   git push origin main
   ```
   GitHub Pages will automatically rebuild and deploy your dashboard.
