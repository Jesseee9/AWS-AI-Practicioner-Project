# Project 2: Air Quality Data Analysis with PartyRock

An AI-powered data analysis project using **AWS PartyRock's Analyze Data** feature to explore a 30-day air quality dataset, ask analytical questions, evaluate the accuracy of AI-generated insights, and identify patterns across six different pollutants.

---

## The Dataset

**Source:** OpenAQ (Open Air Quality) sample dataset provided by the AWS AI Practitioner Challenge

**Location:** Del Norte monitoring station (ID 2178), Albuquerque, New Mexico, USA

**Time period:** December 1–31, 2025 (hourly readings)

**Structure:** 9 columns across approximately 4,200+ rows

| Column | Description |
|--------|-------------|
| location_id | Monitoring station identifier |
| sensors_id | Individual sensor identifier |
| location | Station name (Del Norte-2178) |
| datetime | Hourly timestamp |
| lat / lon | GPS coordinates |
| parameter | Pollutant type |
| units | Measurement unit |
| value | Recorded reading |

**Six pollutants tracked:**

| Parameter | Full Name | Unit |
|-----------|-----------|------|
| pm10 | Coarse particulate matter | µg/m³ |
| pm25 | Fine particulate matter | µg/m³ |
| no2 | Nitrogen dioxide | ppm |
| co | Carbon monoxide | ppm |
| so2 | Sulfur dioxide | ppm |
| o3 | Ozone | ppm |

---

## Approach

I chose the Air Quality dataset because working with time-series sensor data maps directly to skills used in IT support and infrastructure monitoring. Tracking pollutant levels over time, spotting anomalies, identifying thresholds, and correlating related metrics is the same thought process as monitoring CPU usage, network latency, or ticket volumes on a dashboard.

My goal was not just to ask basic summary questions, but to push the AI to find patterns, flag anomalies, and compare how different metrics relate to each other. I also wanted to test whether the AI's analysis could be trusted or whether it made mistakes.

---

## Questions Asked

I asked PartyRock's AI assistant (Whiskers) five prompts in total. Four analytical questions and one final prompt to generate a summary table for export.

### Question 1: Overview and Baseline

**Prompt:**
> What are all the different air quality parameters recorded in this dataset? For each parameter, give me the average, minimum, and maximum values, the unit of measurement, and the total number of readings.

**Why I asked this:** I needed a baseline to verify the AI was reading the data correctly before asking deeper questions. This also established the averages I would compare against later.

**What it returned:** All six parameters correctly identified with summary statistics. PM10 showed the highest variability (2 to 106 µg/m³). Reading counts varied between 666 and 737 per parameter, meaning some hours had missing data for certain pollutants.

---

### Question 2: Daily Patterns

**Prompt:**
> How does air quality change throughout the day? Calculate the average value for each parameter grouped by hour of the day (0-23) and identify which hours have the worst air quality for each pollutant.

**Why I asked this:** I wanted to see if pollution follows predictable time-of-day patterns, similar to how IT systems show predictable load patterns during business hours.

**What it returned:** Traffic-related pollutants (CO, NO2, PM10) peak during the 3 to 5 PM rush hour. PM2.5 peaks during early morning hours (3 to 7 AM). However, the AI produced an incorrect analysis of ozone, which I document in the accuracy section below.

---

### Question 3: Anomaly Detection

**Prompt:**
> Are there any days or time periods with unusually high pollution readings compared to the overall averages? Identify the top 5 peak pollution events in the dataset and explain what might be notable about those times.

**Why I asked this:** Anomaly detection is the most valuable analytical skill in both environmental monitoring and IT operations. I wanted to see if the AI could go beyond averages and find the unusual events hiding in the data.

**What it returned:** The AI identified five significant events, including a New Year's Eve PM2.5 spike (39.9 µg/m³, nearly 7x the average), a PM10 extreme of 106 µg/m³ on December 18th, and a multi-pollutant event on December 19th where five parameters spiked simultaneously. It used standard deviations to rank severity, which was a strong approach.

---

### Question 4: Correlations

**Prompt:**
> Is there a relationship between pm10 and pm25 readings? When one is high, is the other also high? Also compare how co and no2 values relate to each other throughout the month.

**Why I asked this:** Looking at pollutants in isolation only tells part of the story. Understanding how metrics relate to each other helps identify root causes, the same way correlating high CPU with high memory usage can point to a specific process causing both.

**What it returned:** CO and NO2 have a 0.90 correlation (very strong), confirming they track the same source: vehicle traffic. PM10 and PM2.5 have a 0.71 correlation (moderate), and when they don't match it reveals whether the source is dust/mechanical (high PM10 only) or combustion (high PM2.5 only). The December 18th event was a clear example where PM10 hit 106 µg/m³ while PM2.5 was only 9.4 µg/m³, pointing to a dust source rather than burning.

---

### Question 5: Summary Table for Export

**Prompt:**
> Generate a summary analysis table showing the daily average value for each air quality parameter (pm10, pm25, no2, co, so2, o3) for each day in December 2025. Each row should be one day and each column should be one parameter.

