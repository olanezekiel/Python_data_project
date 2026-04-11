

# Overview

Welcome to my deep dive into the data job market\! I built this project to better navigate the data analyst landscape and figure out exactly what employers are looking for right now. By analyzing top-paying and high-demand skills, I wanted to uncover the best opportunities out there for data analysts today.

The data powering this analysis comes from [Luke Barousse's Python Course](https://lukebarousse.com/python). Using this as my foundation, I wrote a series of Python scripts to explore the most sought-after skills, salary trends, and the sweet spot where high demand meets high pay.

# The Questions

Going into this project, I had four main questions I wanted to answer:

1.  What are the skills most in demand for the top 3 most popular data roles?
2.  How are in-demand skills trending for Data Analysts?
3.  How well do jobs and skills pay for Data Analysts?
4.  What are the optimal skills for data analysts to learn? (High Demand AND High Paying)

# Tools I Used

To tear into the data and uncover these insights, I relied on a solid stack of tools:

  * **Python:** The engine behind my analysis. I leaned heavily on a few core libraries:
      * **Pandas:** For cleaning and wrangling the data.
      * **Matplotlib:** For building the foundational visualizations.
      * **Seaborn:** For crafting more advanced, eye-catching charts.
  * **Jupyter Notebooks:** My main workspace for running code and taking notes side-by-side.
  * **Visual Studio Code:** My primary editor for writing and executing Python scripts.
  * **Git & GitHub:** Kept everything version-controlled and ready to share, ensuring I could track my progress and collaborate easily.

# Data Preparation and Cleanup

Before jumping into the fun stuff, I needed to make sure the data was clean and ready to go. Here is a quick look at my prep process.

## Import & Clean Up Data

First up: importing the right libraries, pulling in the dataset, and handling some initial data type conversions to make the `job_skills` column usable.

```python
#importing libraries
import ast 
import seaborn as sns
import pandas as pd
from datasets import load_dataset
import matplotlib.pyplot as plt
import seaborn as sns

# Loading data
dataset= load_dataset('lukebarousse/data_jobs')
df= dataset['train'].to_pandas()

#Data cleanup
df['job_posted_date']= pd.to_datetime(df['job_posted_date'])
df['job_skills']= df['job_skills'].apply(lambda x:ast.literal_eval(x) if pd.notna(x) else x)
```

## Filter US Jobs

Since my focus is strictly on the U.S. job market, I filtered the dataset to only include roles based in the United States.

```python
df_US = df[df['job_country'] == 'United States']
```

# The Analysis

Each Jupyter notebook in this project tackles a specific piece of the puzzle. Here is how I broke down my first major question:

## 1\. What are the most demanded skills for the top 3 most popular data roles?

To figure this out, I first filtered the dataset to identify the most popular job titles. From there, I extracted the top five skills requested for each of those top three roles. This gave me a clear roadmap of what skills to prioritize depending on the exact role I'm targeting.

View my notebook with detailed steps here: [2\_Demand\_skiils](MY_project\2_Demand_skills.ipynb).

### Visualize Data

```python
fig, ax= plt.subplots(len(job_titles),1)

sns.set_theme(style='ticks')

for i, job_title in enumerate(job_titles):
    df_plot= df_skills_perc[df_skills_perc['job_title_short']==job_title].head(5)
    sns.barplot(data=df_plot, x= 'skills_perecnt',y= 'job_skills', ax=ax[i], hue='skills_count', palette='dark:b_r')
    ax[i].set_title(job_title)
    ax[i].set_xlim(0, 78)
    ax[i].set_ylabel(' ')
    ax[i].set_xlabel(' ')
    ax[i].legend().set_visible(False)

fig.suptitle('Likelihood of Skills Requested in US Job Posting', fontsize=15)
fig.tight_layout(h_pad=0.5)
plt.show()
```

### Results

![Bar graph visualizing the top 5 skills associated with the top 3 most popular data roles.](MY_project\image\1.png)

### Insights

  * **SQL is king** for Data Analysts and Data Scientists, appearing in over half of the job postings for both roles. For Data Engineers, **Python** takes the lead, showing up in 68% of postings.
  * **Data Engineers need a specialized stack** (like AWS, Azure, and Spark), whereas Analysts and Scientists lean more heavily on general data manipulation and visualization tools (like Excel and Tableau).
  * **Python is incredibly versatile.** It is highly demanded across the board, but is most prominent for Data Scientists (72%) and Data Engineers (65%).

----

## 2\. How are in-demand skills trending for Data Analysts?

Next up, I wanted to see how the demand for these skills shifted over time. Are certain tools gaining traction while others fade? To figure this out, I filtered the dataset specifically for Data Analyst roles and grouped the requested skills by the month the job was posted. This gave me a clear timeline showing the ebb and flow of the top 5 skills throughout 2023.

View my notebook with detailed steps here: [3\_Skills\_Trend](3_Skills_Trend.ipynb).

### Visualize Data

```python
df_plot= df_percent.iloc[:, :5]


sns.lineplot(data= df_plot, dashes=False, palette= 'tab10')
sns.set_theme(style='ticks')
sns.despine()

plt.title('Trending Top skills for Data Analyst in the US')
plt.ylabel('Likelihood in job posting')
plt.legend().remove()

from matplotlib.ticker import PercentFormatter
ax= plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter(decimals=0))
```

### Results

  
![Line graph visualizing the trending top skills for data analysts in the US](MY_project\image\2.png)

### Insights

* **The Undisputed Heavyweights:** SQL and Excel are in a league of their own. SQL is the clear king of the hill, demanded in over 50% of job postings for most of the year, though it experienced a noticeable dip heading into Q4. Excel holds a strong, distant second place, consistently hovering around 40% before a late-year dip and subsequent December rally.
* **The Neck-and-Neck Midfield:** Python and Tableau are locked in a tight race throughout the entire year, both floating between the 25% and 30% mark. They mirror each other's movements closely, with Tableau even managing to briefly edge out Python in late summer (August) before they tied up again in December.
* **The Steady Niche:** SAS rounds out the top five. While it doesn't command the massive market share of SQL or Excel, it maintains a very consistent, reliable baseline near 20% demand from January through December.

***

## 3\. How well do jobs and skills pay for Data Analysts?
 I wanted to see exactly how compensation shakes out across the industry. To keep the data clean and focused, I restricted this analysis strictly to U.S.-based jobs and zeroed in on median salaries.

Before diving into the value of individual skills, I took a step back to look at the broader salary distributions for core data roles—like Data Scientist, Data Engineer, and Data Analyst—to establish a clear baseline of what the market pays.

View my notebook with detailed steps here: [4\_Salary\_Analysis](4_salary_Analysis.ipynb).

#### Visualize Data

```python
sns.boxplot(data=df_US_top6, x='salary_year_avg', y='job_title_short', order=job_order)
sns.set_theme(style='ticks')
sns.despine()


plt.title('Salary Distributions of Data Jobs in the US')
plt.xlabel('Yearly Salary (USD)')
plt.ylabel('')
plt.xlim(0, 600000) 
ticks_x = plt.FuncFormatter(lambda y, pos: f'${int(y/1000)}K')
plt.gca().xaxis.set_major_formatter(ticks_x)
plt.show()
```

#### Results

  
![Box plot visualizing the salary distributions for the top 6 data job titles.](MY_project\image\3.png)
*Box plot visualizing the salary distributions for the top 6 data job titles.*

#### Insights

* **The Predictability of Data Analysts:** Data Analyst roles have the tightest salary distribution (the narrowest box) and the lowest median (just under $100K). This means compensation for this role is highly consistent and predictable, making it a stable entry point, though it has the lowest ceiling of the group.
* **The Seniority Pay Bump:** As expected, median salaries and the overall spread of pay scale up as you move into specialized and senior roles. Senior Data Scientists and Senior Data Engineers top the charts when it comes to median pay, both hovering around the $150K mark. The wider boxes for these roles indicate that pay becomes much more variable depending on the specific company or niche.
* **The Outlier Anomaly:** Here is the most fascinating takeaway: while Senior roles have higher *median* pay, the absolute highest earning potential actually appears in the non-senior **Data Scientist** and **Data Engineer** roles. Data Scientists have a massive outlier pushing $600K, and Data Engineers have one clearing $500K. This suggests that highly specialized individual contributors at certain top-tier tech companies can actually out-earn standard senior-level positions.

***

### Highest Paid vs. Most Demanded Skills

Next, I focused strictly on Data Analyst roles. I wanted to see the contrast between the skills that pay the highest and the ones employers actually ask for the most. I visualized them side-by-side to get a clear picture of what the market values.

#### Visualize Data

```python
fig, ax = plt.subplots(2, 1)  


sns.barplot(data=df_DA_top_pay, x='median', y=df_DA_top_pay.index, hue='median', ax=ax[0], palette='dark:b_r')
ax[0].legend().remove()
ax[0].set_title('Highest Paid Skills for Data Analysts in the US')


sns.barplot(data=df_DA_skills, x='median', y=df_DA_skills.index, hue='median', ax=ax[1], palette='light:b')
ax[1].legend().remove()
ax[1].set_title('Most In-Demand Skills for Data Analysts in the US')

sns.set_theme(style='ticks')
plt.tight_layout()
plt.show()
```

#### Results
![Highest paid & in demand skills for Data analyst in the US](MY_project\image\4.png)
*Comparing the median salaries of the highest-paid skills versus the most widely demanded skills.*

#### Insights

  * **Specialized tech pays a premium:** Niche tools like `dplyr`, `Bitbucket`, and `Gitlab` top the charts, pushing median salaries well past $150K.
  * **Core skills get you hired:** The bottom chart shows that everyday tools like Python, SQL, and Excel are what employers actually want most. They pay less on average ($80K–$100K), but they are the baseline requirements for the job.
  * **The takeaway:** You need the core foundational skills to actually land a role, but adding a few highly specialized tools to your stack is what ultimately drives up your salary.

-----

## 4\. What are the most optimal skills to learn for Data Analysts?

Finally, I wanted to find the most optimal skills to learn—the ones that offer both high salaries and high demand. To do this, I compared the median salary of each skill against how often it appears in job postings.

View my notebook with detailed steps here: [5\_Optimal\_Skills](MY_project\5_optimal_skills.ipynb).

#### Visualize Data

```python
from adjustText import adjust_text

plt.scatter(df_DA_skills_high_demand['skill_percent'], df_DA_skills_high_demand['median_salary'])
plt.xlabel('Percent of Data Analyst Jobs')
plt.ylabel('Median Salary ($USD)')  
plt.title('Most Optimal Skills for Data Analysts in the US')

plt.show()
```

#### Results
![scattered plot](MY_project\image\5.png)
    
*Scatter plot showing the relationship between skill demand and median salary.*

#### Insights

  * **Specialized skills pay more:** `Oracle` has the highest median salary (nearly $97K) but appears in fewer job postings. This shows employers pay a premium for specialized database knowledge.
  * **Common skills are required, but pay less:** `Excel` and `SQL` are heavily demanded, but they offer lower median salaries compared to more specialized tools.
  * **The optimal choices:** `Python`, `Tableau`, and `SQL Server` hit the sweet spot. They are frequently requested in job postings and offer salaries on the higher end of the spectrum.:

-----

### Visualizing Different Technologies

I also color-coded the skills by their technology category (like "Programming" or "Analyst Tools") to see if certain types of tools pay more.

#### Visualize Data

```python
from matplotlib.ticker import PercentFormatter

 sns.scatterplot(
    data=df_DA_skills_tech_high_demand,
    x='skill_percent',
    y='median_salary',
    hue='technology'

)
plt.show()
```

#### Results
![scatter plot](MY_project\image\6.png)
  
*Scatter plot grouping the optimal skills by their technology category.*

#### Insights

  * **Programming pays the most:** Programming skills consistently offer the highest median salaries.
  * **Databases are highly valued:** Database tools like Oracle and SQL Server also command top salaries.
  * **Analyst tools are the baseline:** Visualization tools like Tableau and Power BI are required in a large number of job postings and still offer competitive pay.

***

# What I Learned

This project helped me improve my Python skills and better understand the job market. Here are my main takeaways:

- **Python Skills:** I improved my ability to use Pandas to manipulate data and Seaborn/Matplotlib to build charts.
- **Data Cleaning is Essential:** I learned that you cannot skip the data cleaning process. If the data isn't formatted correctly, your final results will be wrong.
- **Strategic Learning:** Instead of guessing what to learn next, I now know how to look at actual market data to decide which skills offer the best return on my time.

# Insights

Taking a step back, this analysis showed a few broad trends in the data job market:

- **Pay vs. Demand:** Basic skills get you hired, but specialized skills (like Python and Oracle) get you paid more. 
- **The Market Changes:** The skills employers ask for fluctuate over time. You have to keep paying attention to market trends to stay competitive.

# Challenges I Faced

This project had a few roadblocks that I had to work through:

- **Messy Data:** Handling missing values and inconsistent data was frustrating and required a lot of careful filtering.
- **Creating Clear Charts:** It was difficult to take complex datasets and turn them into charts that were actually easy to read and understand.
- **Staying Focused:** Because there was so much data available, it was hard to decide what to focus on without getting distracted by small details.

# Conclusion

This project gave me a much clearer picture of the data analyst job market. The insights I found provide a real roadmap for what skills to focus on to advance in this field. Because the tech industry is always changing, I know I will need to keep learning and adapting. This project is a great foundation, and I plan to keep analyzing data like this to stay ahead in my career.