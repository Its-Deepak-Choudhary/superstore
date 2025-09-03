---

# Superstore Sales Exploratory Data Analysis (EDA)

This repository contains an exploratory data analysis (EDA) on a Superstore Sales dataset spanning from 2015 to 2019. The goal is to analyze sales data by customers, states, cities, and product categories, visualizing the highest revenue generators.

---

## Libraries Used

```python
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib.ticker as ticker
import seaborn as sns
import numpy as np
```

---

## Loading and Inspecting the Dataset

```python
df = pd.read_csv('train.csv')
```
![](https://github.com/Its-Deepak-Choudhary/superstore/blob/master/images/Load_csv.png)
# Basic data info
```
print(df.shape)
```
![](https://github.com/Its-Deepak-Choudhary/superstore/blob/master/images/shape.png)
```
df.info()
```
![](https://github.com/Its-Deepak-Choudhary/superstore/blob/master/images/info.png)
```
print(df.columns)
```
![](https://github.com/Its-Deepak-Choudhary/superstore/blob/master/images/columns.png)
```
print(df.index)
```
![](https://github.com/Its-Deepak-Choudhary/superstore/blob/master/images/index.png)
```
print(df.dtypes)
```
![](https://github.com/Its-Deepak-Choudhary/superstore/blob/master/images/dtypes.png)
```
print(df.head())
```
![](https://github.com/Its-Deepak-Choudhary/superstore/blob/master/images/head.png)
```
print(df.describe())
```
![](https://github.com/Its-Deepak-Choudhary/superstore/blob/master/images/describe.png)

---

## Data Cleaning and Preprocessing

* Dropped the 'Row ID' column.
* Rounded Sales values to 2 decimals.
* Converted `Order Date` and `Ship Date` to datetime format.
* Filled missing postal codes based on city mappings.

```python
df.drop('Row ID', axis=1, inplace=True)
df['Sales'] = df['Sales'].round(2)

df['Order Date'] = pd.to_datetime(df['Order Date'], format='%d-%m-%Y')
df['Ship Date'] = pd.to_datetime(df['Ship Date'], format='%d-%m-%Y')

# Handling missing postal codes
city_postal_map = {
    'Burlington': 5401,
    'New York': 10001,
    'Los Angeles': 90001,
    'Chicago': 60601,
}

def fill_postal_code(row):
    if pd.isnull(row['Postal Code']):
        return city_postal_map.get(row['City'], np.nan)
    return row['Postal Code']

df['Postal Code'] = df.apply(fill_postal_code, axis=1)
```

---

## Analysis & Visualization

### Most Valuable Customers

```python
Top_Customer = df.groupby('Customer Name').sum(numeric_only=True).sort_values('Sales', ascending=False).head(20)
Top_Customer = Top_Customer[['Sales']].round(2).reset_index()

plt.figure(figsize=(13, 6))
plt.title('Most Valuable Customers (2015-2019)', fontsize=18, color='blue', fontweight='bold')
plt.bar(Top_Customer["Customer Name"], Top_Customer["Sales"], color="#9C9208", edgecolor='green', linewidth=1)
plt.xlabel('Customers', fontsize=15, color='blue', fontweight='bold')
plt.ylabel('Revenue', fontsize=15, color='b')
plt.xticks(fontsize=10, rotation=90, color='black')
plt.yticks(fontsize=10)

for i, v in enumerate(Top_Customer['Sales']):
    plt.text(i, v - 8000, f'₹{v:,.0f}', fontsize=12, rotation=90, color='w', horizontalalignment='center')

plt.tight_layout()
plt.show()
```

### Top States by Revenue

```python
Top_states = df.groupby('State')['Sales'].sum().sort_values(ascending=False).head(20)
Top_states = Top_states.round(2).reset_index()

plt.figure(figsize=(13,5))
plt.title('States which generated Highest Revenue (2015-2019)', fontsize=18, color='Red', fontweight='bold')
plt.bar(Top_states['State'], Top_states['Sales'], color='#E8860E', edgecolor='red', linewidth=1)
plt.xlabel('States', fontsize=15, fontweight='bold', color='red')
plt.ylabel('Revenue', fontsize=15, fontweight='bold')
plt.xticks(fontsize=10, rotation=90, color="#e005bc")
plt.yticks(fontsize=10, rotation=90)

for k, v in Top_states['Sales'].items():
    if v > 400000:
        plt.text(k, v-150000, '₹' + str(v), fontsize=10, color='k', horizontalalignment='center', rotation=90)
    else:
        plt.text(k, v+1500, '₹' + str(v), fontsize=10, color='k', horizontalalignment='center', rotation=90)

plt.show()
```

### Top Cities by Revenue

```python
Top_city = df.groupby('City')['Sales'].sum().sort_values(ascending=False).head(20)
Top_city = Top_city.round(2).reset_index()

plt.figure(figsize=(15,5))
plt.title('Which cities generated highest revenue (2015-2019)', fontsize=15, color='purple')
plt.bar(Top_city['City'], Top_city['Sales'], color="#09F1A4", edgecolor="#010A06E8", linewidth=1)
plt.xlabel('Cities', fontsize=12, fontweight='bold', color="#DF0660")
plt.ylabel('Revenue', fontsize=12, fontweight='bold', color="#26d104")
plt.xticks(fontsize=10, rotation=90)
plt.yticks(fontsize=10, rotation=90)

for k, v in Top_city['Sales'].items():
    if v > 250000:
        plt.text(k, v - 75000, '₹' + str(v), fontsize=10, rotation=90, color='k', horizontalalignment='center')
    else:
        plt.text(k, v + 15000, '₹' + str(v), fontsize=10, rotation=90, color='k', horizontalalignment='center')

plt.show()
```

### Revenue by Category - Donut Chart

```python
Category_wise = df.groupby('Category')['Sales'].sum().sort_values(ascending=False)
Category_wise = Category_wise.round(2).reset_index()

colors = ["#E00909", "#DB0D9D", "#00FF0D"]
explode = [0.01] * len(Category_wise)

plt.figure(figsize=(8, 5))
plt.title('Categories Wise Revenue', fontsize=12, color="#ee790c", fontweight='bold')

plt.pie(
    Category_wise['Sales'],
    labels=Category_wise['Category'],
    colors=colors,
    autopct='%1.1f%%',
    explode=explode,
    startangle=90,
    wedgeprops={'width': 0.8},  # Makes it a donut
    pctdistance=0.85,
)

centre_circle = plt.Circle((0, 0), 0.7, fc='white')
plt.gca().add_artist(centre_circle)
plt.tight_layout()
plt.show()
```

---

## Conclusion

This EDA provided valuable insights into the Superstore sales data, highlighting the top customers, states, cities, and product categories generating the most revenue. The visualizations help identify key areas for business focus and potential growth opportunities.

---

