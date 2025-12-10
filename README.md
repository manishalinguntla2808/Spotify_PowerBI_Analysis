# 🎧 Spotify Listening Analysis Dashboard (2013–2024)

An interactive **Power BI project** that analyzes Spotify listening history from **2013 to 2024**, uncovering trends in listening behavior, favorite artists, albums, tracks, and time-based usage patterns using dynamic and visually rich dashboards.

---

## 📌 Project Overview

This project transforms raw Spotify streaming data into a **clean, interactive Power BI dashboard** that answers questions like:

- How has my music listening evolved over time?
- Which artists, albums, and tracks do I listen to the most?
- What time and days am I most active on Spotify?
- How does my listening behavior change year over year?

This is a **Power BI–only project** using DAX, Power Query, and data modeling (no Python or external coding).

---

## 🛠 Tools & Technologies Used

- Power BI Desktop  
- DAX (Data Analysis Expressions)  
- Power Query Editor  
- Spotify Streaming History Dataset (CSV/JSON)

---

## 🧩 Data Model Structure

### Fact Table: `spotify_history`
Contains fields like:
- Track Name  
- Album Name  
- Artist Name  
- Timestamp  
- Milliseconds Played  
- Platform  
- Shuffle Flag  
- Skipped Flag  

### Date Dimension Table
A custom **Date Table** was created to support time intelligence calculations.

Relationship:
DateTable[Date] → spotify_history[Date]

---

## 📊 Dashboard Pages & Visuals

### 1. Albums, Artists & Tracks Overview

Each section contains:

- **Line Chart** – Shows trends from 2013 to 2024  
- **KPI Cards** – Total count, Latest Year vs Previous Year and Year-over-Year comparison  
- **Donut Charts** – Weekday vs Weekend listening  
- **Bar Charts** – Top 5 Albums, Artists, and Tracks  

**Key Insights:**
- Growth in music diversity over time  
- Shifts in music preferences  
- Popular listening content  

---

### 2. Listening Hours vs Days (Heatmap)

A **Matrix Heatmap** showing:

- Rows → Hours of the day (0–23)  
- Columns → Days of the week (Mon–Sun)  
- Colors → Listening intensity  

**Insights:**
- Peak listening hours  
- Weekday vs weekend behavior  
- Best time windows for engagement  

---

### 3. Avg Listening Time vs Track Frequency (Scatter Chart)

Scatter plot showing:

- X-axis → Average listening time per track  
- Y-axis → Number of track plays  

Tracks are grouped into four logical quadrants:

| Quadrant | Meaning |
|---------|--------|
| High Frequency + High Time | Favorite tracks |
| High Frequency + Low Time | Often skipped / short tracks |
| Low Frequency + High Time | Long tracks listened occasionally |
| Low Frequency + Low Time | Least engaged tracks |

**Insights:**
- Identifies highly engaging content  
- Highlights skipping behavior  
- Helps optimize playlists  

---

### 4. Detailed Data Table

An interactive table showing:

- Album Name  
- Artist Name  
- Track Name  
- Number of Plays  
- Total Milliseconds Played  
- Average Listening Time  

Used for drill-down and deeper analysis.

---

## 🧮 Key DAX Measures Used

```DAX
Total Listening Minutes = SUM(spotify_history[ms_played]) / 60000

Track Frequency = COUNTROWS(spotify_history)

Avg Listening Time (min) =
DIVIDE(SUM(spotify_history[ms_played]), COUNTROWS(spotify_history)) / 60000

Latest Year Tracks =
CALCULATE(
    DISTINCTCOUNT(spotify_history[track_name]),
    FILTER(ALL(spotify_history), spotify_history[Year] = MAX(spotify_history[Year]))
)

Previous Year Tracks =
CALCULATE(
    DISTINCTCOUNT(spotify_history[track_name]),
    FILTER(ALL(spotify_history), spotify_history[Year] = MAX(spotify_history[Year]) - 1)
)

YoY % Change =
DIVIDE(
    [Latest Year Tracks] - [Previous Year Tracks],
    [Previous Year Tracks]
)

