# daraz-tshirt-sales-analytics
Daraz T‑Shirt Sales Analytics — CSV (3,280 rows, 9 cols). Reproducible analyses using SQL and Python: data cleaning, EDA, SQL queries, and a short report extracting pricing, discount, sales‑volume, SKU and seller insights. Includes scripts and instructions to reproduce results.

**Handling Missing Values using python**

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
