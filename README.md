# 🎧 Spotify Listening Analysis Dashboard (2013–2024)

An interactive **Power BI project** that analyzes Spotify listening history from **2013 to 2024**, uncovering trends in listening behavior, favorite artists, albums, tracks, and time-based usage patterns using dynamic and visually rich dashboards.

---

## 📌 Project Overview

This project converts raw Spotify streaming data into a powerful **interactive Power BI dashboard** that enables users to analyze:

- Growth in listening habits over time
- Most played artists, albums, and tracks
- Hourly and daily listening behavior
- Track-level engagement patterns

This is a **Power BI only project**, built using **DAX**, **Power Query**, and **data modeling**.

---

## 🛠 Tools & Technologies Used

- Power BI Desktop  
- DAX (Data Analysis Expressions)  
- Power Query  
- Spotify Streaming History Data (CSV/JSON files)

---

## 🧩 Data Model Structure

### Fact Table: `spotify_history`
Key fields:
- Track Name  
- Album Name  
- Artist Name  
- Timestamp  
- Milliseconds Played  
- Platform  
- Shuffle (Yes/No)  
- Skipped (Yes/No)  

### Date Dimension Table
A custom **Date Table** enables time intelligence.

Relationship:
DateTable[Date] → spotify_history[Date]


---

## 📊 Dashboard Preview

### 🔹 Main Overview – Albums, Artists & Tracks

![Spotify Overview Dashboard](assets/spotify_overview.png)

**What this visual shows:**

This section provides a **high-level summary** of listening behavior:

- **Line charts** track growth of Albums, Artists, and Tracks from 2013–2024.
- **KPI cards** compare:
  - Latest Year vs Previous Year
  - Year-over-Year percentage growth
- **Donut charts** show how much listening happens on **Weekdays vs Weekends**.
- **Top 5 bar charts** highlight the most played:
  - Albums
  - Artists
  - Tracks

✅ **What you can expect:**
- Understand overall growth trends
- Identify dominant content
- Spot preference shifts

---

### 🔹 Listening Hours vs Days (Heatmap)

![Listening Heatmap](assets/spotify_heatmap.png)

**What this visual does:**

- Rows = hours of the day (0–23)
- Columns = days of the week
- Color intensity = listening activity

✅ **Insights:**
- Peak listening times
- Daily behavior patterns
- Workday vs weekend habits

---

### 🔹 Track Engagement Scatter Plot

![Track Engagement Scatter](assets/spotify_scatter.png)

This visual analyzes **track-level engagement** based on frequency and depth of listening.

| Quadrant | Behavior Meaning |
|----------|------------------|
| High Frequency + High Time | Favorite songs |
| High Frequency + Low Time | Skipped/Previewed songs |
| Low Frequency + High Time | Occasionally played long tracks |
| Low Frequency + Low Time | Least engaging tracks |

---

### 🔹 Detailed Track Table

![Detailed Table](assets/spotify_table.png)

This visual provides raw-level exploration of:

- Album Details
- Artist Details
- Track-Level Metrics
- Listening Duration

---

## 🧮 DAX Measures & Their Purpose

### 1. Total Listening Minutes
```DAX
Listening Time(min) Value = SELECTEDVALUE('Listening Time (min)'[Listening Time(min)])

### 2. Avg Listening time
```DAX
Avg Listening time = AVERAGE(spotify_history[ms_played])/ 60000

### 3. CF Quadrant
```DAX
CF Quadrant = 
VAR AvgTime = [Avg Listening time] <= 'Listening Time (min)'[Listening Time(min) Value]
VAR TrackFreq = [Track Frequency] >= 'Track Freaquency (Parameter)'[Track Freaquency (Parameter) Value]
VAR RESULT =
    SWITCH(
        TRUE(),
        AvgTime && TrackFreq, 1, --- Low Time & High Freq
        NOT AvgTime && TrackFreq, 2, -- High Time & High Freq
        NOT AvgTime && NOT TrackFreq, 3, -- High Time & Low Freq
        AvgTime && NOT TrackFreq, 4 -- Low Time & Low Freq
    )
    RETURN RESULT

### 4. LatestYearAlbums
```DAX
LatestYearAlbums = 
var _LatestYear = MAX('Date Table'[Year])
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[album_name]), 'Date Table'[Year] = _LatestYear)

### 5. LatestYearArtists
```DAX
LatestYearArtists = 
var _LatestYear = MAX('Date Table'[Year])
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[artist_name]), 'Date Table'[Year] = _LatestYear)

### 6. LatestYearTracks
```DAX
LatestYearTracks = 
var _LatestYear = MAX('Date Table'[Year])
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[track_name]), 'Date Table'[Year] = _LatestYear)

