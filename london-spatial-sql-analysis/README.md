London Spatial SQL Analysis Portfolio

📌 Overview

This project showcases advanced SQL skills using spatial data analysis to explore geographic, demographic, and infrastructure patterns across London boroughs.

Using Spatial SQL (via SpatiaLite / PostGIS-style functions), the project answers real-world analytical questions such as:

Which boroughs are geographically extreme (north, south, east, west)?

How transport infrastructure (Underground stations, motorways) is distributed

Accessibility of public transport relative to population and area

Financial inclusion via credit union distribution



---

🛠️ Tools & Technologies

SQL (Advanced queries, joins, aggregations)

Spatial SQL (e.g., ST_Area, ST_Contains, ST_Distance, ST_Intersects)

SpatiaLite / QGIS

(Compatible with PostgreSQL + PostGIS)



---

📂 Dataset Description

The project uses multiple geospatial datasets, including:

London Borough boundaries

Inner vs Outer London classification

Underground station locations

Motorway networks

Credit union locations

Population data



---

🔍 Key Analyses & Tasks

1. 📍 Geospatial Extremes & Density Analysis

Created a working table (london_guinness_book)

Calculated:

Area (sq km)

Population density

Geographic boundaries (North, South, East, West)



✅ Key Insight:

Enfield = Northernmost

Croydon = Southernmost

Hillingdon = Westernmost

Havering = Easternmost



---

2. 🚇 Underground Accessibility Analysis

Counted number of stations and lines per borough

Normalised by:

Area (stations per sq km)

Population (stations per 1000 people)



✅ Key Insight:

Westminster has the highest station density (economic + political hub)

Outer boroughs have significantly lower accessibility



---

3. 💳 Credit Union Distribution (Financial Inclusion)

Mapped credit unions to boroughs using spatial joins


✅ Key Insight:

Hillingdon has the highest number due to expansion, mergers, and accessibility

Higher presence in lower-income boroughs (e.g., Tower Hamlets)



---

4. 🛣️ Motorway Connectivity

Identified boroughs intersected by major motorways


✅ Key Insight:

Hillingdon is a major transport hub (links to Heathrow & M4 corridor)



---

5. 📏 Transport Proximity Analysis

Measured distance between Underground stations and motorways

Categorised proximity into distance bands (≤1km, 1–2km, 2–3km)


✅ Key Insight:

Several stations lie within 1km of motorways → strong multimodal connectivity



---

⚙️ SQL Techniques Demonstrated

Complex JOIN operations (including spatial joins)

Nested subqueries & UNION

Aggregations with normalization

Spatial functions:

ST_Area

ST_Contains

ST_Distance

ST_Intersects


Query optimisation using:

Aliasing

CASE statements

Cleaner ORDER BY usage




---

📈 Visualisation

Maps were generated to support analysis, including:

London borough boundaries

Transport networks (Underground & motorways)

Credit union locations

Population distribution


Tools like QGIS were used for spatial visualisation.


---

🚀 Key Takeaways

Spatial SQL enables powerful real-world geographic analysis

Infrastructure distribution is strongly linked to economic activity

Transport accessibility varies significantly between Inner and Outer London

Data-driven insights can support urban planning and financial inclusion strategies



---

🔮 Future Improvements

Migrate project to PostgreSQL + PostGIS for scalability

Build interactive dashboards (e.g., Power BI / Tableau)

Add time-series analysis for population and transport changes

Optimise queries for performance on large datasets



---

👤 Author

David Uchenna Asuzor
SQL & Data Analytics Portfolio

