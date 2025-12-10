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

![Spotify Overview Dashboard](https://github.com/manishalinguntla2808/Spotify_PowerBI_Analysis/blob/main/Overview.png)

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

![Listening Heatmap](https://github.com/manishalinguntla2808/Spotify_PowerBI_Analysis/blob/main/Listening%20Times%20Heat%20Map.png)

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

![Track Engagement Scatter](https://github.com/manishalinguntla2808/Spotify_PowerBI_Analysis/blob/main/LIstening%20Times%20Scatter%20Plot.png)

This visual analyzes **track-level engagement** based on frequency and depth of listening.

| Quadrant | Behavior Meaning |
|----------|------------------|
| High Frequency + High Time | Favorite songs |
| High Frequency + Low Time | Skipped/Previewed songs |
| Low Frequency + High Time | Occasionally played long tracks |
| Low Frequency + Low Time | Least engaging tracks |

---

### 🔹 Detailed Track Table

![Detailed Table](https://github.com/manishalinguntla2808/Spotify_PowerBI_Analysis/blob/main/Details.png)

This visual provides raw-level exploration of:

- Album Details
- Artist Details
- Track-Level Metrics
- Listening Duration

---

## 🧮 DAX Measures & Their Purpose

In Power BI, **measures** are DAX (Data Analysis Expressions) calculations that perform dynamic computations based on the user’s current filters and selections.  
These measures power the calculations behind charts, KPIs, and interactive visuals in this dashboard.

Below are the key measures used in this project, along with their purpose and usage.

### 1. Total Listening Minutes

```DAX
Listening Time(min) Value = SELECTEDVALUE('Listening Time (min)'[Listening Time(min)])
```
### 2. Avg Listening time
```DAX
Avg Listening time = AVERAGE(spotify_history[ms_played])/ 60000
```
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
```
### 4. LatestYearAlbums
```DAX
LatestYearAlbums = 
var _LatestYear = MAX('Date Table'[Year])
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[album_name]), 'Date Table'[Year] = _LatestYear)
```
### 5. LatestYearArtists
```DAX
LatestYearArtists = 
var _LatestYear = MAX('Date Table'[Year])
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[artist_name]), 'Date Table'[Year] = _LatestYear)
```
### 6. LatestYearTracks
```DAX
LatestYearTracks = 
var _LatestYear = MAX('Date Table'[Year])
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[track_name]), 'Date Table'[Year] = _LatestYear)
```
### 7. Max Year
```DAX
Max Year = MAX('Date Table'[Year])
```
### 8. MinMax Albums Line Chart 
```DAX
MinMax Albums Line Chart = 
var _MaxValue = MAXX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[album_name])))
var _MinValue = MINX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[album_name])))
var _CurrentValue = DISTINCTCOUNT(spotify_history[album_name])
RETURN
    IF(_CurrentValue = _MaxValue || _CurrentValue = _MinValue, _CurrentValue, BLANK())
```
### 9. MinMax Artists Line Chart
```DAX
MinMax Artists Line Chart = 
var _MaxValue = MAXX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[artist_name])))
var _MinValue = MINX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[artist_name])))
var _CurrentValue = DISTINCTCOUNT(spotify_history[artist_name])
RETURN
    IF(_CurrentValue = _MaxValue || _CurrentValue = _MinValue, _CurrentValue, BLANK())
```
### 10. MinMax Tracks Line Chart
```DAX
MinMax Tracks Line Chart = 
var _MaxValue = MAXX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[track_name])))
var _MinValue = MINX(ALLSELECTED('Date Table'[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[track_name])))
var _CurrentValue = DISTINCTCOUNT(spotify_history[track_name])
RETURN
    IF(_CurrentValue = _MaxValue || _CurrentValue = _MinValue, _CurrentValue, BLANK())
```
### 11. No of Albums
```DAX
No of Albums = DISTINCTCOUNT(spotify_history[album_name])
```
### 12. No of Artists
```DAX
No of Artists = DISTINCTCOUNT(spotify_history[artist_name])
```
### 13. No of Tracks
```DAX
No of Tracks = DISTINCTCOUNT(spotify_history[track_name])
```
### 14. PreviousYearAlbums
```DAX
PreviousYearAlbums = 
var _LatestYear = MAX('Date Table'[Year])
var _PreviousYear = _LatestYear -1
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[album_name]), 'Date Table'[Year] = _PreviousYear)
```
### 15. PreviousYearArtists
```DAX
PreviousYearArtists = 
var _LatestYear = MAX('Date Table'[Year])
var _PreviousYear = _LatestYear -1
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[artist_name]), 'Date Table'[Year] = _PreviousYear)
```
### 16. PreviousYearTracks
```DAX
PreviousYearTracks = 
var _LatestYear = MAX('Date Table'[Year])
var _PreviousYear = _LatestYear -1
RETURN
    CALCULATE(DISTINCTCOUNT(spotify_history[track_name]), 'Date Table'[Year] = _PreviousYear)
```
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
```
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
```
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
```
### 20. Track Frequency
```DAX
Track Frequency = COUNTROWS(spotify_history)
```
### 21. Track Frequency (Parameter) Value
```DAX
Track Freaquency (Parameter) Value = SELECTEDVALUE('Track Freaquency (Parameter)'[Track Freaquency (Parameter)])
```

---

## 🎛 Dashboard Features

The Power BI dashboard includes rich interactive features that improve user experience and data exploration:

