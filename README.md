# Data Analyst Job Market Analysis 📊

## 📌 Project Overview
Welcome to my SQL Portfolio Project! This project is a data-driven deep dive into the data analyst job market. Using a dataset of thousands of real job postings from Luke Barousse's course, this project analyzes remote and global opportunities to uncover **top-paying roles**, **the most in-demand skills**, and **the optimal skills** that sit at the perfect intersection of high demand and high salary.

The goal is to provide actionable insights for anyone looking to enter or advance in the Data Analytics field by demonstrating advanced SQL querying techniques.

## 🧰 Tools & Technologies Used
- **Database Management System:** PostgreSQL
- **IDE:** VS Code (with SQLTools extension)
- **Version Control:** Git & GitHub
- **Language:** SQL (PostgreSQL dialect)

## 🔍 The 5 Core Questions Addressed
Here are the specific questions answered through dedicated SQL scripts in this repository:
1. **What are the top-paying Data Analyst roles?** (`1_top_paying_jobs.sql`)
2. **What skills are required for these top-paying jobs?** (`2_top_paying_job_skills.sql`)
3. **What are the most in-demand skills for remote Data Analysts?** (`3_top_demand_skill.sql`)
4. **What are the top-paying skills based on average salary?** (`4_top_paying_skills.sql`)
5. **What are the most optimal skills to learn (High Demand + High Salary)?** (`5_optimal_skills.sql`)

---

## 📂 Project Structure & Queries

### 1. Top-Paying Data Analyst Jobs (`1_top_paying_jobs.sql`)
Identifies the top 10 highest-paying remote Data Analyst roles that have specified salaries, along with their respective company names.
```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND 
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
```

### 2. Skills Required for Top-Paying Jobs (`2_top_paying_job_skills.sql`)
Finds which specific skills are mapped to the top 10 highest-paying remote Data Analyst positions discovered in the first query.
```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
        LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' AND 
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)
SELECT 
    top_paying_jobs.*,
    skills_dim.skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```

### 3. Top In-Demand Skills (`3_top_demand_skill.sql`)
Isolates the top 5 most frequently requested skills across all remote Data Analyst job postings to show what tools the market craves most.
```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Analyst' AND
    job_work_from_home = TRUE
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```

### 4. Top-Paying Skills (`4_top_paying_skills.sql`)
Explores the top 50 highest-paying skills based on their average annual salary, revealing what niche specialized expertise commands premium financial reward.
```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS Avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Analyst' AND
    salary_year_avg IS NOT NULL 
    --job_work_from_home = TRUE
GROUP BY skills
ORDER BY Avg_salary DESC
LIMIT 50;
```

### 5. Optimal Skills to Learn (`5_optimal_skills.sql`)
Combines insights from both demand and salary to extract the top 25 optimal skills for remote data analysts, identifying high-value tech stacks that are both highly sought after and high-paying.
```sql
WITH skills_demand AS (
    SELECT 
        skills_dim.skill_id, 
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE 
        job_title_short = 'Data Analyst' AND
        job_work_from_home = TRUE AND
        salary_year_avg IS NOT NULL 
    GROUP BY 
        skills_dim.skill_id, 
        skills_dim.skills
), avg_salary AS (
    SELECT 
        skills_dim.skill_id,
        skills_dim.skills,
        ROUND(AVG(salary_year_avg), 0) AS main_avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE 
        job_title_short = 'Data Analyst' AND
        salary_year_avg IS NOT NULL AND
        job_work_from_home = TRUE
    GROUP BY 
        skills_dim.skill_id,
        skills_dim.skills
)
SELECT 
    skills_demand.skill_id,
    skills_demand.skills AS skill_name, 
    skills_demand.demand_count,
    avg_salary.main_avg_salary 
FROM skills_demand
INNER JOIN avg_salary ON skills_demand.skill_id = avg_salary.skill_id
ORDER BY 
    demand_count DESC, 
    main_avg_salary DESC
LIMIT 25;
```

---

## 📈 Key Insights & Findings
- **The Undisputed Core:** Core tools like **SQL**, **Python**, and Excel/Tableau dominate market volume, making them strict prerequisites for any data analysis application.
- **Niche/Cloud Premium:** Specialized tools in cloud platforms (like AWS/GCP), DevOps, or advanced data architecture command sky-high salaries because the supply of talent is low.
- **Strategy for Seekers:** To maximize career ROI, focusing on "Optimal Skills" (like SQL, Python, R, and specific BI tools) guarantees that you are building skills that are highly relevant to employers *and* highly rewarded.

## 🚀 How to Run this Project
1. Clone this repository:
   ```bash
   git clone https://github.com/MahmoudOG54/Sql_project.git
   ```
2. Ensure you have **PostgreSQL** installed and running locally.
3. Import the dataset CSV files into your database server.
4. Open the project folder in **VS Code**, connect via **SQLTools**, and execute any `.sql` script to explore the analytics!

---
*Developed as part of my Data Analysis portfolio. Connect with me on GitHub to follow my progress!*