# Data Analyst Job Market Analysis 📊

## 📌 Project Overview
Welcome to my SQL Portfolio Project! This project is a data-driven deep dive into the data analyst job market, focusing on analyzing job postings from the first quarter of 2023. The primary goal is to uncover actionable insights regarding top-paying roles, the most in-demand skills, and the optimal skills that combine high market demand with lucrative salaries.

This project demonstrates advanced SQL techniques used to solve real-world business and data architecture questions, utilizing a dataset representing thousands of real job postings.

## 🧰 Tools & Technologies Used
- **Database Management System:** PostgreSQL
- **IDE:** VS Code (with SQLTools extension)
- **Version Control:** Git & GitHub
- **Language:** SQL (PostgreSQL dialect)

## 🔍 Key Questions Explored
1. **Top-Paying Data Analyst Roles:** What are the highest-paying job postings for Data Analysts in Q1?
2. **Skills for High-Paying Jobs:** Which specific skills do employers look for in these elite, high-salary positions?
3. **In-Demand Skills:** What are the most frequently requested skills for remote Data Analysts?
4. **Top-Paying Skills:** Which skills command the highest average annual salaries?
5. **Optimal Skills to Learn:** Which skills lie at the intersection of high demand and high pay (the "sweet spot" for career growth)?

## 💡 Advanced SQL Concepts Demonstrated
- **Common Table Expressions (CTEs) & Subqueries:** Used to isolate complex logical steps and organize multiple layers of data consolidation.
- **UNION ALL Operator:** Dynamically combined large, multi-table monthly datasets (`january_jobs`, `february_jobs`, `march_jobs`) into unified structures.
- **Advanced Data Transformations:** Utilized explicit data type casting (`::DATE`) to clean timestamps into neat, readable date formats.
- **Multi-Table Joins:** Implemented complex `INNER JOIN` and `LEFT JOIN` structures to connect fact tables (`job_postings_fact`) with skill dimensions while preserving listings without associated skills.
- **Aggregations & Statistical Rounding:** Applied `COUNT()`, `AVG()`, and `ROUND()` functions to extract meaningful metrics across groupings.

## 📂 Project Structure & Queries
The repository is organized into structured query scripts answering specific analytical goals:

### 1. Combining Q1 Data & Filtering for High Salaries
Consolidates job postings across January, February, and March for Data Analyst roles offering a yearly salary greater than $70,000.
```sql
SELECT
    q1_jobs.job_title_short,
    q1_jobs.job_location,
    q1_jobs.job_via,
    q1_jobs.job_posted_date::DATE,
    q1_jobs.salary_year_avg
FROM (
    SELECT * FROM january_jobs
    UNION ALL
    SELECT * FROM february_jobs
    UNION ALL
    SELECT * FROM march_jobs
) AS q1_jobs
WHERE 
    salary_year_avg > 70000 AND
    job_title_short = 'Data Analyst'
ORDER BY
    salary_year_avg DESC;
```

### 2. Identifying Optimal Skills (High Demand + High Pay)
Uncovers the top 25 skills for remote Data Analysts by simultaneously examining popularity (demand count) and compensation (average salary) to find optimal skills.
```sql
SELECT 
    skills_dim.skill_id,
    skills_dim.skills AS skill_name,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
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
ORDER BY 
    demand_count DESC, 
    avg_salary DESC
LIMIT 25;
```

## 📈 Key Insights & Findings
- **The Core Stack:** Standard data tools like **SQL** and **Python** continue to dominate total market demand for remote Data Analyst roles, making them essential foundational skills.
- **Premium Compensation:** Specialized skills in big data tools, cloud platforms, and advanced analytical frameworks command substantial salary premiums, even if total job posting counts are lower.
- **The Remote Advantage:** Filtering for work-from-home positions reveals a highly competitive but geographically diverse market, where clarity of tech stack alignment is critical for applicants.

## 🚀 How to Run this Project
1. Clone this repository to your local system:
   ```bash
   git clone https://github.com/MahmoudOG54/Sql_project.git
   ```
2. Ensure you have **PostgreSQL** installed and running locally.
3. Load the raw dataset CSV files into your database server.
4. Open the project folder in **VS Code**, connect via **SQLTools**, and execute any `.sql` script to explore the data!

---
*Developed as part of my Data Analysis portfolio. Connect with me on GitHub to follow my progress!*
