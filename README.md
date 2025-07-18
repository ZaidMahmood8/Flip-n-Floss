# Flip-n-Floss

# Flip & Floss Analytics Dashboard

## Table of Contents

- [Description](#description)  
- [Tech Assets and URLs](#tech-assets-and-urls)  
- [Installation](#installation)  
- [Prerequisites](#prerequisites)  
- [Usage](#usage)  
- [Features](#features)  
- [Data Sources](#data-sources)
- [Data Relationships](#data-relationships)
- [Troubleshooting](#troubleshooting)  
- [Contact Information](#contact-information)  
- [Dashboard Visual Guide](#dashboard-visual-guide)

---

## Description

The Flip & Floss dashboard is a student-centric analytics solution that provides interactive visuals and insights into user behavior, course progress, reward systems, and sign-up patterns. It is powered by Power BI and relies on multiple relational data sources.

---

## Tech Assets and URLs

- **Power BI Dashboard Link**: *[Insert URL here]*  
- **Data Sources**: Azure Database, Excel Imports  
- **Flag Assets**: Country Flag URLs (for slicers)

---

## Installation

1. Download Power BI Desktop  
2. Clone repository or acquire the `.pbix` file  
3. Load data sources (Azure DB or Excel)  
4. Refresh all datasets  
5. Configure credentials for gateway (if needed for refresh)

---

## Prerequisites

- Power BI Desktop  
- Admin access to data sources  
- Azure SQL or Excel data access  
- Internet access for live data

---

## Usage

Open the PBIX file in Power BI Desktop and interact with the dashboard using slicers.

### Example

To view reward trends for female students in 2023:  
1. Select **"Female"** from the Gender filter  
2. Select **"2023"** from the Year dropdown  
3. Navigate to **"Students by Reward Type"** visual

---

## Features

- Country filtering using image-based slicers  
- Student sign-up trend tracking  
- Reward system analysis  
- Course completion breakdown  
- Parent plan analysis  
- Geo-distribution of students  
- KPI cards for behavior metrics

---

## Data Sources

The data used for this dashboard was **simulated based on the structure and schema of the data the client intends to use in future production**. All tables were modeled to reflect real-world use cases, ensuring alignment with expected data flows and relationships.


- **students**: id, name, age, gender, wallet, classroom, parent_id, school, country, email, phone  
- **teachers**: id, fname, lname, email, phone, classroom, school  
- **parents**: id, fname, lname, plan, customer_id, email, phone  
- **transactions**: id, userid, amount, student, classroom, user_type, type, created_at  
- **course_progress**: id, userid, course_id, status, percentage, classroom, created_at, attempt  
- **courses**: id, title, reward_value, course_id, grade, category, status  
- **student_reward_purchases**: userid, reward, flip_dollars_spent, timestamp  

---

## Data Relationships


| From Table                 | From Column        | To Table                  | To Column          | Cardinality   | Direction | Active | Description                                          |
|---------------------------|--------------------|---------------------------|--------------------|---------------|-----------|--------|------------------------------------------------------|
| students                  | id                 | transactions              | student            | One-to-Many   | →         | Yes    | Connects students with their transaction records     |
| students                  | id                 | course_progress           | userid             | One-to-Many   | →         | Yes    | Maps course progress entries to students             |
| students                  | classroom          | transactions              | classroom          | One-to-Many   | →         | Yes    | Links student classroom to transactions              |
| students                  | classroom          | teachers                  | classroom          | One-to-Many   | →         | Yes    | Relates students to teachers by classroom            |
| students                  | classroom          | course_progress           | classroom          | One-to-Many   | →         | Yes    | Relates student classroom to course progress         |
| students                  | id                 | student_reward_purchases  | userid             | One-to-Many   | →         | Yes    | Active link from students to their reward purchases  |
| students                  | id                 | transactions              | userid             | One-to-Many   | →         | No     | Inactive: potential alternate link for user-based tx |
| students                  | id                 | course_progress           | parent_id          | One-to-Many   | →         | No     | Inactive: secondary mapping path in course progress  |
| student_reward_purchases | userid             | course_progress           | userid             | Many-to-One   | →         | No     | Inactive: potential reward-course association        |
| student_reward_purchases | userid             | transactions              | userid             | Many-to-One   | →         | No     | Inactive: potential link from rewards to tx          |
| transactions              | customer_id        | parents                   | customer_id        | Many-to-One   | →         | Yes    | Maps parent info for each transaction                |
| courses                   | course_id          | course_progress           | course_id          | One-to-Many   | →         | Yes    | Tracks course progress per course                    |


---

## Troubleshooting

- **Visuals not loading**: Ensure data refresh completes without errors  
- **Filters not responding**: Clear all slicers and retry  
- **Wrong values showing**: Revalidate source paths and data integrity

---

## Contact Information

**Name**: *Zaid Mahmood, Alexandra Guion*  
**Email**: *zaid.mahmood@mail.mcgill.ca, alexandra.guion@mail.mcgill.ca*  

---

## Dashboard Visual Guide

### Slicers and Filters

- **Country Selector**  
  - **Type**: Image Tile Slicer  
  - **Field**: `Country Flags[Country]`, `Country Flags[Flag URLs]`  
  - **Behavior**: Filters visuals based on selected country  

- **Year Selector**  
  - **Type**: Dropdown Slicer  
  - **Field**: `course_progress[created_at]` (Year)  
  - **Behavior**: Filters visuals for the selected year (e.g., 2023)  

- **Age Filter**  
  - **Type**: Range Slicer  
  - **Field**: `students[age]`  
  - **Behavior**: Filters visuals within age range (e.g., 10–18)  

- **Gender Filter**  
  - **Type**: Dropdown Slicer  
  - **Field**: `students[gender]`  
  - **Behavior**: Filters visuals by selected gender  

---

## Dashboard Visuals Overview

| Visual Name                      | Visual Type          | X-Axis / Category                     | Y-Axis / Value                                  | Legend / Group By                 | Filter Behavior         | Notes                                      |
|----------------------------------|----------------------|---------------------------------------|-------------------------------------------------|----------------------------------|--------------------------|---------------------------------------------|
| Number of Students               | Gauge                | —                                     | DISTINCT COUNT(students[id])                    | —                                | All slicers apply       | Shows total students vs. threshold          |
| Patience Index                   | Card (KPI)           | —                                     | Custom DAX Measure                              | —                                | All filters apply       | Behavioral KPI based on user interaction    |
| Account Creation Tracker         | Line Chart           | Month (from students[created_at])     | DISTINCT COUNT(students[id])                    | —                                | Year, others            | Monthly sign-up trend                       |
| Number of Students by Area       | Map                  | students[country]                     | DISTINCT COUNT(students[id])                    | —                                | All slicers apply       | Geo-distribution of student base            |
| Students by Course Status        | Clustered Bar Chart  | courses[title]                        | DISTINCT COUNT(course_progress[id])             | course_progress[status]          | Course status filters   | Status-wise course completion               |
| Completed Courses by Parent Plan | Clustered Column     | parents[plan]                         | DISTINCT COUNT(course_progress[id])             | —                                | Filtered for completed  | Uses CALCULATE to filter completed only     |
| Students by Reward Type          | Treemap              | student_reward_purchases[reward]      | DISTINCT COUNT(student_reward_purchases[userid])| —                                | Minimal                 | Frequency of reward claims                  |

---