**Why I asked this:** This generated the structured CSV export required for project submission. A daily breakdown across all six parameters provides a clean, scannable view of the entire month.

**Output:** A 31-row table with daily averages for all six pollutants, plus the day of the week. Exported as a CSV file.

---

## Key Findings

1. **New Year's Eve caused the most extreme pollution event in the dataset.** PM2.5 peaked at 39.9 µg/m³ between 4 to 7 AM on January 1st, nearly 7x the monthly average of 5.74 µg/m³. CO also spiked to 1.0 ppm on the evening of December 31st. Fireworks and overnight atmospheric trapping are the likely causes.

2. **CO and NO2 are strongly correlated (0.90).** They almost always rise and fall together because both mainly come from vehicle emissions. If you see one spike on a dashboard, you can predict the other will spike too.

3. **PM10 and PM2.5 don't always match, and that tells you something.** Their 0.71 correlation means they usually move together but not always. When PM10 is high and PM2.5 is low, the source is likely dust or construction. When PM2.5 is high and PM10 is normal, the source is likely burning or combustion. December 18th showed this clearly: PM10 hit 106 µg/m³ while PM2.5 was only 9.4 µg/m³.

4. **Different pollutants peak at different times of day.** Traffic pollutants (CO, NO2, PM10) peak at 3 to 5 PM rush hour. PM2.5 peaks at 3 to 7 AM when cold air traps fine particles near the ground. This is the same concept as IT systems showing different load patterns at different times.

5. **The second half of December had worse air quality across the board.** December 15th to 24th showed elevated readings for nearly all pollutants compared to early December. Christmas Eve had the highest daily average CO (0.479 ppm) and PM2.5 (10.90 µg/m³), likely due to holiday activity.

---

## AI Accuracy Evaluation

The AI's statistical calculations were largely reliable. Averages, correlations, min/max values, and anomaly rankings all checked out against the raw data. But I found three clear limitations:

### 1. Rounding Small Values to Zero

SO2 readings in the raw data are typically around 0.0003 ppm. The AI reported the average, minimum, and maximum all as 0.00 ppm in the overview summary. This makes it look like no SO2 was detected at all, which is not true. Anyone relying only on the AI summary would miss that SO2 does exist in the data, just at very small levels.

### 2. Incorrect Ozone Pattern

The AI claimed ozone (O3) peaks in the evening around 10 PM. But the raw data clearly shows O3 peaking in the afternoon (1 to 4 PM) and dropping at night. The AI even contradicted itself by explaining that ozone forms from sunlight reactions, then stating it peaks when there is no sunlight. This is a factual error in the analysis that would go unnoticed without checking the raw data.

### 3. Confident Speculation Presented as Fact

The AI confidently explained the causes behind every pollution spike. Fireworks for New Year's Eve, construction for the December 18th PM10 spike, cold engines for early morning CO levels, diesel vehicles for afternoon NO2 readings. These sound authoritative, but the dataset only contains numbers. It has no information about what actually caused any spike. The AI was guessing, but presenting those guesses as though they were confirmed explanations. This is a pattern worth understanding: generative AI can do the maths correctly while still making up reasons for the results.

---

## Generated Analysis Table

The daily summary table exported from PartyRock is included in this repository:

📄 **[data-analysis-2026-04-15 20-12-12.csv](./data-analysis-2026-04-15%2020-12-12.csv)**

Contains daily average values for all six air quality parameters across December 2025 (31 rows, one per day).

---

## IT Relevance

While this project analyses air quality data, the skills involved map directly to IT operations:

| Data Analysis Skill | IT Equivalent |
|---------------------|---------------|
| Monitoring metrics over time | Tracking CPU, memory, network usage on dashboards |
| Spotting anomalies in sensor data | Reviewing system logs and alerts for unusual activity |
| Correlating related metrics | Linking high CPU with high memory to find root cause |
| Threshold-based analysis | Setting alert thresholds in monitoring tools |
| Evaluating time-of-day patterns | Understanding peak load hours for capacity planning |
| Questioning AI-generated explanations | Not blindly trusting automated reports or AI summaries |

---

## What I Learned

The biggest takeaway from this project is that AI is very good at doing calculations across large datasets quickly, but it is not always right about what the numbers mean. The statistical analysis was solid. The pattern detection was mostly accurate. But the explanations and the causal reasoning were often guesses dressed up as facts.

In an IT context, this means AI tools like this are useful for surfacing patterns and doing the heavy lifting on large datasets, but a human still needs to verify the output, check it against the raw data, and question anything that sounds too confident. The AI got one analysis completely wrong (ozone patterns) and I would not have caught it without looking at the original data myself.

This builds on what I learned in Project 1: AI apps produce more consistent and structured output than raw chatbot conversations, but consistency does not mean accuracy. You still need to verify.

---

## Built With

- **[AWS PartyRock Data Analysis](https://partyrock.aws/data)** (Whiskers AI assistant)
- **Amazon Bedrock** (underlying model infrastructure)

---

## Course

**AWS AI Practitioner Challenge**, part of the AWS Agentic Engineer Nanodegree through Udacity
