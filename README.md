# Netflix Analysis — Viewer & Content Insights (Power BI)

[![Power BI](https://img.shields.io/badge/Tool-Power%20BI-blue)](#)  
**Author:** B. Nagaraj (Jeeva) · **Contact:** nagarajjeeva99@gmail.com

---

## Project overview
An interactive **Power BI** report analyzing Netflix-style viewing data to surface viewer behavior, content performance, and subscription trends. The report includes KPI cards, time-series trendlines, content/category breakdowns, top titles, and filters for date, country and subscription type — designed for product and content teams to quickly spot viewing patterns and make data-driven decisions.

---

## Preview / Screenshots

Add screenshots to an `images/` folder in your repo so they render on GitHub (update names if you rename files):

- `images/Netflix_Screenshot_1.png`  
- `images/Netflix_Screenshot_2.png`  
- `images/Netflix_Screenshot_3.png`

<img width="1303" height="730" alt="Screenshot 2025-10-03 211838" src="https://github.com/user-attachments/assets/870304c1-2a43-450e-9652-189b59cd3bc0" />

<img width="1306" height="724" alt="Screenshot 2025-10-03 211855" src="https://github.com/user-attachments/assets/ab68a22a-9fe1-4694-8e0f-403d3a3d9fea" />

<img width="1308" height="719" alt="Screenshot 2025-10-03 211917" src="https://github.com/user-attachments/assets/a7fd9da8-96aa-4fc6-a05a-7cf467d6bdd5" />


---

## Key features
- KPI cards: Total Watch Time, Active Users, Avg Watch Time per User, Avg Rating.  
- Time-series charts: Daily / Monthly viewing trends and subscriber growth.  
- Content performance: Top titles by watch time, watch count, and average rating.  
- Category / Genre breakdowns: Horizontal bar charts and stacked visuals.  
- Filters & slicers: Date range, Country, Subscription Type, Genre.  
- Sample DAX measures for aggregated analytics (ratings, watch time, conversions).

---

## Data model (recommended)
Use a star schema for best performance:

**Fact table: `Viewing`**  
- ViewingID (PK)  
- UserID (FK)  
- ContentID (FK)  
- Date (date)  
- DurationMinutes (numeric) — viewing length per session  
- WatchCount (numeric) — number of times watched (or 1 per session)  
- Rating (numeric, nullable)  
- Country (string)  
- SubscriptionType (string) — e.g., Basic, Standard, Premium  
- Revenue (numeric, optional) — if modeling revenue per subscription/region

**Dimension tables:**  
- `Users` (UserID, JoinDate, Country, AgeGroup, Gender, SubscriptionType, ...)  
- `Content` (ContentID, Title, Genre, Type (Movie/Series), ReleaseYear, Duration)  
- `Date` (Date, Year, MonthNumber, MonthName, Quarter, WeekOfYear, DayOfWeek)

---

## Sample DAX measures

Copy these example measures into Power BI and adjust column/table names as required:

```dax
-- Total Watch Time (minutes)
Total Watch Time = SUM(Viewing[DurationMinutes])

-- Active Users (distinct users in current filter)
Active Users = DISTINCTCOUNT(Viewing[UserID])

-- Average Watch Time per Active User
Avg Watch Time per User = 
DIVIDE([Total Watch Time], [Active Users], 0)

-- Average Rating (ignore blanks)
Avg Rating = AVERAGEX(FILTER(Viewing, NOT(ISBLANK(Viewing[Rating]))), Viewing[Rating])

-- Top Title Watch Time (example measure for tooltip)
WatchTime by Title = CALCULATE([Total Watch Time], ALLSELECTED(Content[Title]))

-- New Subscribers in Period (if Users[JoinDate] exists)
New Subscribers = 
CALCULATE(
    DISTINCTCOUNT(Users[UserID]),
    DATESBETWEEN(Date[Date], MIN(Date[Date]), MAX(Date[Date])),
    Users[JoinDate] >= MIN(Date[Date]) && Users[JoinDate] <= MAX(Date[Date])
)
