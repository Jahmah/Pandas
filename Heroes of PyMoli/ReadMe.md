
#Import Pandas Library
import pandas as pd


```python
# Store filepath in a variable
file_one = "C://Users/Nii/Homework/Pandas/purchase_data.json"
```


```python
#Read the Data file with the pandas library and create a Data frame
purchases_df = pd.read_json(file_one)

#view a snapshot of the Data frame
purchases_df.head(2)

```


```python
#Sum the unique player names
number_players = len(purchases_df["SN"].unique())
total_num_players = pd.DataFrame({"Total Players":[number_players]})
total_num_players
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>




```python
#sum the unique list of Items
items_count = len(purchases_df["Item ID"].unique())
items_count_df = pd.DataFrame({"Number of Unique Items":[items_count]})

#calculate the average price
average_price = purchases_df["Price"].mean()
average_price_df = pd.DataFrame({"Average Price":["$" + str(round(average_price,2))]})

#Total number of purchases
total_purchases = purchases_df["Item Name"].count()
total_purchases_df = pd.DataFrame({"Number of Purchases": [total_purchases]})

#Total revenue
total_revenue = purchases_df["Price"].sum()
total_rev_df = pd.DataFrame({"Total Revenue": [ "$" + str(total_revenue)]})

#Purchases Summary Table
purchases_summary_df = pd.concat([items_count_df, average_price_df, total_purchases_df, total_rev_df], axis = 1)
purchases_summary_df


```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Group data by Gender
grouped_gender = purchases_df.groupby(["Gender"])

#Filter out duplicates and null values
grouped_unique_gender = grouped_gender.nunique()

#Group Gender by number of players
grouped_gender_df = grouped_unique_gender["SN"]

#Calculate gender percentages
gender_percentage = (grouped_unique_gender["SN"]/number_players).map("{:,.2%}".format)

#Gender Demographics table
gender_summary_table = pd.DataFrame({"Percentage of Players": gender_percentage, "Total Count": grouped_gender_df})
gender_summary_table
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>17.45%</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>81.15%</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40%</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Calculate purchase count, average price and total purchase value by gender
gender_purchases_count = (purchases_df.groupby(["Gender"]).count()["Price"]).map("{:,}".format)
gender_purchases_average = (purchases_df.groupby(["Gender"]).mean()["Price"]).map("${:,.2f}".format)
gender_purchases_total = (purchases_df.groupby(["Gender"]).sum()["Price"]).map("${:,.2f}".format)

#Calculate Normalized total
normalized_totals = (purchases_df.groupby(["Gender"]).sum()["Price"] / gender_summary_table["Total Count"]).map("${:,.2f}".format)
                                                                               
#Purchases by Gender summary table
purchases_gender_summary_table = pd.DataFrame({"Purchases Count": gender_purchases_count, "Average Purchase Price": gender_purchases_average, "Total Purchase Value": gender_purchases_total, "Normalized Totals": normalized_totals})
purchases_gender_summary_table

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchases Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1,867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Calculate the demographics of Unique Players
players_demographics = purchases_df[["SN","Gender","Age"]]
unique_players_demographics = players_demographics.drop_duplicates()

# Create the bins to hold the Data 
bins = [0,9,14,19,24,29,34,39,100]
#Create names for the bins
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]

#Group players into age groups
unique_players_demographics["Age Groups"] = pd.cut(unique_players_demographics["Age"], bins, labels = group_names)

# Calculate player Totals and Percentages by Age Group
age_demographics_players_totals = unique_players_demographics["Age Groups"].value_counts()
age_demographics_players_percents = (age_demographics_players_totals / number_players).map("{:,.2%}".format)

#Create a data frame of results
age_demographics_summary = pd.DataFrame({"Percentage of Players": age_demographics_players_percents,"Total Count": age_demographics_players_totals})

#Sort the summary table by ascending order
age_demographics_summary.sort_index()
```

    C:\Users\Nii\Anaconda3\envs\PythonData\lib\site-packages\ipykernel_launcher.py:11: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      # This is added back by InteractiveShellApp.init_path()
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>3.32%</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.01%</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>17.45%</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>45.20%</td>
      <td>259</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>15.18%</td>
      <td>87</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>8.20%</td>
      <td>47</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>4.71%</td>
      <td>27</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1.92%</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Put the Purchases Data into bins
