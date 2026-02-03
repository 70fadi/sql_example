# Introduction
Dive into the data job market! Focusing on data analyst roles, this project explores & top-paying jobs, in-demand skills, and where high demand meets high salary in data analytics.
SQL queries? Check them out here: [project_sql folder](/project_sql/) 
# Background
Driven by a quest to navigate the data analyst job market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.
Data hails from this [SQL Course] (https://lukebarousse.com/sql). It's packed with insights on job titles, salaries, locations, and essential skills.
### The questions I wanted to answer through my SQL queries were:
1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?
# Tools I Used
For my deep dive into the data analyst job market, I harnessed the power of several key tools:
- SQL: The backbone of my analysis, allowing me to query the database and unearth critical insights.
PostgreSQL: The chosen database management -system, ideal for handling the job posting data.
Visual Studio Code: My go-to for database -management and executing SQL queries.
- Git & GitHub: Essential for version control and -sharing my SQL scripts and analysis, ensuring collaboration and project tracking.
# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market.
Here's how I approached each question:

### 1- top paying jobs 

To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title, 
    job_location, 
    job_schedule_type, 
    salary_year_avg, 
    job_posted_date::date,
    name as company_name
FROM
    job_postings_fact 
left JOIN company_dim on job_postings_fact.company_id = company_dim.company_id
where job_title_short = 'Data Analyst' 
and job_location = 'London, UK'
and salary_year_avg is not NULL
ORDER BY salary_year_avg DESC
LIMIT 10 
```


Breakdown of Top Data Analyst–Related Roles in London (2023)

**Strong Salary Potential:**
The highest-paying roles range from approximately $100,500 to $177,283 annually, with research-focused and data architecture positions commanding the top end of the salary spectrum.

**Concentration of High-Value Employers:**
Leading organizations such as DeepMind, Logispin, AccorCorpo, and Ocorian appear among the top-paying roles, highlighting strong demand for data and analytics talent across technology, research, and enterprise sectors.

**Diverse Role Specializations:**
Job titles span from Data Analyst and Business Intelligence Analyst to Analytics Engineer, Data Architect, and Research Scientist, demonstrating the breadth of career paths and specializations within the data analytics field.



### 2- top_paying_job_skills

To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title, 
        salary_year_avg, 
        name as company_name
    FROM
        job_postings_fact 
    left JOIN company_dim on job_postings_fact.company_id = company_dim.company_id
    where job_title_short = 'Data Analyst' 
    and job_location = 'London, UK'
    and salary_year_avg is not NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10 
)

    SELECT 
        top_paying_jobs.*,
        skills_job_dim.skill_id,
        skills_dim.skills as skill_name
    from top_paying_jobs
    INNER JOIN skills_job_dim on top_paying_jobs.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim on skills_dim.skill_id = skills_job_dim.skill_id
    ORDER BY 
        salary_year_avg DESC
    LIMIT 10;
```

Here’s a snapshot of the key skills associated with some of the highest-paying data and research roles:

- Machine Learning & Research Skills Drive Top Salaries:
The highest average salary ($177,283) is associated with a Research Engineer role at DeepMind, requiring advanced skills such as Python, C++, Pandas, NumPy, TensorFlow, and PyTorch. This concentration of machine learning and scientific computing tools highlights the strong market premium for AI-focused expertise.

- Data Architecture & Cloud Proficiency Remain Highly Valued:
The Data Architect role offers a competitive salary ($163,782) and emphasizes skills like NoSQL, Azure, and Looker, underscoring the importance of scalable data infrastructure, cloud platforms, and analytics visualization in senior data engineering roles.

- Analytics Engineering Relies on Core Querying Skills:
The Analytics Engineer position, with an average salary of $139,216, centers on SQL, reinforcing its continued importance as a foundational skill for transforming and analyzing data within production environments.

- Skill Clustering by Role Type:
The data shows a clear separation between research-oriented roles, which demand advanced programming and ML frameworks, and engineering/analytics roles, which prioritize data architecture, cloud services, and querying capabilities—each with distinct salary tiers.


### 3- Demanded_skills
This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql 
SELECT
    skills_dim.skills,
     count(skills_job_dim.job_id) AS Job_count
from job_postings_fact
INNER JOIN skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim on skills_dim.skill_id = skills_job_dim.skill_id
WHERE 
    job_title_short ='Data Analyst' AND
    job_location = 'London, UK'
GROUP BY
    skills
ORDER BY
    Job_count DESC
LIMIT 5;
```

Here's the breakdown of the most demanded skills for data analysts in 2023

SQL and Excel remain fundamental, emphasizing the need for strong foundational skills in data processing and spreadsheet manipulation.
Programming and Visualization Tools like Python, Tableau, and Power BI are essential, pointing towards the increasing importance of technical skills in data storytelling and decision support.

## Skill Demand Count

| Skill     | Demand Count |
|-----------|--------------|
| SQL       | 182        |
|Python  | 152        |
| Excel    | 98       |
| Tableau   | 72        |
| Power BI  | 67        |



Table of the demand for the top 5 skills in data analyst job postings


### 4. Skills Based on Salary

Exploring the average salaries associated with different skills revealed which skills are the highest paying

```sql 
SELECT
    skills_dim.skills,
    ROUND(avg(salary_year_avg),2) as avg_salary
from job_postings_fact
INNER JOIN skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim on skills_dim.skill_id = skills_job_dim.skill_id
WHERE 
    job_title_short ='Data Analyst' AND
    job_location = 'London, UK' AND
    salary_year_avg is not NULL
GROUP BY
   skills
ORDER BY
    avg_salary DESC
LIMIT 25;
```

- Strong Premium on Data Science & AI Foundations:
The highest average salaries are associated with core data science and AI skills such as Pandas, NumPy, PyTorch, TensorFlow, and C++, highlighting the strong market demand for professionals who can build, optimize, and scale machine learning models and data-driven systems.

- Programming & Advanced Analytics Expertise:
Languages and tools like Python, JavaScript, MATLAB, and SQL continue to command competitive salaries, reflecting their critical role in data analysis, statistical modeling, and end-to-end analytical workflows across industries.

- Business Intelligence & Enterprise Tooling Value:
Proficiency in analytics and enterprise platforms—including Tableau, Power BI, DAX, Snowflake, Excel, and SharePoint—demonstrates solid earning potential, emphasizing the importance of translating complex data insights into actionable business intelligence within organizational environments.
## Skills Demand and Salary

## Average Salary by Skill

| Skill        | Average Salary ($) |
|--------------|--------------------|
| pandas       | 177,283.00         |
| tensorflow   | 177,283.00         |
| c++          | 177,283.00         |
| pytorch     | 177,283.00         |
| numpy        | 177,283.00         |
| nosql        | 163,782.00         |
| javascript   | 111,175.00         |
| python       | 110,353.43         |
| powerpoint   | 105,837.50         |
| azure        | 105,210.67         |

Table of the average salary for the top 10 paying skills for data analysts

### 5- Most optimal skills to learn

Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skill development.

```sql 
WITH skills_demand AS (
        SELECT
           skills_dim.skill_id,
           skills_dim.skills,
            count(skills_job_dim.job_id) AS demand_count
        from job_postings_fact
        INNER JOIN skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
        INNER JOIN skills_dim on skills_dim.skill_id = skills_job_dim.skill_id
        WHERE 
            job_title_short ='Data Analyst' AND
            job_work_from_home = TRUE AND
            salary_year_avg is not NULL 
        GROUP BY
         skills_dim.skill_id
), 

    average_salary AS (
        SELECT
            skills_job_dim.skill_id,
            ROUND(avg(salary_year_avg),0) as avg_salary
        from job_postings_fact
        INNER JOIN skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
        INNER JOIN skills_dim on skills_dim.skill_id = skills_job_dim.skill_id
        WHERE 
            job_title_short ='Data Analyst' AND
            job_work_from_home = TRUE AND
            salary_year_avg is not NULL
        GROUP BY
         skills_job_dim.skill_id
)

SELECT 
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
from 
    skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE
demand_count > 10
ORDER BY
    average_salary DESC,
    skills_demand DESC
LIMIT 25;
```


## Top 10 Skills by Listing Order

| Skill ID | Skill        | Demand Count | Average Salary ($) |
|---------:|--------------|--------------|--------------------|
| 234      | Confluence   | 11           | 114,210            |
| 233      | Jira         | 20           | 104,918            |
| 215      | Flow         | 28           | 97,200             |
| 201      | Alteryx      | 17           | 94,145             |
| 199      | SPSS         | 24           | 92,170             |
| 198      | Outlook      | 13           | 90,077             |
| 197      | SSRS         | 14           | 99,171             |
| 196      | PowerPoint   | 58           | 88,701             |
| 195      | SharePoint   | 18           | 81,634             |
| 194      | SSIS         | 12           | 106,683            |


- High Demand Does Not Always Equal Higher Pay:
Widely required tools such as Excel (256 postings), Tableau (230), and Power BI (110) show the highest demand but offer relatively moderate average salaries. This suggests these skills are foundational and expected, rather than strong salary differentiators.

- Specialized Data Engineering & Cloud Skills Command Premium Salaries:
Skills like Confluence ($114,210), Hadoop ($113,193), Snowflake ($112,948), AWS ($108,317), and BigQuery ($109,654) offer higher average salaries despite lower demand, indicating a premium for specialized, infrastructure-focused expertise.

- Business Intelligence Tools Balance Demand and Compensation:
BI and analytics platforms such as Looker, Qlik, SSIS, and Tableau strike a balance between demand and pay, making them valuable skills for analysts aiming to move into higher-paying roles without full engineering specialization.

- Enterprise & Productivity Tools Are Common but Lower Paying:
Skills like PowerPoint, Word, Outlook, and SharePoint appear frequently but correlate with lower average salaries, reinforcing their role as complementary rather than career-defining technical skills.

- Cloud Data Warehousing Skills Increase Earning Potential:
Modern cloud-based data platforms (Snowflake, Redshift, BigQuery) consistently rank among the higher-paying skills, highlighting the growing importance of scalable, cloud-native analytics environments.




# What I Learned

Throughout this adventure, I've turbocharged my SQL toolkit with some serious firepower:

🧩 **Complex Query Crafting:** Mastered the art of advanced SQL, merging tables like a pro and wielding WITH clauses for ninja-level temp table maneuvers.

📊 **Data Aggregation:** Got cozy with GROUP BY and turned aggregate functions like COUNT() and AVG() into my data-summarizing sidekicks.

💡 **Analytical Wizardry:** Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.

# Conclusion

From this analysis of the London-based data analyst job market, several key insights emerged:

- Strong Salary Potential with Role Specialization:
Top-paying data-related roles in London offer salaries ranging from approximately $100,000 to $177,000, with research-focused and data architecture positions commanding the highest compensation. This highlights the financial upside of specializing beyond traditional data analyst roles.

- Advanced Technical Skills Drive Higher Pay:
Roles with the highest salaries consistently require advanced technical expertise, particularly in machine learning frameworks (TensorFlow, PyTorch), data science libraries (Pandas, NumPy), and programming languages (Python, C++), signaling a strong market premium for AI and research-oriented skill sets.

- Foundational Skills Remain Essential but Less Differentiating:
Core tools such as SQL, Excel, Tableau, and Power BI continue to dominate job demand, reinforcing their importance for entry and mid-level roles. However, their widespread adoption means they are less likely to significantly differentiate candidates in terms of salary alone.

- Specialized and Cloud-Oriented Skills Increase Market Value:
Skills related to cloud platforms, data warehousing, and analytics infrastructure—including Snowflake, AWS, BigQuery, SSIS, and Looker—are associated with higher average salaries despite lower demand, suggesting that specialization in modern data ecosystems can significantly boost earning potential.

- Optimal Skill Strategy for Career Growth:
The most valuable career strategy combines high-demand foundational skills with high-paying specialized tools. Analysts who pair strong SQL and BI expertise with cloud, data engineering, or machine learning skills are best positioned to maximize both employability and salary growth.

**Closing Thoughts**

This project strengthened my SQL proficiency while providing practical insights into the data analyst job market. By analyzing salary trends alongside skill demand, I gained a clearer understanding of how technical specialization influences earning potential. These findings can serve as a roadmap for aspiring data analysts to prioritize learning paths, focus job searches, and remain competitive in an evolving, data-driven job market. Continuous learning and adaptation to emerging technologies remain critical for long-term success in data analytics.
