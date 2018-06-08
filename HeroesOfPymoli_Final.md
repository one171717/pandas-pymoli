
### Heroes Of Pymoli Data Analysis
* There are 576 total players, the vast majority of which are male, making up 84% of the player base.
* Though the female players only make up 14% of the total players, the average purchase price is higher.
* The 20-24 age bracket makes up almost 50% of the total player count. When we consider 76% of our player base is contained within ages 15-29, it is obvious who the target audience is. 

-----


```python
# Dependencies and Setup
import pandas as pd
import numpy as np

# Raw data file
csvpath = "Resources/purchase_data.csv"

# Read purchasing file and store into pandas data frame
purchase_data = pd.read_csv(csvpath)
```

## Player Count

* Display the total number of players



```python
player_count = len(purchase_data["SN"].value_counts())
pd.DataFrame([player_count], columns = ["Total Players"])

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
      <td>576</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Total)

* Run basic calculations to obtain number of unique items, average price, etc.


* Create a summary data frame to hold the results


* Optional: give the displayed data cleaner formatting


* Display the summary data frame



```python
# Finding & Appending Number of Unique Items
unique_items = len(purchase_data["Item ID"].value_counts())

# Total money spent
total_revenue = round(purchase_data["Price"].sum(), 2)

# Number of purchases
num_purchases = purchase_data["Price"].count()

# Average Price
average_price = round(purchase_data["Price"].mean(), 2)

# List for analysis
purchase_analysis = []

# Appending values to list
purchase_analysis.append(unique_items)
purchase_analysis.append("$" + str(average_price))
purchase_analysis.append(num_purchases)
purchase_analysis.append("$" + str(total_revenue))

# Converting to a DataFrame
pd.DataFrame([purchase_analysis], columns = ["Number of Unique Items", "Average Price", "Number of Purchases", "Total Revenue"])


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
      <td>$3.05</td>
      <td>780</td>
      <td>$2379.77</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics

* Run basic calculations to obtain number of unique items, average price, etc.


* Create a summary data frame to hold the results


* Optional: give the displayed data cleaner formatting


* Display the summary data frame



```python
# Grouping by gender & counting
gender_grouped = purchase_data[["SN", "Gender"]]
gender_grouped = gender_grouped.drop_duplicates()
counts = gender_grouped["Gender"].value_counts()

# List of values
total_counts = [counts[0],counts[1],counts[2]]
percents_gender = [round((counts[0]/player_count)*100,2),round((counts[1]/player_count)*100,2),round((counts[2]/player_count)*100,2)]

# Creating DataFrame & setting index
gender_df = pd.DataFrame({
    "Percentage of Players": percents_gender,
    "Total Count": total_counts
})
gender_df.index = (["Male", "Female", "Other / Non-Disclosed"])
gender_df

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
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>84.03</td>
      <td>484</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>14.06</td>
      <td>81</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.91</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>




## Purchasing Analysis (Gender)

* Run basic calculations to obtain purchase count, avg. purchase price, etc. by gender


* For normalized purchasing, divide total purchase value by purchase count, by gender


* Create a summary data frame to hold the results


* Optional: give the displayed data cleaner formatting


* Display the summary data frame


```python
gender_grouped = purchase_data[["SN","Gender","Price"]]
counts_gender = gender_grouped["Gender"].value_counts()

# Purchase counts
purchase_counts = [counts_gender[0],counts_gender[1],counts_gender[2]]

gender_grouped = gender_grouped.groupby("Gender")
total_spent = gender_grouped.sum()
total_spent

# Total Purchase Value
total_purchase_value_gender = [total_spent.iloc[1,0], total_spent.iloc[0,0], total_spent.iloc[2,0]]

# Average Purchase Price
avg_purchase_gender = [total_spent.iloc[1,0]/counts_gender[0], total_spent.iloc[0,0]/counts_gender[1], total_spent.iloc[2,0]/counts_gender[2]]

