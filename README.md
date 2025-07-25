# BeautyMetrics Bot: Cosmetics Sales Analyzer

## 🌟 Project Overview

The **BeautyMetrics Bot** is an interactive web application built with Streamlit that provides insightful analytics on cosmetics sales data. It allows users to quickly retrieve sales figures (Amount and Boxes Shipped) for specific products, countries, and date ranges. Designed for ease of use, this bot transforms raw sales figures into actionable intelligence, helping you understand performance, identify trends, and support data-driven decision-making within the cosmetics industry.

## ✨ Features

* **Dynamic Data Filtering:** Dive deep into your sales performance by filtering data with a flexible date selection:
    * **Specific Date:** Get precise metrics for a chosen day using a calendar dropdown.
    * **Yearly Aggregation:** View combined sales data for an entire selected year.
    * **"All Years" Option:** Aggregate data across the entire dataset if no specific year or date is chosen.
* **Instant Sales Metrics:** Displays total `Amount ($)` and `Boxes Shipped` corresponding to your applied filters.
* **Intuitive User Interface:** A clean, responsive, and easy-to-use web interface powered by Streamlit, featuring a custom background image (`cosmetics.jpg`) for an enhanced visual experience.
* **Data-Driven Decisions:** Whether you're tracking daily sales, analyzing annual trends for a specific product, or comparing performance across regions, the BeautyMetrics Bot empowers you to make informed business decisions with confidence.
* **Robust Data Handling:** Includes error handling for missing files, incorrect column names, and flexible date parsing.

## 🛠️ Technologies Used

* **Python**
* **Pandas:** For efficient data manipulation and analysis.
* **Streamlit:** For building the interactive web application interface.
* **Google Colab:** For cloud-based development and hosting (via `localtunnel`).

## 📦 Requirements

To run the BeautyMetrics Bot, you need the following dependencies:

### Python Libraries

These libraries are crucial for the bot's functionality and can be installed using `pip`.

```txt
pandas
streamlit
openpyxl  # Required by pandas to read .xlsx files (even if using .csv, it's a common dependency for data projects)