### 7. Max Year
```DAX
Max Year = MAX('Date Table'[Year])

### 8. MinMax Albums Line Chart 
```DAX
MinMax Albums Line Chart = 
var _MaxValue = MAXX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[album_name])))
var _MinValue = MINX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[album_name])))
var _CurrentValue = DISTINCTCOUNT(spotify_history[album_name])
RETURN
    IF(_CurrentValue = _MaxValue || _CurrentValue = _MinValue, _CurrentValue, BLANK())

### 9. MinMax Artists Line Chart
```DAX
MinMax Artists Line Chart = 
var _MaxValue = MAXX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[artist_name])))
var _MinValue = MINX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[artist_name])))
var _CurrentValue = DISTINCTCOUNT(spotify_history[artist_name])
RETURN
    IF(_CurrentValue = _MaxValue || _CurrentValue = _MinValue, _CurrentValue, BLANK())

### 10. MinMax Tracks Line Chart
```DAX
MinMax Tracks Line Chart = 
var _MaxValue = MAXX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[track_name])))
var _MinValue = MINX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[track_name])))
var _CurrentValue = DISTINCTCOUNT(spotify_history[track_name])
RETURN
    IF(_CurrentValue = _MaxValue || _CurrentValue = _MinValue, _CurrentValue, BLANK())

### 11. No of Albums
```DAX
No of Albums = DISTINCTCOUNT(spotify_history[album_name])

### 12. No of Artists
```DAX
No of Artists = DISTINCTCOUNT(spotify_history[artist_name])

### 13. No of Tracks
```DAX
No of Tracks = DISTINCTCOUNT(spotify_history[track_name])

### 14. PreviousYearAlbums
```DAX
PreviousYearAlbums = 
var _LatestYear = MAX('Date Table'[Year])
var _PreviousYear = _LatestYear -1
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[album_name]), 'Date Table'[Year] = _PreviousYear)

### 15. PreviousYearArtists
```DAX
PreviousYearArtists = 
var _LatestYear = MAX('Date Table'[Year])
var _PreviousYear = _LatestYear -1
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[artist_name]), 'Date Table'[Year] = _PreviousYear)

### 16. PreviousYearTracks
```DAX
PreviousYearTracks = 
var _LatestYear = MAX('Date Table'[Year])
var _PreviousYear = _LatestYear -1
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[track_name]), 'Date Table'[Year] = _PreviousYear)

### 17. PY and YoY Albums KPI
```DAX
PY and YoY Albums KPI = 
VAR _latest = [LatestYearAlbums]
VAR _previous = [PreviousYearAlbums]
VAR _YoY = IF(NOT(ISBLANK(_previous)),DIVIDE(_latest - _previous, _previous, 0), BLANK())
RETURN
    if(NOT(ISBLANK(_previous)),
        "vs PY: " & FORMAT(_previous, "#,##0") & 
        " (" & FORMAT(_YoY, "0.00%") & ")",
        "No Data")

### 18. PY and YoY Artists KPI
```DAX
PY and YoY Artists KPI = 
VAR _latest = [LatestYearArtists]
VAR _previous = [PreviousYearArtists]
VAR _YoY = IF(NOT(ISBLANK(_previous)),DIVIDE(_latest - _previous, _previous, 0), BLANK())
RETURN
    if(NOT(ISBLANK(_previous)),
        "vs PY: " & FORMAT(_previous, "#,##0") & 
        " (" & FORMAT(_YoY, "0.00%") & ")",
        "No Data")

### 19. PY and YoY Tracks KPI
```DAX
PY and YoY Tracks KPI = 
VAR _latest = [LatestYearTracks]
VAR _previous = [PreviousYearTracks]
VAR _YoY = IF(NOT(ISBLANK(_previous)),DIVIDE(_latest - _previous, _previous, 0), BLANK())
RETURN
    if(NOT(ISBLANK(_previous)),
        "vs PY: " & FORMAT(_previous, "#,##0") & 
        " (" & FORMAT(_YoY, "0.00%") & ")",
        "No Data")

### 20. Track Frequency
```DAX
Track Frequency = COUNTROWS(spotify_history)

### 21. Track Frequency (Parameter) Value
```DAX
Track Freaquency (Parameter) Value = SELECTEDVALUE('Track Freaquency (Parameter)'[Track Freaquency (Parameter)])