# Normalized Totals
norm_tot_gender = [total_spent.iloc[1,0]/counts[0], total_spent.iloc[0,0]/counts[1], total_spent.iloc[2,0]/counts[2]]

# Creating DataFrame & setting index
purchase_analysis_gender_df = pd.DataFrame({
    "Purchase Count": purchase_counts,
    "Average Purchase Price": avg_purchase_gender,
    "Total Purchase Value": total_purchase_value_gender,
    "Normalized Totals": norm_tot_gender,
    "Gender": ["Male", "Female", "Other / Non-Disclosed"]
})
purchase_analysis_gender_df = purchase_analysis_gender_df.set_index("Gender")
purchase_analysis_gender_df = purchase_analysis_gender_df[["Purchase Count", "Average Purchase Price", "Total Purchase Value",
                                                 "Normalized Totals"]]

# Formatting Prices
purchase_analysis_gender_df.style.format({"Average Purchase Price": "${:.2f}", "Normalized Totals": "${:.2f}",
                                     "Total Purchase Value":"${:.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Purchase Count</th> 
        <th class="col_heading level0 col1" >Average Purchase Price</th> 
        <th class="col_heading level0 col2" >Total Purchase Value</th> 
        <th class="col_heading level0 col3" >Normalized Totals</th> 
    </tr>    <tr> 
        <th class="index_name level0" >Gender</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8level0_row0" class="row_heading level0 row0" >Male</th> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row0_col0" class="data row0 col0" >652</td> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row0_col1" class="data row0 col1" >$3.02</td> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row0_col2" class="data row0 col2" >$1967.64</td> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row0_col3" class="data row0 col3" >$4.07</td> 
    </tr>    <tr> 
        <th id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8level0_row1" class="row_heading level0 row1" >Female</th> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row1_col0" class="data row1 col0" >113</td> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row1_col1" class="data row1 col1" >$3.20</td> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row1_col2" class="data row1 col2" >$361.94</td> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row1_col3" class="data row1 col3" >$4.47</td> 
    </tr>    <tr> 
        <th id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8level0_row2" class="row_heading level0 row2" >Other / Non-Disclosed</th> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row2_col0" class="data row2 col0" >15</td> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row2_col1" class="data row2 col1" >$3.35</td> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row2_col2" class="data row2 col2" >$50.19</td> 
        <td id="T_89c969a4_6ac7_11e8_ad09_a8667f358ca8row2_col3" class="data row2 col3" >$4.56</td> 
    </tr></tbody> 
</table> 



## Age Demographics

* Establish bins for ages


* Categorize the existing players using the age bins. Hint: use pd.cut()


* Calculate the numbers and percentages by age group


* Create a summary data frame to hold the results


* Optional: round the percentage column to two decimal points


* Display Age Demographics Table



```python
# Establish bins for ages
age_bins = [0, 9.90, 14.90, 19.90, 24.90, 29.90, 34.90, 39.90, 99999]
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]

purchase_data_2 = purchase_data[["SN","Age"]]
purchase_data_2 = purchase_data_2.drop_duplicates()

# Age count
age_10 = purchase_data_2[purchase_data_2["Age"] < 10].count()[0]
age_14 = purchase_data_2[(purchase_data_2["Age"] >= 10) & (purchase_data_2["Age"] <= 14)].count()[0]
age_19 = purchase_data_2[(purchase_data_2["Age"] >= 15) & (purchase_data_2["Age"] <= 19)].count()[0]
age_24 = purchase_data_2[(purchase_data_2["Age"] >= 20) & (purchase_data_2["Age"] <= 24)].count()[0]
age_29 = purchase_data_2[(purchase_data_2["Age"] >= 25) & (purchase_data_2["Age"] <= 29)].count()[0]
age_34 = purchase_data_2[(purchase_data_2["Age"] >= 30) & (purchase_data_2["Age"] <= 34)].count()[0]
age_39 = purchase_data_2[(purchase_data_2["Age"] >= 35) & (purchase_data_2["Age"] <= 39)].count()[0]
age_40 = purchase_data_2[purchase_data_2["Age"] >= 40].count()[0]
ages = [age_10, age_14, age_19, age_24, age_29, age_34, age_39, age_40]

# Percents
percent_10 = round((age_10/player_count)*100,2)
percent_14 = round((age_14/player_count)*100,2)
percent_19 = round((age_19/player_count)*100,2)
percent_24 = round((age_24/player_count)*100,2)
percent_29 = round((age_29/player_count)*100,2)
percent_34 = round((age_34/player_count)*100,2)
percent_39 = round((age_39/player_count)*100,2)
percent_40 = round((age_40/player_count)*100,2)
percents_a = [percent_10, percent_14, percent_19, percent_24, percent_29, percent_34, percent_39, percent_40]

# Creating the dictionary
age_demographic = {
        "Percent of Players": percents_a,
        "Total Count": ages
    }
    
# Creating DataFrame & setting index
age_demographic_df = pd.DataFrame(age_demographic)
age_demographic_df.index = (["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"])
age_demographic_df

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
      <th>Percent of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>2.95</td>
      <td>17</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>3.82</td>
      <td>22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>18.58</td>
      <td>107</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>44.79</td>
      <td>258</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>13.37</td>
      <td>77</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>9.03</td>
      <td>52</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>5.38</td>
      <td>31</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>2.08</td>
      <td>12</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Age)

* Bin the purchase_data data frame by age


* Run basic calculations to obtain purchase count, avg. purchase price, etc. in the table below


* Calculate Normalized Purchasing


* Create a summary data frame to hold the results


* Optional: give the displayed data cleaner formatting


* Display the summary data frame


```python
# Purchase Count
purchase_10 = purchase_data[purchase_data["Age"] < 10].count()[0]
purchase_14 = purchase_data[(purchase_data["Age"] >= 10) & (purchase_data["Age"] <= 14)].count()[0]
purchase_19 = purchase_data[(purchase_data["Age"] >= 15) & (purchase_data["Age"] <= 19)].count()[0]
purchase_24 = purchase_data[(purchase_data["Age"] >= 20) & (purchase_data["Age"] <= 24)].count()[0]
purchase_29 = purchase_data[(purchase_data["Age"] >= 25) & (purchase_data["Age"] <= 29)].count()[0]
purchase_34 = purchase_data[(purchase_data["Age"] >= 30) & (purchase_data["Age"] <= 34)].count()[0]
purchase_39 = purchase_data[(purchase_data["Age"] >= 35) & (purchase_data["Age"] <= 39)].count()[0]
purchase_40 = purchase_data[purchase_data["Age"] >= 40].count()[0]
purchases_a = [purchase_10, purchase_14, purchase_19, purchase_24, purchase_29, purchase_34, purchase_39, purchase_40]

# Total Purchase Value
total_10 = purchase_data.loc[purchase_data['Age'] < 10, 'Price'].sum()
total_14 = purchase_data.loc[(purchase_data['Age'] >= 10) & (purchase_data['Age'] <=14), 'Price'].sum()
total_19 = purchase_data.loc[(purchase_data['Age'] >= 15) & (purchase_data['Age'] <=19), 'Price'].sum()
total_24 = purchase_data.loc[(purchase_data['Age'] >= 20) & (purchase_data['Age'] <=24), 'Price'].sum()
total_29 = purchase_data.loc[(purchase_data['Age'] >= 25) & (purchase_data['Age'] <=29), 'Price'].sum()
total_34 = purchase_data.loc[(purchase_data['Age'] >= 30) & (purchase_data['Age'] <=34), 'Price'].sum()
total_39 = purchase_data.loc[(purchase_data['Age'] >= 35) & (purchase_data['Age'] <=39), 'Price'].sum()
total_40 = purchase_data.loc[purchase_data['Age'] >= 40, 'Price'].sum()
totals_a = [total_10, total_14, total_19, total_24, total_29, total_34, total_39, total_40]

# Average Purchase Price
avg_price_a = [total_10/purchase_10, total_14/purchase_14, total_19/purchase_19, total_24/purchase_24, total_29/purchase_29,
              total_34/purchase_34, total_39/purchase_39, total_40/purchase_40]

# Normalized Totals
norms_a = [total_10/age_10, total_14/age_14, total_19/age_19, total_24/age_24, total_29/age_29, total_34/age_34,
           total_39/age_39, total_40/age_40]

# Creating dictionary
puchase_analysis_a = {
    "Purchase Count": purchases_a,
    "Average Purchase Price": avg_price_a,
    "Total Purchase Value": totals_a,
    "Normalized Totals": norms_a
}

# Creating DataFrame & setting index
purchase_analysis_a_df = pd.DataFrame(puchase_analysis_a)
purchase_analysis_a_df = purchase_analysis_a_df[['Purchase Count', 'Average Purchase Price', 'Total Purchase Value',
                                                 'Normalized Totals']]
purchase_analysis_a_df.index = (["<10", "10-14","15-19","20-24","25-29","30-34","34-39","40+"])

# Formatting Prices
purchase_analysis_a_df.style.format({"Average Purchase Price": "${:.2f}", "Normalized Totals": "${:.2f}",
                                     "Total Purchase Value":"${:.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Purchase Count</th> 
        <th class="col_heading level0 col1" >Average Purchase Price</th> 
        <th class="col_heading level0 col2" >Total Purchase Value</th> 
        <th class="col_heading level0 col3" >Normalized Totals</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8level0_row0" class="row_heading level0 row0" ><10</th> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row0_col0" class="data row0 col0" >23</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row0_col1" class="data row0 col1" >$3.35</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row0_col2" class="data row0 col2" >$77.13</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row0_col3" class="data row0 col3" >$4.54</td> 
    </tr>    <tr> 
        <th id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8level0_row1" class="row_heading level0 row1" >10-14</th> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row1_col0" class="data row1 col0" >28</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row1_col1" class="data row1 col1" >$2.96</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row1_col2" class="data row1 col2" >$82.78</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row1_col3" class="data row1 col3" >$3.76</td> 
    </tr>    <tr> 
        <th id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8level0_row2" class="row_heading level0 row2" >15-19</th> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row2_col0" class="data row2 col0" >136</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row2_col1" class="data row2 col1" >$3.04</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row2_col2" class="data row2 col2" >$412.89</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row2_col3" class="data row2 col3" >$3.86</td> 
    </tr>    <tr> 
        <th id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8level0_row3" class="row_heading level0 row3" >20-24</th> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row3_col0" class="data row3 col0" >365</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row3_col1" class="data row3 col1" >$3.05</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row3_col2" class="data row3 col2" >$1114.06</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row3_col3" class="data row3 col3" >$4.32</td> 
    </tr>    <tr> 
        <th id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8level0_row4" class="row_heading level0 row4" >25-29</th> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row4_col0" class="data row4 col0" >101</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row4_col1" class="data row4 col1" >$2.90</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row4_col2" class="data row4 col2" >$293.00</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row4_col3" class="data row4 col3" >$3.81</td> 
    </tr>    <tr> 
        <th id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8level0_row5" class="row_heading level0 row5" >30-34</th> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row5_col0" class="data row5 col0" >73</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row5_col1" class="data row5 col1" >$2.93</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row5_col2" class="data row5 col2" >$214.00</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row5_col3" class="data row5 col3" >$4.12</td> 
    </tr>    <tr> 
        <th id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8level0_row6" class="row_heading level0 row6" >34-39</th> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row6_col0" class="data row6 col0" >41</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row6_col1" class="data row6 col1" >$3.60</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row6_col2" class="data row6 col2" >$147.67</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row6_col3" class="data row6 col3" >$4.76</td> 
    </tr>    <tr> 
        <th id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8level0_row7" class="row_heading level0 row7" >40+</th> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row7_col0" class="data row7 col0" >13</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row7_col1" class="data row7 col1" >$2.94</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row7_col2" class="data row7 col2" >$38.24</td> 
        <td id="T_8ec9d650_6ac7_11e8_88ea_a8667f358ca8row7_col3" class="data row7 col3" >$3.19</td> 
    </tr></tbody> 
</table> 



## Top Spenders

* Run basic calculations to obtain the results in the table below


* Create a summary data frame to hold the results


* Sort the total purchase value column in descending order


* Optional: give the displayed data cleaner formatting


* Display a preview of the summary data frame




```python
purchase_data_3 = purchase_data[["SN","Price","Item Name"]]
total_spent = purchase_data_3.groupby("SN").sum()
total_spent.sort_values(by = "Price", ascending = False, inplace = True)

# Top Spender SN
names = list(total_spent.index.values)
top_names = [names[0],names[1],names[2],names[3],names[4]]

# Total Purchase Values
total_purchase_values_1 = total_spent.iloc[0,0]
total_purchase_values_2 = total_spent.iloc[1,0]
total_purchase_values_3 = total_spent.iloc[2,0]
total_purchase_values_4 = total_spent.iloc[3,0]
total_purchase_values_5 = total_spent.iloc[4,0]
top_purchase_values = [total_spent.iloc[0,0], total_spent.iloc[1,0], total_spent.iloc[2,0], total_spent.iloc[3,0],
                      total_spent.iloc[4,0]]

# Purchase Counts
top_purchase_counts_1 = purchase_data_3[purchase_data_3["SN"] == names[0]].count()[0]
top_purchase_counts_2 = purchase_data_3[purchase_data_3["SN"] == names[1]].count()[0]
top_purchase_counts_3 = purchase_data_3[purchase_data_3["SN"] == names[2]].count()[0]
top_purchase_counts_4 = purchase_data_3[purchase_data_3["SN"] == names[3]].count()[0]
top_purchase_counts_5 = purchase_data_3[purchase_data_3["SN"] == names[4]].count()[0]
top_purchase_counts = [top_purchase_counts_1, top_purchase_counts_2, top_purchase_counts_3, top_purchase_counts_4,
                       top_purchase_counts_5]

# Average Purchas Prices
avg_price_1 = total_purchase_values_1/top_purchase_counts_1
avg_price_2 = total_purchase_values_2/top_purchase_counts_2
avg_price_3 = total_purchase_values_3/top_purchase_counts_3
avg_price_4 = total_purchase_values_4/top_purchase_counts_4
avg_price_5 = total_purchase_values_5/top_purchase_counts_5
avg_prices = [avg_price_1, avg_price_2, avg_price_3, avg_price_4, avg_price_5]

# Dictionary of values
top_spenders_dict = {
    "Purchase Count": top_purchase_counts,
    "Average Purchase Price": avg_prices,
    "Total Purchase Value": top_purchase_values,
    "SN": top_names
}

# Creating DataFrame & setting index
top_spenders_df = pd.DataFrame(top_spenders_dict)
top_spenders_df = top_spenders_df.set_index("SN")
top_spenders_df = top_spenders_df[["Purchase Count", "Average Purchase Price", "Total Purchase Value"]]

# Formatting prices
top_spenders_df.style.format({"Average Purchase Price": "${:.2f}", "Total Purchase Value": "${:.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Purchase Count</th> 
        <th class="col_heading level0 col1" >Average Purchase Price</th> 
        <th class="col_heading level0 col2" >Total Purchase Value</th> 
    </tr>    <tr> 
        <th class="index_name level0" >SN</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8level0_row0" class="row_heading level0 row0" >Lisosia93</th> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row0_col0" class="data row0 col0" >5</td> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row0_col1" class="data row0 col1" >$3.79</td> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row0_col2" class="data row0 col2" >$18.96</td> 
    </tr>    <tr> 
        <th id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8level0_row1" class="row_heading level0 row1" >Idastidru52</th> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row1_col0" class="data row1 col0" >4</td> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row1_col1" class="data row1 col1" >$3.86</td> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row1_col2" class="data row1 col2" >$15.45</td> 
    </tr>    <tr> 
        <th id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8level0_row2" class="row_heading level0 row2" >Chamjask73</th> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row2_col0" class="data row2 col0" >3</td> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row2_col1" class="data row2 col1" >$4.61</td> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row2_col2" class="data row2 col2" >$13.83</td> 
    </tr>    <tr> 
        <th id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8level0_row3" class="row_heading level0 row3" >Iral74</th> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row3_col0" class="data row3 col0" >4</td> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row3_col1" class="data row3 col1" >$3.40</td> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row3_col2" class="data row3 col2" >$13.62</td> 
    </tr>    <tr> 
        <th id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8level0_row4" class="row_heading level0 row4" >Iskadarya95</th> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row4_col0" class="data row4 col0" >3</td> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row4_col1" class="data row4 col1" >$4.37</td> 
        <td id="T_90965a46_6ac7_11e8_8c10_a8667f358ca8row4_col2" class="data row4 col2" >$13.10</td> 
    </tr></tbody> 
</table> 



## Most Popular Items

* Retrieve the Item ID, Item Name, and Item Price columns


* Group by Item ID and Item Name. Perform calculations to obtain purchase count, item price, and total purchase value


* Create a summary data frame to hold the results


* Sort the purchase count column in descending order


* Optional: give the displayed data cleaner formatting


* Display a preview of the summary data frame




```python
purchase_data_4 = purchase_data[["Item ID", "Item Name", "Price"]]
pop_items = purchase_data_4.groupby("Item ID").count()
pop_items.sort_values(by = "Item Name", ascending = False, inplace = True)
purchase_data_4 = purchase_data_4.drop_duplicates(["Item ID", "Item Name"])

# Item IDs
item_ids = [pop_items.index[0], pop_items.index[1], pop_items.index[2], pop_items.index[3], pop_items.index[4]]

# Item Names
name_1 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[0], "Item Name"].item()
name_2 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[1], "Item Name"].item()
name_3 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[2], "Item Name"].item()
name_4 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[3], "Item Name"].item()
name_5 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[4], "Item Name"].item()
pop_item_names = [name_1, name_2, name_3, name_4, name_5]

