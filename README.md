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

Stats are logged via your linked Google Form strictly when contributions occur:
* **Responses are ONLY submitted when a bot makes a goal contribution ($G + A \ge 1$).**
* There is no concept of "matches played" or per-match averages — the dashboard purely aggregates confirmed offensive output.

### Submitted Fields
| Form Field | Description |
| :--- | :--- |
| `Timestamp` | Submission date & time (used for cumulative growth timeline). |
| `Bot Name` | Name of the AI bot (e.g. `A. Morales`, `K. Lindqvist`). |
| `Goals` | Number of goals scored. |
| `Assists` | Number of assists provided. |

---

## Statistical Metrics & How They Are Calculated

### 1. Cumulative Offensive Volume
* **Goals ($G$)**: Total goals scored across all logged entries:
  $$G = \sum \text{Goals}$$
* **Assists ($A$)**: Total assists provided across all logged entries:
  $$A = \sum \text{Assists}$$
* **Total G+A**: Combined goal contributions:
  $$\text{Total G+A} = G + A$$

### 2. Relative Impact & Distribution
* **Club G+A Share %**:
  The bot's percentage share of the club's total AI bot goal contributions:
  $$\text{Club Share \%} = \frac{\text{Total G+A}_{\text{bot}}}{\sum \text{Total G+A}_{\text{team}}} \times 100\%$$
* **Goal Share %**:
  $$\text{Goal Share \%} = \frac{G_{\text{bot}}}{\sum G_{\text{team}}} \times 100\%$$
* **Assist Share %**:
  $$\text{Assist Share \%} = \frac{A_{\text{bot}}}{\sum A_{\text{team}}} \times 100\%$$

---

## Tactical Role Profiles

Each bot is automatically assigned a tactical role based on their **Goal-to-Contribution Ratio** ($G / (G + A)$):

| Role Badge | Archetype | Criteria | Description |
| :---: | :---: | :---: | :--- |
| ⚽ **Finisher** | Pure Goalscorer | $\ge 70\%$ of G+A from Goals | Lethal finisher who converts chances. |
| 🎯 **Playmaker** | Creator / Maestro | $\ge 70\%$ of G+A from Assists | Creative engine who sets up teammates. |
| 🔥 **Dual-Threat** | Complete Attacker | Balanced ($>30\%$ & $<70\%$ Goals) | All-round attacker dangerous at both scoring and assisting. |
| 🛡️ **Depth** | Squad Player | $G + A = 0$ | Squad rotation bot. |

---

## Trophy Cabinet & Club Awards

Featured cards at the top of the dashboard spotlight club leaders:

* 👑 **Club MVP**: Bot with the highest overall Total G+A (and displays their club output share %).
* 👟 **Golden Boot**: Leading goalscorer (and displays their share of team bot goals).
* 🎯 **Top Playmaker**: Leading assist provider (and displays their share of team bot assists).

---

## Visual Charts

1. **Club G+A Share (Donut Chart)**: Visualizes the percentage distribution of bot contributions across top performers and the rest of the squad.
2. **Goals vs. Assists by Bot (Stacked Bar Chart)**: Compares individual scoring volume versus playmaking volume side-by-side.
3. **Cumulative G+A Timeline (Line Chart)**: Tracks progressive goal contribution growth over time. Clicking a bot in the leaderboard filters this chart to show only that bot's curve.

---

## Dashboard Features

* **Interactive Bot Filtering**: Click any bot row in the Leaderboard to highlight that bot and filter both the **Cumulative G+A line chart** and the **Recent Contributions** log to that bot. Click again or use the **"✕ Clear filter"** badge to reset.
* **Auto-Refresh Toggle (2m)**: Check the **"Auto-refresh (2m)"** box in the header during club sessions to automatically fetch the latest Google Sheet data every 120 seconds.
* **Multi-Metric Sorting & Search**: Sort by `Total G+A`, `Goals`, `Assists`, or `Club Share %`.
* **Export CSV**: Click **"Export CSV"** to download the clean leaderboard as `fc27-bot-leaderboard.csv`.
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
