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

1. Create a CSV file called `election_ids.csv` with two columns: `year` and `election_id` where `election_id` is the id used by the website for the presidential election in the corresponding year. (6 points)

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

2. Get and save the CSV for each year to files `data/1924.csv,...,data/2016.csv`. (6 points)

    Hint: To iterate over the rows in a DataFrame you can use the following pattern:
    
    ```python
    for i, row in df.iterrows():
        # now row is a Series with index ['election_id', 'year']
    ```
    
    Note that when reading `election_ids.csv`, pandas will automatically use numeric types for `election_id` and `year`. To use them in a filename you'll need to convert them to strings, e.g. `open('data/' + str(year) + '.csv', 'w')`.

# Part B: Analyze

1. Create a new CSV called `republican_shares.csv` with three columns: Year, County, and Republican Share by following these steps:

    i. For each CSV file into a dataframe read it and add a column called 'Republican Share' with the proportion of votes going to the Republican in each race.
    
        Hint: The CSVs are weirdly formatted where the second row contains the party and the numbers have commas in them. So one strategy is:

        - Grab the parties row as a series using `df.loc[0]`.
        - Find the Republican column (assuming there is one and only one!) by subsetting the parties series to those entries where the value is `'Republican'` and take the first one.
        - [Drop](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.drop.html) the first row from the dataframe
        - The numbers have commas so you can't divide them yet. Write a function called `to_number(series)` that takes a series of numbers and removes the commas and converts them to numbers.
    
    ii. Add a column called `Year` that's a constant in each year's dataframe and equal to the year.
    iii. Subset to just the `County/City`, `Year` and `Republican Share` columns.
    iv. Use `pandas.concat()` to conatenate all of these DataFrames and write the result to `republican_shares.csv`


2. 
