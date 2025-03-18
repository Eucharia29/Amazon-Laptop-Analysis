# **Amazon Laptop Data Analysis**

## **Project Overview**

Laptops are one of the most sought after electronic devices, with customers considering multiple factors such as price, brand, specifications, and ratings before making a purchase. This project aims to analyze Amazon laptop listings to identify key trends, including the relationship between price and customer ratings, brand performance, and RAM influence on pricing.

The data was scraped from Amazon using Python’s **BeautifulSoup**, cleaned in **Excel** and **Python**, then analyzed using **Power BI** to generate meaningful insights. The visualizations and KPIs help both consumers make informed buying decisions and retailers optimize their pricing strategies.

## Objective of the Analysis

The objective of this analysis is to explore the relationship between laptop prices and customer ratings to uncover trends and insights that influence purchasing decisions. Specifically, this analysis aims to:

1. **Assess the Impact of Price on Ratings:** Determine whether higher-priced laptops tend to receive better customer ratings or if affordability correlates with higher satisfaction.
2. **Evaluate Key Specifications:** Analyze how factors such as RAM and brand affect customer ratings and overall value perception.
3. **Identify Brand Performance:** Compare different laptop brands to determine which ones consistently offer the best-rated products at different price points.
4. **Understand Customer Preferences:** Examine common trends in highly rated laptops to identify the most desirable features and specifications for buyers.
5. **Support Business and Consumer Decisions:** Provide insights to help businesses optimize pricing strategies and assist consumers in making informed purchasing decisions based on both cost and performance.

## Data Collection and Preprocessing

### Step 1: Web Scraping using Python (BeautifulSoup)

The data was scraped from Amazon using Python’s **BeautifulSoup** library. The script extracted details such as:

- Brand
- RAM Size
- Price
- Rating Score
- Image URL
- Category (Gaming, Office, Student, etc.)

### Code for Web Scraping

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import time

# Amazon URL 
url = "https://www.amazon.com/s?k=laptops&crid=24KNS3N21HOLA&sprefix=laptops%2Caps%2C296&ref=nb_sb_noss_1"

headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36"
}

response = requests.get(url, headers=headers)
soup = BeautifulSoup(response.text, 'html.parser')

# Extract laptop details
def scrape_page(url):
    headers = headers = { 
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebkit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36",
    "Accept-Language": "en-US, en;q=0.9",
}
    response = requests.get(url, headers=headers)
    soup = BeautifulSoup(response.text, "html.parser")
    products = soup.find_all("div", {"data-component-type": "s-search-result"})
    data = []
 
    for product in products:
       title = product.find("h2", class_="a-text-normal") 
       price = product.find("span", class_="a-offscreen")
       rating = product.find("span", class_="a-icon-alt")
       image = product.find("img")
       data.append([
          title.text.strip() if title else "N/A",
          price.text.strip() if price else "N/A",
          rating.text.strip() if rating else "NA",
         image["src"] if image else "N/A"
      ])
    return data, soup

base_url = "https://www.amazon.com/s?k=laptops&crid=24KNS3N21HOLA&sprefix=laptops%2Caps%2C296&ref=nb_sb_noss_1"
all_data = []
max_pages = 20
current_page = 1
url = base_url

# Extract from next pages
while current_page <= max_pages:
    print(f"Scraping page {current_page}...")
    page_data, soup = scrape_page(url)
    all_data.extend(page_data)
    next_page = soup.find("a", class_="s-pagination-next")
    if next_page and "href" in next_page.attrs:
        next_url = "https://www.amazon.com/s?k=laptops&crid=24KNS3N21HOLA&sprefix=laptops%2Caps%2C296&ref=nb_sb_noss_1" + next_page["href"]
        url = next_url
        current_page += 1
        time.sleep(5)
    else:
        break    

        
