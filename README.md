# FC 27 Clubs — Bot G&A Dashboard

A static, real-time analytics dashboard for tracking EA Sports FC 27 Clubs AI bot **Goals** and **Assists**, powered by Google Forms, Google Sheets (published as CSV), Chart.js, and GitHub Pages.

---

## Architecture & Data Flow

```
[ Google Form ] (Submit Match Stats: Bot Name, Goals, Assists)
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

## Data Input Workflow & The Contribution-Only Rule

Stats are recorded via your linked Google Form. To keep stats focused on impact:
* **Responses are ONLY submitted when a bot makes a goal contribution ($G + A \ge 1$).**
* If a bot plays in a match without scoring or assisting, no form response is submitted for that bot.
* **Result:** Every row in the dataset represents a confirmed **Scoring Outing** / **Impact Match**.

### Submitted Columns
| Form Field | Description |
| :--- | :--- |
| `Timestamp` | Submission date and time (used for session clustering and timeline trends). |
| `Bot Name` | Name of the AI bot (e.g. `A. Morales`, `K. Lindqvist`). |
| `Goals` | Number of goals scored by this bot in this match. |
| `Assists` | Number of assists provided by this bot in this match. |

---

## Statistical Metrics & How They Are Calculated

### 1. Match Counts
* **Scoring Outings ($M$)**:
  $$\text{Outings} = \text{Count of submitted entries for the bot}$$
  Because rows are only logged when a bot contributes, each entry equals 1 confirmed scoring outing.
* **Unique Club Matches**:
  Estimated by clustering entry timestamps within a **rolling 5-minute window** ($\Delta t \le 300\text{ seconds}$). When multiple bot entries are submitted around the same time, they are grouped as a single team match.

### 2. Offensive Production
* **Goals ($G$)**: Total goals scored across all outings: $\sum \text{Goals}$.
* **Assists ($A$)**: Total assists recorded across all outings: $\sum \text{Assists}$.
* **Total G+A**: Combined goal contributions:
  $$\text{Total G+A} = G + A$$

### 3. Per-Outing Rates (Averages)
* **Goals / Outing**:
  $$\text{G / Outing} = \frac{G}{M}$$
* **Assists / Outing**:
  $$\text{A / Outing} = \frac{A}{M}$$
* **G+A / Outing (Contribution Density)**:
  $$\text{G+A / Outing} = \frac{G + A}{M}$$

### 4. Consistency & Explosiveness Metrics
* **2+ G+A Outings (Multi-Contribution)**:
  Count of games where the bot contributed 2 or more G+A ($G + A \ge 2$) in a single match.
* **Explosiveness Rate**:
  Percentage of a bot's outings that produced multiple goal contributions:
  $$\text{Explosiveness \%} = \frac{\text{Outings with } G+A \ge 2}{M} \times 100\%$$
* **Dual Outings (Complete Performance)**:
  Count of matches where the bot achieved **both** a goal and an assist in the same game ($G > 0 \land A > 0$).
* **Best Single Match (Personal Best)**:
  Highest total G+A recorded in any individual outing:
  $$\text{Best Game} = \max(G + A)$$
* **Club G+A Share %**:
  The bot's percentage share of all bot goal contributions across the entire club:
  $$\text{Club Share \%} = \frac{\text{Total G+A}_{\text{bot}}}{\sum \text{Total G+A}_{\text{team}}} \times 100\%$$

---

## Role Profiles & Archetype Classification

The dashboard automatically tags each bot with a tactical role badge based on their **Goal-to-Contribution Ratio** ($G / (G + A)$):

| Role Badge | Archetype | Criteria | Description |
| :---: | :---: | :---: | :--- |
| ⚽ **Finisher** | Pure Goalscorer | $\ge 70\%$ of G+A from Goals | Lethal poacher who finishes chances in the box. |
| 🎯 **Playmaker** | Creator / Maestro | $\ge 70\%$ of G+A from Assists | Creative engine who primarily sets up human and AI teammates. |
| 🔥 **Dual-Threat** | Complete Attacker | Balanced ($>30\%$ & $<70\%$ Goals) | Elite all-round forward who scores and assists with equal danger. |
| 🛡️ **Depth** | Squad Player | $G + A = 0$ | Squad rotation bot. |

---

## Trophy Cabinet & Dynamic Club Awards

Featured cards at the top of the dashboard dynamically spotlight team leaders:

* 👑 **Club MVP**: Bot with the highest overall Total G+A (and displays their club output share %).
* 👟 **Golden Boot**: Leading goalscorer (with goals per outing).
* 🎯 **Top Playmaker**: Leading assist provider (with assists per outing).
* 💥 **Most Explosive**: Bot with the most multi-contribution ($2+ \text{ G+A}$) outings (tiebreak: highest single-game G+A).

---

## Visual Charts

1. **Club G+A Share (Donut Chart)**: Visualizes the percentage distribution of bot contributions across top performers and the rest of the squad.
2. **Goals vs. Assists by Bot (Stacked Bar Chart)**: Compares individual scoring volume versus playmaking volume side-by-side.
3. **Cumulative G+A Timeline (Line Chart)**: Tracks progressive goal contribution growth over time. When a bot is selected from the leaderboard, the chart filters dynamically to show only that bot's curve.

---

## Interactive Dashboard Features

* **Interactive Bot Filtering**: Click any bot row in the Leaderboard to highlight that bot and filter both the **Cumulative G+A line chart** and the **Recent Scoring Outings** table to that bot. Click again or use the **"✕ Clear filter"** badge to reset.
* **Auto-Refresh Toggle (2m)**: Check the **"Auto-refresh (2m)"** box in the header during club sessions to automatically fetch the latest Google Sheet data every 120 seconds.
* **Multi-Column Sorting & Instant Search**: Sort by any of the 11 analytical columns (`Total G+A`, `Goals`, `Assists`, `Outings`, `G / Outing`, `A / Outing`, `2+ G+A`, `Dual Outings`, `Best Match`, `Share %`) or filter by bot name.
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