# Purchase Counts
item_counts = [pop_items.iloc[0,0], pop_items.iloc[1,0], pop_items.iloc[2,0], pop_items.iloc[3,0], pop_items.iloc[4,0]]

# Item Prices
price_1 = purchase_data_4.loc[purchase_data_4["Item Name"] == pop_item_names[0], "Price"].item()
price_2 = purchase_data_4.loc[purchase_data_4["Item Name"] == pop_item_names[1], "Price"].item()
price_3 = purchase_data_4.loc[purchase_data_4["Item Name"] == pop_item_names[2], "Price"].item()
price_4 = purchase_data_4.loc[purchase_data_4["Item Name"] == pop_item_names[3], "Price"].item()
price_5 = purchase_data_4.loc[purchase_data_4["Item Name"] == pop_item_names[4], "Price"].item()
item_prices = [price_1,price_2,price_3,price_4,price_5]

# Total Purchase Value
total_values = [pop_items.iloc[0,0]*price_1, pop_items.iloc[1,0]*price_2, pop_items.iloc[2,0]*price_3, 
                pop_items.iloc[3,0]*price_4, pop_items.iloc[4,0]*price_5]

# Creating DataFrame & setting index
pop_items_df = pd.DataFrame({
    "Item ID": item_ids,
    "Item Name": pop_item_names,
    "Purchase Count": item_counts,
    "Item Price": item_prices,
    "Total Purchase Value": total_values
})
pop_items_df = pop_items_df.set_index(["Item ID", "Item Name"])
pop_items_df = pop_items_df[["Purchase Count", "Item Price", "Total Purchase Value"]]