# Save to CSV
df = pd.DataFrame(all_data, columns=["Title", "Price", "Ratings","Image URL"])
df.to_csv("amazon_laptops.csv", index=False)
```

### Step 2: Data Cleaning in Python and Excel

After scraping, the dataset was cleaned in Python and then moved to Excel for additional cleaning before importing it into **Power BI**. Steps included:

- Removing missing values
- Extracting unecessary character and extra words (with Python)
```python
pattern = r"\b(" + "|".join(words_to_remove) + r")\b"
df["Title"] = df["Title"].str.replace(pattern, "", regex=True).str.strip()
```
- Converting price to numerical format
- Standardizing RAM categories
- Handling inconsistent ratings

## Data Analysis and Visualization using Power BI

The cleaned dataset was imported into **Power BI**, where **KPIs, measures, and visualizations** were created to analyze trends.

### KPIs and DAX Measures Used in Power BI
Key Metrics & Insights from Power BI Dashboard

KPIs (Key Performance Indicators)

The Power BI dashboard highlights the following KPIs:
1.	Average Price: $546 – The typical cost of laptops in the dataset.
2.	Average Rating: 4 – The overall average customer rating across all laptops.
3.	Total Laptops : 45 – The number of laptops included in this analysis.
4.	Total Sales : $173K – The combined price of all laptops analyzed.


```DAX
-- Brands
Total Laptops = COUNT('Amazon Laptops'[Brand])

-- Average Price of Laptops
Average Price = AVERAGE('Amazon Laptops'[Price])

-- Average Rating
Average Rating = AVERAGE('Amazon Laptops'[Rating])

-- Total Price of All Laptops
Total Sales = SUM('Amazon Laptops'[Price])
```

### Power BI Dashboard Features

#### Key Metrics Displayed:

- Average Price, Number of Brands, Total Sales, Average Rating
- Most Expensive and Cheapest Brands
- Count of Laptops by Rating
- RAM vs. Price Analysis
- Price vs. Rating Trends
- Laptop Categories by Ratings

#### Visualizations Used:

1.	**Price vs Ratings Analysis**
•	A line chart shows the correlation between price and ratings, identifying whether expensive laptops generally receive higher ratings.

•	Finding: Laptops with moderate pricing ($1,500 - $2,296) tend to receive better ratings, but some lower-priced models also perform well.

2.	**Most Expensive & Cheapest Brands**
•	Bar charts show the brands with the highest and lowest laptop prices.

•	Finding: MSI, ASUS, and LG have the most expensive models, while FANGOR and WGK offer the cheapest options.

3.	**RAM vs Price Distribution**
•	Bar chart displays RAM capacity distribution by price.

•	Finding: Higher RAM configurations (32GB, 64GB) are significantly more expensive, while 12GB and 16GB are more budget-friendly.

4.	**Rating Distribution by Category**
•	Pie chart categorizes laptops by their average rating, showing the most highly-rated segments.

•	Finding: Gaming and Office laptops have the highest proportion of 4 and 3 star ratings.

5.	**Star Rating Visualization**
•	Star ratings were displayed using custom Power BI visuals beneath the average rating KPI, providing a clearer visual representation.

6.  **Counts of brands by rating**
•   Funnel chart categorized laptops, showing the counts of laptops by ratings.

•   Findings: There are more laptop brands rated 4 and 5, just one laptop brand rated 1.
### Custom Star Rating in Power BI

```DAX
Star Rating = REPT("★", ROUND([Average Rating], 0)) & REPT("☆", 5 - ROUND([Average Rating], 0))
```

## Findings and Insights

1. **Higher-priced laptops do not always receive the highest ratings.**
• Some mid-range laptops received better ratings than expensive ones, suggesting value and user experience matter more than price alone.

2. **Gaming laptops tend to have higher prices but not always the best ratings.**
• This could indicate that gamers prioritize performance over reviews.

3. **Certain brands dominate the premium segment, while others focus on affordability.**
• MSI, ASUS, and Lenovo had the most expensive models, whereas Fangor, WGK, and Android had the cheapest.

4. **RAM affects price significantly, but not always ratings.**
• Higher RAM options (e.g., 32GB and 64GB) had the highest prices but didn’t always correlate with better ratings.

5. **Most customers rated laptops between 3 and 5 stars, with very few 1-star ratings.**
• Indicates that customers generally have positive experiences with their purchases.

## Conclusion

This project successfully demonstrated how **web scraping, data cleaning, and Power BI visualizations** can be combined to extract meaningful insights from Amazon laptop listings.

- **For Businesses:** Helps retailers optimize pricing strategies by understanding how consumers perceive value in different price ranges.
- **For Consumers:** Buyers can make informed decisions by identifying the best-value laptops based on price-to-rating ratios.


## Credits

Project by: **CHIBUIKE EUCHARIA**  
Tools Used: **Python (BeautifulSoup), Excel, Power BI**




