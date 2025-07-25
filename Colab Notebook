# Install required libraries
!pip install -q pandas streamlit

# Install localtunnel for exposing the Streamlit app
!npm install -g localtunnel

#cell 1 completed

%%writefile cosmetics_bot_app.py
import streamlit as st
import pandas as pd
import os
from datetime import datetime, date

# --- SalesBot Class ---
class SalesBot:
    def __init__(self, file_path):
        """
        Initializes the bot by loading data from the CSV file.
        """
        self.file_path = file_path
        self.sales_data = None
        self.available_years = [] # To store unique years
        self._load_data()

    def _load_data(self):
        """
        Loads data from the CSV file into a pandas DataFrame and processes 'Date' column.
        """
        try:
            if not os.path.exists(self.file_path):
                raise FileNotFoundError(f"File not found: {self.file_path}")

            df = pd.read_csv(self.file_path)

            required_columns = ['Country', 'Product', 'Amount ($)', 'Boxes Shipped', 'Date']
            if not all(col in df.columns for col in required_columns):
                missing_cols = [col for col in required_columns if col not in df.columns]
                st.error(f"Error: Data file must contain all required columns: {required_columns}. Missing: {missing_cols}")
                st.stop()

            # --- Date Parsing: Uses infer_datetime_format for flexibility ---
            df['Date'] = pd.to_datetime(df['Date'], infer_datetime_format=True, errors='coerce')

            # Drop rows where Date conversion failed, if any (these will be NaT values)
            df.dropna(subset=['Date'], inplace=True)

            # Extract unique years for the filter
            self.available_years = sorted(df['Date'].dt.year.unique().tolist(), reverse=True)
            if not self.available_years: # Add an option for "All Years" if no years found
                self.available_years = ["No Years Found"]
            self.available_years.insert(0, "All Years") # Add "All Years" as default option

            # Convert relevant columns to string and lowercase for consistent lookup
            df['Country_Lower'] = df['Country'].astype(str).str.strip().str.lower()
            df['Product_Lower'] = df['Product'].astype(str).str.strip().str.lower()

            self.sales_data = df
            st.success("Sales data loaded successfully!")

        except FileNotFoundError:
            st.error(f"Error: Data file not found at '{self.file_path}'. Please ensure 'cosmetics_sales_data.csv' is uploaded to the Colab session or the path is correct.")
            st.stop()
        except Exception as e:
            st.error(f"An unexpected error occurred during data loading: {e}")
            st.stop()

    def get_sales_details(self, country, product, selected_date=None, selected_year=None):
        """
        Returns Amount ($) and Boxes Shipped for a given country, product, and selected year/date.
        """
        if self.sales_data is None:
            return "Error: Data not loaded.", "Error: Data not loaded."

        filtered_df = self.sales_data.copy()

        # Apply year filter first if selected and not 'All Years'
        if selected_year and selected_year != "All Years":
            filtered_df = filtered_df[filtered_df['Date'].dt.year == selected_year]
        
        # Apply specific date filter if provided (overrides year filter for that specific day)
        if selected_date:
            filtered_df = filtered_df[filtered_df['Date'].dt.date == selected_date]

        # Apply country and product filters (case-insensitive)
        country_lower = str(country).strip().lower()
        product_lower = str(product).strip().lower()

        result = filtered_df[
            (filtered_df['Country_Lower'] == country_lower) &
            (filtered_df['Product_Lower'] == product_lower)
        ]

        if not result.empty:
            total_amount = result['Amount ($)'].sum()
            total_boxes_shipped = result['Boxes Shipped'].sum()
            return f"{total_amount:.2f}", f"{int(total_boxes_shipped)}"
        else:
            return "N/A", "N/A"

# --- Streamlit Interface with Background Image ---

st.set_page_config(page_title="Cosmetics Sales Bot", layout="centered")

# Custom CSS to set the background image
# IMPORTANT: Ensure 'cosmetics.jpg' is uploaded to your Colab session
page_bg_img = f"""
<style>
[data-testid="stAppViewContainer"] > .main {{
background-image: url("cosmetics.jpg"); /* This must exactly match your uploaded image filename */
background-size: cover; /* Make image cover the entire background */
background-position: center; /* Center the image */
background-repeat: no-repeat; /* Prevent tiling */
background-attachment: fixed; /* Keep image fixed when scrolling */
}}

/* Optionally, you might want to adjust text color for better contrast */
h1, h2, h3, h4, h5, h6, .stMarkdown, .stTextInput > div > label, .stDateInput > label, .stSelectbox > label {{
    color: #333333; /* Darker text for readability if background is light */
    text-shadow: 0.5px 0.5px 1px rgba(255,255,255,0.7); /* Subtle shadow for text on image */
}}
/* Adjust input field backgrounds if needed for readability */
.stTextInput > div > div > input, .stDateInput > div > div > input, .stSelectbox > div > div > div > div > div > input {{
    background-color: rgba(255, 255, 255, 0.8); /* Semi-transparent white background for input fields */
    color: #333333;
}}
</style>
"""
st.markdown(page_bg_img, unsafe_allow_html=True)


st.title("🛍️ Cosmetics Sales Bot 📊")

st.write("Filter by **Year** or **Date**, then enter **Country** and **Product** to get sales **Amount ($)** and **Boxes Shipped**.")

data_file_path = "/content/cosmetics_sales_data.csv"

if 'sales_bot' not in st.session_state:
    st.session_state.sales_bot = SalesBot(data_file_path)

# Year selection
selected_year = st.selectbox(
    "Select Year:",
    st.session_state.sales_bot.available_years
)

# Single Date Input field
selected_date = st.date_input("Select Specific Date (Optional)", value=None)

country_input = st.text_input("Enter Country:")
product_input = st.text_input("Enter Product:")

if st.button("Get Sales Details"):
    if country_input and product_input:
        # Check if a specific date is selected and prioritize it
        if selected_date:
            bot = st.session_state.sales_bot
            amount, boxes_shipped = bot.get_sales_details(
                country_input, product_input,
                selected_date=selected_date, # Pass specific date
                selected_year=None # Year from selectbox is ignored if specific date chosen
            )
            st.subheader("Sales Details (Specific Date):")

        # If no specific date, but a year is selected (and not 'All Years')
        elif selected_year and selected_year != "All Years":
            bot = st.session_state.sales_bot
            amount, boxes_shipped = bot.get_sales_details(
                country_input, product_input,
                selected_date=None, # No specific date
                selected_year=selected_year # Use selected year
            )
            st.subheader(f"Sales Details (Year {selected_year}):")
        
        # If neither specific date nor specific year selected (i.e., 'All Years')
        else:
            bot = st.session_state.sales_bot
            amount, boxes_shipped = bot.get_sales_details(
                country_input, product_input,
                selected_date=None,
                selected_year=None # Search across all years
            )
            st.subheader("Sales Details (All Years):")


        if amount != "N/A":
            st.success(f"**Amount ($):** ${amount}")
            st.success(f"**Boxes Shipped:** {boxes_shipped}")
        else:
            st.warning("No sales data found for the specified combination of filters.")
    else:
        st.warning("Please enter both Country and Product to search.")

st.markdown("""
---
*Cosmetics Sales Analyser*
""")


#cell 2 completed


# Run the Streamlit app in the background and pipe output to a log file
!streamlit run cosmetics_bot_app.py &>/content/logs.txt &

# Start localtunnel to expose the Streamlit app running on port 8501
!npx localtunnel --port 8501 & curl ipv4.icanhazip.com



#cell 3 completed