# Formatting Prices
pop_items_df.style.format({"Item Price": "${:.2f}", "Total Purchase Value": "${:.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8" > 
<thead>    <tr> 
        <th class="blank" ></th> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Purchase Count</th> 
        <th class="col_heading level0 col1" >Item Price</th> 
        <th class="col_heading level0 col2" >Total Purchase Value</th> 
    </tr>    <tr> 
        <th class="index_name level0" >Item ID</th> 
        <th class="index_name level1" >Item Name</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8level0_row0" class="row_heading level0 row0" >178</th> 
        <th id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8level1_row0" class="row_heading level1 row0" >Oathbreaker, Last Hope of the Breaking Storm</th> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row0_col0" class="data row0 col0" >12</td> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row0_col1" class="data row0 col1" >$4.23</td> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row0_col2" class="data row0 col2" >$50.76</td> 
    </tr>    <tr> 
        <th id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8level0_row1" class="row_heading level0 row1" >145</th> 
        <th id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8level1_row1" class="row_heading level1 row1" >Fiery Glass Crusader</th> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row1_col0" class="data row1 col0" >9</td> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row1_col1" class="data row1 col1" >$4.58</td> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row1_col2" class="data row1 col2" >$41.22</td> 
    </tr>    <tr> 
        <th id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8level0_row2" class="row_heading level0 row2" >108</th> 
        <th id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8level1_row2" class="row_heading level1 row2" >Extraction, Quickblade Of Trembling Hands</th> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row2_col0" class="data row2 col0" >9</td> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row2_col1" class="data row2 col1" >$3.53</td> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row2_col2" class="data row2 col2" >$31.77</td> 
    </tr>    <tr> 
        <th id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8level0_row3" class="row_heading level0 row3" >82</th> 
        <th id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8level1_row3" class="row_heading level1 row3" >Nirvana</th> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row3_col0" class="data row3 col0" >9</td> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row3_col1" class="data row3 col1" >$4.90</td> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row3_col2" class="data row3 col2" >$44.10</td> 
    </tr>    <tr> 
        <th id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8level0_row4" class="row_heading level0 row4" >19</th> 
        <th id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8level1_row4" class="row_heading level1 row4" >Pursuit, Cudgel of Necromancy</th> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row4_col0" class="data row4 col0" >8</td> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row4_col1" class="data row4 col1" >$1.02</td> 
        <td id="T_9259a71e_6ac7_11e8_8062_a8667f358ca8row4_col2" class="data row4 col2" >$8.16</td> 
    </tr></tbody> 
</table> 



## Most Profitable Items

* Sort the above table by total purchase value in descending order


* Optional: give the displayed data cleaner formatting


* Display a preview of the data frame




```python
purchase_data_4 = purchase_data[["Item ID", "Item Name", "Price"]]
profit_items = purchase_data_4.groupby("Item ID").sum()
profit_items.sort_values(by = "Price", ascending = False, inplace = True)
purchase_data_4 = purchase_data_4.drop_duplicates(["Item ID", "Price"])

# Item IDs
item_ids = [profit_items.index[0], profit_items.index[1], profit_items.index[2], profit_items.index[3], profit_items.index[4]]

# Item Names
name_1 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[0], "Item Name"].item()
name_2 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[1], "Item Name"].item()
name_3 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[2], "Item Name"].item()
name_4 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[3], "Item Name"].item()
name_5 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[4], "Item Name"].item()
profit_names = [name_1, name_2, name_3, name_4, name_5]

# Total Purchase Value
values = [profit_items.iloc[0,0],profit_items.iloc[1,0],profit_items.iloc[2,0],profit_items.iloc[3,0],profit_items.iloc[4,0]]

# Item Price
price_1 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[0], "Price"].item()
price_2 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[1], "Price"].item()
price_3 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[2], "Price"].item()
price_4 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[3], "Price"].item()
price_5 = purchase_data_4.loc[purchase_data_4["Item ID"] == item_ids[4], "Price"].item()
profit_prices = [price_1,price_2,price_3,price_4,price_5]

# Purchase counts
purchase_data_5 = purchase_data[["Item ID", "Item Name", "Price"]].groupby("Item Name").count()
count_1 = purchase_data_5.loc[purchase_data_5.index == profit_names[0], "Item ID"].item()
count_2 = purchase_data_5.loc[purchase_data_5.index == profit_names[1], "Item ID"].item()
count_3 = purchase_data_5.loc[purchase_data_5.index == profit_names[2], "Item ID"].item()
count_4 = purchase_data_5.loc[purchase_data_5.index == profit_names[3], "Item ID"].item()
count_5 = purchase_data_5.loc[purchase_data_5.index == profit_names[4], "Item ID"].item()
counts = [count_1, count_2, count_3, count_4, count_5]

# Creating DataFrame & setting index
profit_items_df = pd.DataFrame({
    "Item ID": item_ids,
    "Item Name": profit_names,
    "Purchase Count": counts,
    "Item Price": profit_prices,
    "Total Purchase Value": values
})
profit_items_df = profit_items_df.set_index(["Item ID", "Item Name"])
profit_items_df = profit_items_df[["Purchase Count", "Item Price", "Total Purchase Value"]]

# Formatting prices
profit_items_df.style.format({"Item Price": "${:.2f}", "Total Purchase Value": "${:.2f}"})


```




<style  type="text/css" >
</style>  
<table id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8" > 
<thead>    <tr> 
        <th class="blank" ></th> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Purchase Count</th> 
        <th class="col_heading level0 col1" >Item Price</th> 
        <th class="col_heading level0 col2" >Total Purchase Value</th> 
    </tr>    <tr> 
        <th class="index_name level0" >Item ID</th> 
        <th class="index_name level1" >Item Name</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8level0_row0" class="row_heading level0 row0" >178</th> 
        <th id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8level1_row0" class="row_heading level1 row0" >Oathbreaker, Last Hope of the Breaking Storm</th> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row0_col0" class="data row0 col0" >12</td> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row0_col1" class="data row0 col1" >$4.23</td> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row0_col2" class="data row0 col2" >$50.76</td> 
    </tr>    <tr> 
        <th id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8level0_row1" class="row_heading level0 row1" >82</th> 
        <th id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8level1_row1" class="row_heading level1 row1" >Nirvana</th> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row1_col0" class="data row1 col0" >9</td> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row1_col1" class="data row1 col1" >$4.90</td> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row1_col2" class="data row1 col2" >$44.10</td> 
    </tr>    <tr> 
        <th id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8level0_row2" class="row_heading level0 row2" >145</th> 
        <th id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8level1_row2" class="row_heading level1 row2" >Fiery Glass Crusader</th> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row2_col0" class="data row2 col0" >9</td> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row2_col1" class="data row2 col1" >$4.58</td> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row2_col2" class="data row2 col2" >$41.22</td> 
    </tr>    <tr> 
        <th id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8level0_row3" class="row_heading level0 row3" >92</th> 
        <th id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8level1_row3" class="row_heading level1 row3" >Final Critic</th> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row3_col0" class="data row3 col0" >13</td> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row3_col1" class="data row3 col1" >$4.88</td> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row3_col2" class="data row3 col2" >$39.04</td> 
    </tr>    <tr> 
        <th id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8level0_row4" class="row_heading level0 row4" >103</th> 
        <th id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8level1_row4" class="row_heading level1 row4" >Singed Scalpel</th> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row4_col0" class="data row4 col0" >8</td> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row4_col1" class="data row4 col1" >$4.35</td> 
        <td id="T_94ea3ce6_6ac7_11e8_a784_a8667f358ca8row4_col2" class="data row4 col2" >$34.80</td> 
    </tr></tbody> 
</table> 


