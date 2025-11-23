
# Data Scraping and Analysis Project

This project demonstrates the process of scraping data from a website, cleaning and transforming it, performing data analysis, and generating insights with Python and SQL.

## Project Overview
This project scrapes product data from the Banggood website, including categories like **Toys, Hobbies, and Robots**, **Sports and Outdoors**, and **Men & Women's Clothing**. The data is then cleaned, transformed, and analyzed to provide valuable insights into product prices, ratings, and sales.

## Project Structure:
- **/data**: Contains raw and cleaned data files.
- **/scripts**: Python scripts for scraping, cleaning, and analysis.
- **/notebooks**: Jupyter notebooks for data exploration and analysis.
- **/reports**: Final report and results.
- **/images**: Diagram and other visuals.

## Scraping Methodology
### 1. Scraping Categories and Subcategories:
The scraping process starts by collecting the main product categories and drilling down into each subcategory. Each category and subcategory page contains a list of products with details such as product name, price, rating, and reviews.

#### Categories and URLs:
1. **Toys, Hobbies, and Robots**
   - [Main Category](https://www.banggood.com/Wholesale-Toys-Hobbies-and-Robot-c-133.html)
   - Subcategories:
     - [DIY RC Parts](https://www.banggood.com/Wholesale-RC-Parts-ca-7013.html)
     - [RC Vehicle](https://www.banggood.com/Wholesale-RC-Vehicles-ca-7007.html)
     - [Science and Discovery Toys](https://www.banggood.com/Wholesale-Science-and-Discovery-Toys-ca-18977.html?bid=21070104&from=nav)
     - [RC Drones](https://www.banggood.com/Wholesale-RC-Drones-ca-7002.html?bid=21070101&from=nav)
2. **Sports and Outdoors**
   - [Main Category](https://www.banggood.com/Wholesale-Sports-and-Outdoors-ca-6001.html)
   - Subcategories:
     - [Cycling](https://www.banggood.com/Wholesale-Cycling-ca-6002.html)
     - [Camping](https://www.banggood.com/Wholesale-Camping-ca-6030.html)
     - [Sports & Fitness](https://www.banggood.com/Wholesale-Fitness-Wellness-ca-6016.html)
     - [Water Sports](https://www.banggood.com/Wholesale-Water-Sports-ca-6059.html)
     - [Fishing and Hunting](https://www.banggood.com/Wholesale-Fishing-and-Hunting-ca-6023.html)
3. **Men & Women’s Clothing**
   - [Main Category](https://www.banggood.com/Wholesale-Men-and-Womens-Clothing-ca-18941.html)
   - Subcategories:
     - [Men’s Clothing](https://www.banggood.com/Wholesale-Mens-Clothing-ca-15001.html)
     - [Women’s Clothing](https://www.banggood.com/Wholesale-Womens-Clothing-ca-16001.html)
     - [Beauty & Styling](https://www.banggood.com/Wholesale-Beauty-and-Styling-ca-18948.html)
     - [Jewelry & Watches](https://www.banggood.com/Wholesale-Jewelry,Watches-and-Accessories-ca-8001.html)
     - [Men’s Accessories](https://www.banggood.com/Wholesale-Mens-Accessories-ca-10007.html)

### 2. Pagination Handling:
Pagination is handled to scrape multiple pages of product listings. The scraper clicks the "Next" button to navigate through the pages and collect data from each page.

### Example Selenium Code for Scraping with Pagination:
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
import time
import pandas as pd

# Initialize Selenium WebDriver (using Chrome in this case)
driver = webdriver.Chrome(executable_path='path_to_chromedriver')

# List of URLs to scrape (Category and Subcategories)
urls = [
    "https://www.banggood.com/Wholesale-Toys-Hobbies-and-Robot-c-133.html",
    "https://www.banggood.com/Wholesale-RC-Parts-ca-7013.html",
    # Add more URLs here for other subcategories
]

# List to store scraped data
product_data = []

# Function to scrape products from a given URL
def scrape_category(url):
    driver.get(url)
    time.sleep(2)  # Wait for page to load

    while True:
        # Scrape product details
        products = driver.find_elements(By.XPATH, '//div[@class="product-item"]')

        for product in products:
            try:
                name = product.find_element(By.XPATH, './/div[@class="product-title"]/a').text
                price = product.find_element(By.XPATH, './/div[@class="price"]/span').text
                rating = product.find_element(By.XPATH, './/div[@class="rating"]/span').text
                reviews = product.find_element(By.XPATH, './/div[@class="reviews"]/span').text

                product_data.append([name, price, rating, reviews])
            except Exception as e:
                print(f"Error scraping product: {e}")

        # Check if "Next" button is present to handle pagination
        try:
            next_button = driver.find_element(By.XPATH, '//a[@class="next"]')
            next_button.click()
            time.sleep(2)  # Wait for next page to load
        except:
            print("No more pages, ending the scraping process.")
            break

# Loop through URLs and scrape data
for url in urls:
    scrape_category(url)

# Convert scraped data into a DataFrame
df = pd.DataFrame(product_data, columns=['Product Name', 'Price', 'Rating', 'Reviews'])

# Save the DataFrame to a CSV file
df.to_csv('scraped_products.csv', index=False)

# Close the driver after scraping is done
driver.quit()
```

## 4. Cleaning/Transformation Steps:
The data cleaning and transformation process involved several steps to ensure the quality and consistency of the data:
1. Handling Missing Data: Missing values were either filled using appropriate methods or dropped.
2. Data Type Conversion: Columns were converted to the appropriate data types.
3. Removing Duplicates: Duplicate rows were removed to ensure the integrity of the data.
4. Outlier Detection: Statistical methods were used to detect and remove outliers.

## 5. Python Analyses (with Graphs):
Data analysis was performed using Python libraries such as pandas, matplotlib, and seaborn.
### 1. **Price Distribution per Category**: A histogram was plotted to visualize the price distribution of products.
### 2. **Rating vs Price Correlation**: A scatter plot was used to examine the relationship between product price and rating.

### Example Python Code for Plotting:
```python
import matplotlib.pyplot as plt
import seaborn as sns

# Plotting the price distribution
sns.histplot(df['price'], bins=20, kde=True)
plt.title('Price Distribution')
plt.xlabel('Price')
plt.ylabel('Frequency')
plt.show()
```

## 6. SQL Aggregated Insights:
SQL queries were used to aggregate data and gain insights from the dataset.
### Key SQL queries included:
1. **Average Rating per Category**:
```sql
SELECT category, AVG(rating) as avg_rating
FROM products
GROUP BY category
ORDER BY avg_rating DESC;
```

2. **Total Revenue per Product**:
```sql
SELECT product_name, SUM(price * quantity_sold) as total_revenue
FROM sales
GROUP BY product_name
ORDER BY total_revenue DESC;
```

## 7. Final Conclusions and Recommendations:
Based on the analysis and insights from the data, the following conclusions and recommendations were made:
1. **Price Distribution**: The majority of products are priced low, with only a few high-priced products.
2. **Top Rated Products**: Higher-rated products generally have a higher price.
3. **Recommendations**: Pricing strategies should be adjusted for low-rated products, and marketing efforts should focus on the high-rated products.