- **Year Filters**  
  Allows users to filter the visuals by specific years to analyze trends over time.

- **Platform Filters**  
  Enables analysis based on listening platforms such as Android, iOS, or Web.

- **Shuffle and Skipped Filters**  
  Helps understand user behavior by filtering tracks that were shuffled or skipped.

- **Cross-Visual Interactions**  
  Selecting a data point in one visual dynamically highlights and filters related data in other visuals.

- **Drill-through Reports**  
  Users can right-click and drill through from summary visuals to detailed pages for deeper track-level insights.

---

## ⚙️ Report Parameters

This dashboard uses **dynamic parameters** to give users more control over how the data is analyzed and visualized.

### 1. Track Frequency (Parameter)

This parameter allows users to control the **threshold for track play counts** used in the analysis.

**What it does:**
- Defines what is considered a **“high frequency”** track.
- Used to dynamically adjust the **quadrants in the scatter plot**.
- Helps users customize how strict or flexible the analysis should be.

**Where it is used:**
- Scatter chart (Track Engagement Analysis)
- Dynamic quadrant segmentation

---

### 2. Listening Time (min) (Parameter)

This parameter allows users to control the **listening time threshold (in minutes)** that determines engagement levels.

**What it does:**
- Defines what is considered **“long listening time”** vs short listening time.
- Used to dynamically adjust the X-axis behavior in the scatter plot.
- Helps users personalize engagement classification.

**Where it is used:**
- Scatter chart (Avg Listening Time vs Track Frequency)
- Interactive threshold controls in the report

---

## 📈 Insights Discovered

This Power BI dashboard uncovers meaningful behavioral and listening insights from Spotify data spanning **2013 to 2024**. The analysis transforms raw listening history into actionable and easy-to-understand patterns.

### 🎵 1. Favorite Tracks and Artists

The dashboard clearly identifies:
- Most frequently played tracks
- Top-performing artists
- Most listened albums

**Key Insights:**
- Users tend to develop long-term loyalty towards a small group of artists.
- Repeat listening behavior shows strong emotional or productivity-driven engagement with certain tracks.
- Favorite tracks often correlate with higher average listening times, indicating deeper engagement.

---

### ⏰ 2. Listening Behavior by Time of Day

The **hourly heatmap** reveals how listening patterns change throughout the day.

**Key Insights:**
- Peak listening hours typically occur in the **evening and late night**.
- Morning listening tends to be shorter and more selective.
- Music is often used for relaxation, focus, or stress relief after work or study hours.

---

### 📅 3. Weekday vs Weekend Behavior

The dashboard separates **weekday and weekend patterns** to understand lifestyle-based listening habits.

**Key Insights:**
- Weekends show longer average listening durations.
- Weekdays show more frequent but shorter listening sessions.
- Weekend listening behavior indicates more relaxed and exploratory music choices.

---

### 📊 4. Long-Term Trend Analysis (2013–2024)

The time-series visuals highlight how listening behavior evolves over the years.

**Key Insights:**
- A steady growth in variety of tracks and artists over time.
- Shifts in genre preferences across different time periods.
- Increased total listening time in recent years, showing deeper platform usage.

---

### ⏭ 5. Skipping Behavior Analysis

Using shuffle and skipped indicators, the dashboard analyzes how often users skip tracks.

**Key Insights:**
- High skip rates correlate with shorter track durations and unfamiliar artists.
- Favorite tracks show significantly lower skip behavior.
- Frequent skipping typically happens during discovery sessions or shuffled playback.

---

### 🧠 6. Track Engagement Segmentation

The scatter plot divides tracks into behavioral segments.

| Segment | Insight |
|--------|---------|
| High Frequency + High Time | Core favorite tracks |
| High Frequency + Low Time | Habitual but non-engaging tracks |
| Low Frequency + High Time | Niche or special tracks |
| Low Frequency + Low Time | Low-interest content |

**Key Insights:**
- Core favorites form a small but powerful portion of listening behavior.
- Some tracks show curiosity-based listening (long time but low frequency).
- Low engagement tracks suggest content that struggled to connect.

---

### 🎯 7. Personalization and Optimization Opportunities

This dashboard helps identify opportunities for:
- Smarter playlist creation
- Better music discovery habits
- Reduction of unwanted skips
- Improved personalization strategy

---

## ✅ Business and Personal Value

This project demonstrates how personal data can be transformed into:

- Behavior-driven insights  
- Personalized content strategies  
- Data-driven decision-making  

It showcases strong skills in:
- Data modeling
- DAX calculations
- Visual storytelling
- User-centric dashboard design

---

## Final Conclusions

This Spotify Analysis project provides a comprehensive overview of trends and patterns in Spotify data from 2013 to 2024. Key insights from this analysis include:

- **Trend Analysis:** The popularity of different music genres and artists has evolved significantly over the years, reflecting changing user preferences and global music trends.  
- **User Behavior:** Analysis of plays, streams, and releases reveals seasonal and yearly variations in user engagement.  
- **Top Performers:** Certain artists and tracks consistently dominate the charts, highlighting their influence and sustained popularity over the last decade.  
- **Data Insights:** Patterns in streaming behavior, such as peak listening times and preferred genres, can guide music marketing strategies and content recommendations.  

---

## Author

**Manisha Linguntla**  
- [GitHub Profile](https://github.com/manishalinguntla2808)  
- [LinkedIn](https://www.linkedin.com/in/manisha-linguntla/)  

---

**License:**  
This project is for educational purposes and personal portfolio use.
