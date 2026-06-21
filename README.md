# Project-SQL11
SQL Analysis on Jobs
# Data Analyst Job Market Analysis – SQL Portfolio Project

## Project Overview
This end-to-end SQL portfolio project explores the data job market with a special focus on **Data Analyst** roles. The primary goal was to answer practical career questions: What are the highest paying opportunities? Which skills are most in demand? And most importantly — which skills offer the **best combination of high demand and high salary**?

By working exclusively with SQL on real job posting data, I demonstrated the ability to turn raw data into clear, actionable business insights. This project showcases clean query writing, logical problem-solving, and the capability to derive meaningful recommendations that job seekers and hiring managers care about.

## Dataset
- **Source**: Luke Barousse SQL Course (2024–2025 data)
- **Main Tables**: `job_postings_fact`, `skills_job_dim`, `skills_dim`
- **Scope**: Focus on roles with salary information, particularly Data Analyst positions

---

### 1. Top Paying Jobs
```sql
-- Top Paying Jobs
SELECT DISTINCT job_id, job_title, salary_year_avg
FROM job_postings_fact
WHERE salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 20;


This foundational query extracts the 20 highest-paying job postings across all data-related roles. Using DISTINCT, ORDER BY, and LIMIT, it quickly identifies the salary ceiling in the market. The results typically show senior titles such as Director of Analytics, Head of Data, and Data Scientist roles leading the list. This step sets the context for understanding what “good” compensation looks like before narrowing the focus to Data Analyst roles.




-- Top Paying Jobs and Skills
WITH top_paying_jobs AS (
    SELECT DISTINCT job_id, job_title, salary_year_avg
    FROM job_postings_fact
    WHERE salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 20
)
SELECT 
    tp.job_id,
    tp.job_title,
    s.skills
FROM top_paying_jobs tp
LEFT JOIN skills_job_dim sj ON tp.job_id = sj.job_id
LEFT JOIN skills_dim s ON sj.skill_id = s.skill_id
WHERE s.skills IS NOT NULL;



This query builds directly on the first one using a Common Table Expression (CTE). It joins the top-paying jobs with their associated skills through two bridge tables. The result reveals which technical skills appear most often in the highest-paying roles (Python, SQL, AWS, Spark, etc.). This bridges salary data with skill requirements and shows what companies are willing to pay a premium for.



-- Skills in Demand for Data Analyst (> $70k)
WITH skills_demand AS (
    SELECT *
    FROM job_postings_fact
    WHERE job_title_short = 'Data Analyst' 
      AND salary_year_avg > 70000
)
SELECT
    skills,
    COUNT(*) AS demand_count,
    CASE WHEN COUNT(*) >= 1000 THEN 'High Demand' ELSE 'Medium Demand' END AS demand_level
FROM skills_demand
LEFT JOIN skills_job_dim ON skills_demand.job_id = skills_job_dim.job_id
LEFT JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE skills IS NOT NULL
GROUP BY skills
ORDER BY demand_count DESC;

This analysis narrows the scope to Data Analyst positions paying above $70,000. Through multiple LEFT JOINs, grouping, and aggregation, it ranks skills by how frequently they appear in job postings. The CASE statement adds a demand category. Results usually show SQL, Excel, Tableau, Power BI, and Python dominating the list.




-- Top Skills by Average Salary (Data Analyst)
WITH top_skills_based_on_salary AS (
    SELECT *
    FROM job_postings_fact
    LEFT JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    LEFT JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Analyst'
      AND salary_year_avg > 70000
)
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary,
    COUNT(*) AS demand_count
FROM top_skills_based_on_salary
WHERE skills IS NOT NULL
GROUP BY skills
ORDER BY avg_salary DESC;



Moving beyond frequency, this query calculates the average salary associated with each skill for Data Analyst roles. It reveals which skills have the strongest correlation with higher pay. This is especially useful because some commonly requested skills (like Excel) may not pay as well as less common ones (like certain cloud or programming tools).




-- Most Optimal Skills (High Demand + High Paying)
WITH optimal_skills AS (
    SELECT
        skills,
        COUNT(*) AS demand_count,
        AVG(salary_year_avg) AS avg_salary
    FROM job_postings_fact
    LEFT JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    LEFT JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Analyst'
      AND salary_year_avg > 70000
      AND salary_year_avg IS NOT NULL
      AND skills IS NOT NULL
    GROUP BY skills
)
SELECT 
    skills,
    demand_count,
    ROUND(avg_salary, 0) AS avg_salary
FROM optimal_skills
WHERE demand_count >= 1000 
  AND avg_salary >= 70000
ORDER BY demand_count DESC;


The capstone query combines demand volume and salary level in one CTE to identify the most optimal skills — those that are both highly requested and associated with strong compensation. This is the most practical output of the entire project, giving clear learning priorities for anyone wanting to maximize their career growth as a Data Analyst.




Key Insights
-SQL is consistently the most demanded foundational skill.
-Python and cloud tools often appear in higher-paying roles.
-There is a noticeable gap between “commonly requested” and “highest paying” skills.

Conclusion
This project proves that strong SQL skills alone can deliver high-value career insights. By methodically exploring the data from broad salary trends down to specific skill-level recommendations, I transformed raw job posting data into clear, actionable intelligence.The combination of CTEs, multi-table joins, aggregations, conditional logic, and strategic filtering used throughout this analysis reflects real-world expectations for junior Data Analysts. This portfolio project is a strong demonstration of my technical SQL abilities and business thinking — ready for entry-level Data Analyst roles.


Skills Demonstrated
-Advanced SQL (CTEs, Joins, Aggregations, CASE statements)
-Analytical problem-solving
-Insight generation and storytelling with data
-Career-focused data analysis

