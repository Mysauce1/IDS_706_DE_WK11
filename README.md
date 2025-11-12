# IDS 706 - Data Engineering - Week 11 - Pyspark Data Processing

## Project Overview

This project is dedicated to exploring how to process data and evaluate the performance of the data processing using Pyspark and Databricks

## Dataset
The data used for this project is a synthetic e-commerce dataset from Kaggle. The data is organized in 5 related tables, including:
- customers: General customer information, such as customer name and email
- orders: Information about the order, such as payment method and shipping country
- order items: Information about the product that was purchased, such as the quantity of the item that was purchased and the price per unit of the product
- products: General product information, such as the product name and the number of units of the product in stock
- product_reviews: Reviews of the product

The data can be found here: https://www.kaggle.com/datasets/naelaqel/synthetic-e-commerce-relational-dataset

## Performance Analysis

### Query 1 Performance

<img width="1680" height="801" alt="Screenshot 2025-11-11 210926" src="https://github.com/user-attachments/assets/c94b1727-e275-42f9-a77c-dee5c95468be" />

<img width="1348" height="631" alt="Screenshot 2025-11-11 220441" src="https://github.com/user-attachments/assets/069d171c-adb0-401b-8706-6b95d2c949dc" />

<img width="1371" height="157" alt="Screenshot 2025-11-11 220637" src="https://github.com/user-attachments/assets/6cdef7a9-7d66-4ce6-a35b-b88726eeae5d" />


### Query 2 Performance

<img width="1718" height="793" alt="Screenshot 2025-11-11 211826" src="https://github.com/user-attachments/assets/b33b70ee-829d-49e6-a50a-caabab8af5a1" />

<img width="1398" height="648" alt="Screenshot 2025-11-11 220511" src="https://github.com/user-attachments/assets/2a34b42a-d34e-4db7-9d68-59aeaa71a986" />

<img width="1342" height="164" alt="Screenshot 2025-11-11 220728" src="https://github.com/user-attachments/assets/900c9dd0-0958-4d3f-ba9a-74138af167f7" />




Here is an overview of the pipeline’s performance and how Spark optimized it. It pushed filters such as the year and payment method filters down to the file scan level so that only the necessary rows were read from the CSV files. It also pruned unused columns, meaning it only loaded the data actually needed for the joins and aggregations. The data was converted to a columnar format for faster processing. These steps reduced memory use and improved overall performance.
Even with these optimizations, most of the runtime was spent on shuffle and join operations. In Query 1, about 51% of the total time came from shuffle stages, while joins took around 38%. In the Query 2, shuffles took up to 1.85 seconds, which was longer compared to the first query. These slowdowns happened because large datasets needed to be redistributed across nodes for the joins and group-by steps. To make the pipeline faster for the queries, I applied filters early in the pipeline, and I included partitions based on the columns that were filtered or grouped in the queries. The goal behind this was to reduce the amount of data the pipeline had to process at a given time.


## Data Analysis
### Query 1: Find the 5 categories that have the most products in stock

<img width="466" height="216" alt="Screenshot 2025-11-11 223923" src="https://github.com/user-attachments/assets/7b5806b7-a4c8-4901-a266-74ed3ec92ab2" />


The 5 categories that have the most products in stock in 2024 are clothing, books, electronics, toys, and beauty. Clothing is an essential resource, so it seems reasonable that clothes are the most in stock. Considering e-books have grown in popularity over the past 15 years, it’s possible that the large supply of books is a result of people not buying them. Electronics are a big part of many peoples’ lives and there’s always people who want to buy them, so that may be why there is a lot of electronics in stock. It’s also possible that the electronic devices are not desirable, and the stock for them is large because no one wants to buy them. Toys are probably large in stock because kids love toys. Lastly, beauty products are probably high in stock because these products are heavily utilized by women on a daily basis.

### Query 2: Find the 3 products that have generated the most revenue among female customers

<img width="474" height="166" alt="Screenshot 2025-11-11 223935" src="https://github.com/user-attachments/assets/67029f74-cf11-48b5-8116-ae913f742f9f" />


The 3 products that female customers spend the most money on are Decide Morning ($235,148.20), Under Break ($215,501,27), and Alone National ($198,856.40). Decide Morning is a home product. While the type of home product is unknown, it’s common for women to buy things for their homes that can provide utility or aesthetic. Under Break is a beauty product. Given how popular beauty products are among women, and how expensive they can be if they are high-quality products, this could explain why Under Break is one of the top money-generating products among women. Alone National is a book, which could be heavily purchased by women for book clubs and leisure reading.



## Transformation vs. Action
```
# Transformation: Filter the merged dataframe to only include US customers
us_customers = merged_df.filter(merged_df['country'] == 'United States of America')


# Action: Count the number of US customers
us_customers.count()
```

This is an example of a transformation vs an action. The first line of code shows a transformation where the merged dataset is filtered to only include US customers. This line of code isn’t executing anything, it’s just setting up the dataframe for future execution. The second line of code shows an action where the number of US customers is counted. This causes the driver to request and receive an output from an executor.
