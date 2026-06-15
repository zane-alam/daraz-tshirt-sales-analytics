## Daraz-tshirt-sales-analytics
Daraz T‑Shirt Sales Analytics — CSV (3,280 rows, 9 cols). Reproducible analyses using SQL and Python: data cleaning, EDA, SQL queries, and a short report extracting pricing, discount, sales‑volume, SKU and seller insights. Includes scripts and instructions to reproduce results.

***Handling Missing Values using python***

1.Import required python libraries

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```
2. Load dataset

```python
df = pd.read_csv('/content/daraz_raw_tshirt_sales_data.csv')
```

3.load the info of dataset

```python
df.info()
```
4. FInd the amount of Missing/Null values

```python
print(df.isnull().sum())
```

5. Finding the mean values[extracting numeric values from strings

```python
df['No. of Sells'] = pd.to_numeric(df['No. of Sells'].astype(str).str.extract(r'(\d+)', expand=False), errors='coerce')
print(df['No. of Sells'].mean())
df['No. of Review'] = pd.to_numeric(df['No. of Review'].astype(str).str.extract(r'(\d+)', expand=False), errors='coerce')
print(df['No. of Review'].mean())
df.columns = df.columns.str.strip()
df['Discount(%)'] = pd.to_numeric(df['Discount(%)'].astype(str).str.extract(r'(\d+)', expand=False), errors='coerce')
print(df['Discount(%)'].mean())
```

6.Updating Null values with mean value

```python
updated_df= df
updated_df['No. of Sells'] = updated_df['No. of Sells'].fillna(df['No. of Sells'].mean())
updated_df.info()
updated_df= df
updated_df['No. of Review'] = updated_df['No. of Review'].fillna(df['No. of Review'].mean())
updated_df.info()
updated_df= df
updated_df['Discount(%)'] = updated_df['Discount(%)'].fillna(df['Discount(%)'].mean())
updated_df.info()
```

7. Delete extra unwanted columns

```python
df = df.drop(columns=['Unnamed: 9', 'Unnamed: 10'], errors='ignore')
print("Remaining columns:", df.columns.tolist())
display(df.head())
```
8. Fixing the price column and extracting only numeric values

```python
df['Price'] = df['Price'].astype(str).str.replace('à§³', '', regex=False).str.strip()
df['Price'] = pd.to_numeric(df['Price'], errors='coerce')
print("Cleaned Price Head:")
print(df['Price'].head())
df.info()
```
9. Download the updated csv dataset

```python
from google.colab import files
# Save the dataframe to a csv file
df.to_csv('cleaned_daraz_data.csv', index=False)
# Trigger the download to your local machine
files.download('cleaned_daraz_data.csv')
```
