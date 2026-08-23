# Aerofit Customer Behavior Analysis

## Table of Contents
- [Project Background](#Project-Background)
- [Data Structure and Context](#Data-Structure-and-Context)
- [Executive Summary](#Executive-Summary)
- [Key Findings](#Key-Findings)
- [Recommendations](#Recommendations)
- [Assumptions and Caveats](#Assumptions-and-Caveats)

***

## Project Background

Aerofit is an Indian fitness company that manufactures residential and commercial gym equipments, such as treadmills, exercise bikes and elliptical trainers.

As part of the Market Research team, my role is to identify the customer profile for each treadmill model to provide a better recommendation of the treadmills to new customers. Therefore, this project analyzes the differences between the products based on customer characteristics.

The analysis focuses on the following key areas:
- **Customer Profile:** Determine customer characteristics associated with each product.
- **Sales Performance:** Analyze units sold and sales value across the products.
- **Growth Opportunities:** Identify opportunities for targeted marketing and product recommendations.

The SQL queries utilized to prepare the data can be found [here](https://github.com/sasha-gutierrez/Aerofit-Customer-Behavior-Analysis/blob/main/aerofit-analysis.md#data-preparation).

The SQL queries utilized to answer business questions can be found [here](https://github.com/sasha-gutierrez/Aerofit-Customer-Behavior-Analysis/blob/main/aerofit-analysis.md#business-questions-and-answers).

***

## Data Structure and Context

The dataset contains information on 180 customers who purchased a treadmill from AeroFit stores during the previous three months. It includes customer characteristics and planned treadmill usage.

AeroFit offers three treadmill models at different price points:

- **KP281:** entry-level treadmill that sells for $1,500.
- **KP481:** mid-level treadmill that sells for $1,750.
- **KP781:** advanced treadmill that sells for $2,500.

<img width="620" height="472" alt="Untitled (1)" src="https://github.com/user-attachments/assets/dd84b910-3e7d-4925-8fbb-1bdcfcd83df6" />

Before starting with the analysis, the original dataset was restructured to improve data organization. The changes included creating a Products table, adding identifiers, and establishing the relationship between products and customer records.

The SQL queries utilized to prepare the data can be found [here](https://github.com/sasha-gutierrez/Aerofit-Customer-Behavior-Analysis/blob/main/aerofit-analysis.md#data-preparation).

***
## Executive Summary
AeroFit's three treadmill models serve distinct customer segments. The KP281 and KP481 treadmills share similar customer profiles, while KP781 customers have higher income and fitness levels. Although the KP281 treadmill generates the highest sales value, the strongest growth opportunity identified is the similarity between KP281 and KP481 customers, which can support more targeted product recommendations.

## Key Findings

### Customer Profile

- **KP281 and KP481 treadmills attract similar customer profiles**, while KP781 customers have higher income, education, fitness and intended usage.
- **69.4% of KP281 customers are in the low-income group, while 69.0% of KP781 customers fall into the high-income group**, showing a clear difference in the income profiles of the entry-level and advanced products.

### Sales Performance

- **KP281 treadmills generated the highest sales value** at $120.000, representing 36.9% of total sales in the previous three months. It also accounted for the largest share of units sold (44.4%).

### Growth Insights

- **The strongest overlap between KP281 and higher-tier customers** was found among customers with middle income, medium fitness, and medium expected weekly usage, identifying this profile as a **potential target for upselling.**
- **KP281 customers show greater similarity to KP481 buyers**, suggesting that the strongest upselling opportunity is toward the mid-level product.

***

## Recommendations

Based on the findings above, the following recommendations have been provided:

- KP281 and KP481 customers share a similar profile: 60% are partnered, average usage is three times per week, and self-rated fitness is 2, with planned weekly miles approximately 85. **Using these characteristics to build targeted campaigns for customers with similar needs, would help AeroFit reach relevant audiences with more tailored product messaging.**
- KP281 customers with middle income, medium fitness, and medium usage show the strongest similarity to KP481 customers. **When new customers match this profile, the Customer Service team should consider recommending the KP481 treadmill as a higher-tier option, to increase mid-level product sales.**
- KP781 accounts for 22.2% of total purchases, the lowest among the three products. **Enhancing marketing for the KP781 treadmill by positioning its advanced features toward higher-income, higher-fitness customers.**
  
***

## Assumptions and Caveats

Throughout the analysis, assumptions were made and limitations were identified based on the available data. These are considered when interpreting the results:
- **Customer segments were defined for analytical purposes.** Income, fitness, usage, and miles groups were created based on the distribution and characteristics of the dataset and should not be interpreted as universal market classifications.
- **Customer similarity does not imply purchase intent.** Similar characteristics between customer segments were used to identify potential targeting or product recommendation opportunities, but the analysis does not establish that customers with those characteristics will purchase a specific product.

***