purchases_df["Age Groups"] = pd.cut(purchases_df["Age"], bins, labels=group_names)
                                   
#Calculate purchase totals, count and average price by age group
purchases_totals_age = (purchases_df.groupby(["Age Groups"]).sum()["Price"]).map("${:,.2f}".format)
average_purchases_price = (purchases_df.groupby(["Age Groups"]).mean()["Price"]).map("${:,.2f}".format)
purchases_counts_age = purchases_df.groupby(["Age Groups"]).count()["Price"]

# Calculate Normalized Purchasing
normalized_totals_age = (purchases_df.groupby(["Age Groups"]).sum()["Price"] / age_demographics_summary["Total Count"]).map("${:,.2f}".format)

#Create a data frame of results
purchases_age_summary = pd.DataFrame({"Purchase Count": purchases_counts_age, "Average Purchase Price": average_purchases_price, "Total Purchase Value": purchases_totals_age, "Normalized Totals": normalized_totals_age})

purchases_age_summary.sort_index()
                                
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$4.39</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Calculate value of Total purchases, average purchase price and count of purchases by players
total_purchases_player = (purchases_df.groupby(["SN"]).sum()["Price"]).map("${:,.2f}".format)
average_purchase_price_player = (purchases_df.groupby(["SN"]).mean()["Price"]).map("${:,.2f}".format)
purchase_count_player = purchases_df.groupby(["SN"]).count()["Price"]

# Create a DataFrame
player_purchases_summary = pd.DataFrame({"Purchase Count": purchase_count_player, "Average Purchase Price":average_purchase_price_player,"Total Purchase Value": total_purchases_player })

# Sort table by Total Purchase Value 
player_purchases_summary.sort_values("Total Purchase Value", ascending=False).head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Qarwen67</th>
      <td>4</td>
      <td>$2.49</td>
      <td>$9.97</td>
    </tr>
    <tr>
      <th>Sondim43</th>
      <td>3</td>
      <td>$3.13</td>
      <td>$9.38</td>
    </tr>
    <tr>
      <th>Tillyrin30</th>
      <td>3</td>
      <td>$3.06</td>
      <td>$9.19</td>
    </tr>
    <tr>
      <th>Lisistaya47</th>
      <td>3</td>
      <td>$3.06</td>
      <td>$9.19</td>
    </tr>
    <tr>
      <th>Tyisriphos58</th>
      <td>2</td>
      <td>$4.59</td>
      <td>$9.18</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Extract Data on items
items_players = purchases_df.loc[:,["Item ID", "Item Name", "Price"]]

#Calculate total purchases, average price and count for each item
total_item_purchases = (items_players.groupby(["Item ID", "Item Name"]).sum()["Price"]).map("${:,.2f}".format)
average_item_purchases = (items_players.groupby(["Item ID", "Item Name"]).mean()["Price"]).map("${:,.2f}".format)
total_item_count = (items_players.groupby(["Item ID", "Item Name"]).count()["Price"])

#Create a data frame
item_players_summary = pd.DataFrame({"Purchase Count": total_item_count, "Item Price": average_item_purchases,"Total Purchase Value": total_item_purchases})

# Sort table by Purchase count 
item_players_summary.sort_values("Purchase Count", ascending=False).head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Calculate total purchases, average price and count for each item
total_item_profitable = (items_players.groupby(["Item ID", "Item Name"]).sum()["Price"])
average_item_profitable = (items_players.groupby(["Item ID", "Item Name"]).mean()["Price"])
profitable_item_count = (items_players.groupby(["Item ID", "Item Name"]).count()["Price"])

#Create a data frame
item_profitable_summary = pd.DataFrame({"Purchase Count": profitable_item_count, "Item Price": average_item_profitable,"Total Purchase Value":total_item_profitable})

# Sort summary by Total Purchase Value
sorted_summary = item_profitable_summary.sort_values("Total Purchase Value", ascending=False)

#Format Columns
sorted_summary["Total Purchase Value"] = sorted_summary["Total Purchase Value"].map("${:,.2f}".format)
sorted_summary["Item Price"] = sorted_summary["Item Price"].map("${:,.2f}".format)

sorted_summary.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


