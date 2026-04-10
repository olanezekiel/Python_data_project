

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
# Importing Libraries
import ast
import pandas as pd
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt  

# Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
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

View my notebook with detailed steps here: [2\_Skill\_Demand](https://www.google.com/search?q=2_Skill_Demand.ipynb).

### Visualize Data

```python
fig, ax = plt.subplots(len(job_titles), 1)

for i, job_title in enumerate(job_titles):
    df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_title].head(5)[::-1]
    sns.barplot(data=df_plot, x='skill_percent', y='job_skills', ax=ax[i], hue='skill_count', palette='dark:b_r')

plt.show()
```

### Results

*Bar graph visualizing the top 5 skills associated with the top 3 most popular data roles.*

### Insights

  * **SQL is king** for Data Analysts and Data Scientists, appearing in over half of the job postings for both roles. For Data Engineers, **Python** takes the lead, showing up in 68% of postings.
  * **Data Engineers need a specialized stack** (like AWS, Azure, and Spark), whereas Analysts and Scientists lean more heavily on general data manipulation and visualization tools (like Excel and Tableau).
  * **Python is incredibly versatile.** It is highly demanded across the board, but is most prominent for Data Scientists (72%) and Data Engineers (65%).

-----


