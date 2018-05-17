# Assignment 8: APIs and Regression
In this assignment you will use a "hidden" API in the Virginia elections site to download  county-level presidential election results and then analyze the results. Please commit python scripts `a1.py, a2.py, b1.py, b2.py, b3.py` and the images specified below.

# Part A: API
Your task is to download all of the CSVs for all of the presidential general elections in Virginia from their [Elections Database website](http://historical.elections.virginia.gov/) and save them as `data/1924.csv,...,data/2016.csv`.

First, visit the website in your browser and use the interface to find a list of all presidential general elections. The URL will look like this:

```
http://historical.elections.virginia.gov/elections/search/year_from:1924/year_to:2016/office_id:1
```

Click on one of them, say 2016, and then click "See Details for this Election" and you'll be taken to this URL:

```
http://historical.elections.virginia.gov/elections/view/80871/
```

Click "Download this Election" on the left bar and then "Municipality Results" and you'll get a CSV at this URL:

```
http://historical.elections.virginia.gov/elections/download/80871/precincts_include:0/
```

Note that `80871` is the identifier for that the website is using for this election.

1. Create a CSV file called `election_ids.csv` with two columns: `year` and `election_id` where `election_id` is the id used by the website for the presidential election in the corresponding year. (4 points)

    Hint: The result should start like this:
    
    ```
    election_id,year
    80871,2016
    ....
    ```
    
    The HTML source for the 2016 election row looks like this:

    ```html
     <tr id="election-id-80871" class="election_item general_party">

               <td class="year first" style="padding: 7px 4px;">2016</td>
               <td class="" style="padding: 7px 4px;">President</td>
               <td class="" style="padding: 7px 4px;">Statewide</td>
               <td class="party_border_top">General Election</td>
     ...
    ```
    
    So you can find the election rows by looking for `tr` tags with a class of `election_item` (not that spaces in the class string denote multiple classes). You can find their ids by grabbing the `id` attribute and extracting the number from the end. You can find the year of the election in the text of the next `td` tag.

2. Get and save the CSV for each year to files `data/1924.csv,...,data/2016.csv`. (4 points)

    Hint: To iterate over the rows in a DataFrame you can use the following pattern:
    
    ```python
    for i, row in df.iterrows():
        # now row is a Series with index ['election_id', 'year']
    ```
    
    Note that when reading `election_ids.csv`, pandas will automatically use numeric types for `election_id` and `year`. To use them in a filename you'll need to convert them to strings, e.g. `open('data/' + str(year) + '.csv', 'w')`.

# Part B: Analyze

1. Create a new CSV called `republican_shares.csv` with three columns: Year, County/City, and Republican Share (the proportion of all votes cast for the Republican candidate). Note that the `County/City` column is not quite accurate because there are congressional district subdivisions (e.g. `Chesterfield County (CD 4)`). So you'll have to aggregate the data up to the true `County/City` level. (8 points)

    Hints:

    - For each year's DataFrame, find the Republican candidate's column.
    - [Drop](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.drop.html) the first row; subset to just the Republican's and Total Votes Cast columns, convert those to numbers (they are currently strings with commas in them)
    - Remove the congressional districts in the `County/City` column, e.g. replace `Chesterfield County (CD 4)` to just `Chesterfield County`.
    - Find the Republican and total votes cast in each county using a `groupby().sum()`.
    - Find the `Republican Share`.
    - Subset to just the Republican Share, and add a column called Year that is the year for this election.
    - Finally use [`pd.concat()`](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.concat.html) to concatenate the DataFrames for each election and write the result to `republican_shares.csv`


2. Create a plot with four time series of Republican vote shares in the following counties: Accomack, Amelia County, Amherst, Alleghany. Make sure your plot has meaningful axis labels and a title. (4 points)

3. Select at least 3 [variables](https://api.census.gov/data/2016/acs/acs5/profile/variables.html) from the 2016 5-year ACS whose relationship with 2016 Republican vote share you are interested in exploring. See the Variable Types table [here](https://www.census.gov/data/developers/data-sets/acs-5year/data-notes.html) for more information on variable types. Download them at the County level for Virginia using the Census API and save a CSV called `acs.csv`.

    Hint: Look at the [examples](https://api.census.gov/data/2016/acs/acs5/examples.html)  (note the URL for 2016 is slightly different from 2014) and use the `in` parameter in the API to select Virginia and the `for` parameter to specify counties. Convert the result to a dataframe as in lecture and then use `to_csv()`.
