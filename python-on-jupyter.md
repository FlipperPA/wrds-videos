# Python on Jupyter Script

## Scene 1: TIM ONSCREEN - Slide behind him says "Using Python on Jupyter"

Hi. I’m Timothy Allen, Principal Engineer here at WRDS, Wharton Research Data Services. Today I’m going to show you how to use Python on the WRDS Jupyter instance. We’ll start from the WRDS home page. 

## Scene 2: TIM OFFSCREEN

So, the first thing I’ll do is hop here to the search box and search for Python Jupyter. The page I’ll take a look at is the How to Access Python from the Web on JupyterHub. 

I’ll click through to the page. The article has some useful information, but I’m just going to click on the Open JupyterHub link.  

Now you’ll just need to log into Jupyter using your WRDS credentials, which is straightforward to do. If this is your first time logging into Jupyter on WRDS, you’ll need to select the Python 3 Kernel. This will bring up a blank Jupyter Notebook.  

## Scene 3: TIM ONSCREEN - Slide behind him says "WRDS Python Package"

Let’s dive into using the WRDS package. This Python package is something we’ve built specifically for accessing data on WRDS that is publicly available from the Python Package Index. The package makes accessing WRDS data very straightforward. It’s a custom fit, like a fine suit.  

If this is the first time you’re doing this, you will be prompted to create a pgpass file. This will save your credentials so you don’t need to enter them every time you want to use Python on Jupyter. 

## Scene 4: TIM OFFSCREEN

I’m going to import the WRDS package using the Python IMPORT command in the Jupyter cell and connect to the database:

```python
import wrds
db = wrds.Connection(wrds_username='tallen')
```

Now I’m going to use some of the helper methods provided by the package to select some data from one of the free datasets, the Dow Jones Index. I’m using the raw SQL method, so I type in:  

db.raw_sql('SELECT date, dji FROM djones.djdaily') 

What this will do is connect to our data server and return the Dow Jones Industrial Average for each of the dates that we have data for.  Now I run the code in this cell by hitting the Run button. And you’ll see it returns the data I requested, showing the Dow Jones Industrial Average starting back from 1896.  

The next thing that we’ll do is take a look at all the libraries that your institution has access to.  

We’ve built a handy command in the package called `list_libraries`. 

db.list_libraries() 

When I type this into a cell and click Run I’m going to get a very long list - because I’m staff at WRDS and I have access to everything. Your list will be a little shorter, but that’s just fine. As we scroll here you will be able to see all the different libraries your institution has access to. Each library is stored as a schema in the database. In this context, a schema is an organizational unit for holding a set of data tables with restricted access. This command shows you the libraries you can access.  

What I’m going to do is come down to select crsp_a_stock, the CRSP Annual Stock library, one of our more popular. Let’s take a look at the actual tables within it. We have another handy command called `list_tables` where I can pass the library name crsp_a_stock and hit the Run button again. 

db.list_tables() 

Here, we see a list of all the tables available within the crsp_a_stock library. 

The table I’m going to take a closer look at is dsf, which is the Daily Stock File. If we scroll down to the next cell, we can come back to using our handy `raw_sql` command again. The WRDS package contains helper functions for previewing the data, but I’ll just SELECT the first few rows using the SQL LIMIT clause to specify the number of rows to fetch. I click Run again. And that will give us the first five rows of the CRSP Daily Stock file. 

db.raw_sql("SELECT * FROM crsp_a_stock.dsf") 

You can see now see all the columns that are available within this table and the first five rows of data. And from there we can start to do some really interesting things.  

For example, if we want to just look at the volume, Bid Low and Ask High data for Apple, I happen to know Apple’s CUSIP off the top of my head is 03783310.  I type in the raw SQL requesting that data.  

df = db.raw_sql("""
    SELECT cusip, date, vol, bidlo, askhi
    FROM crsp_a_stock.dsf
    WHERE cusip = '03783310'
""")

df 

I can go ahead and run this. Then we can take a look at the DataFrame by typing df and clicking Run. And you’ll see all the data that we requested displayed in a data frame.   

[Scene 5: TIM ONSCREEN - Slide behind him says “Pandas DataFrame”] 

Let’s take a moment to talk about how this data is returned to us by the WRDS package in a pandas DataFrame. The raw SQL Command automatically outputs a DataFrame, which is a tabular data structure with labeled rows and columns.   

I won't get into the details of everything you can do with pandas. But for those of you not familiar with pandas DataFrames, they give you all the power of an Excel spreadsheet from a programmatic interface. You can think of it as spreadsheet capability within Python. It is incredibly powerful. Which is why I want to end this tutorial by showing you an example of how to use one of the pandas functions, the Mean function, which we’ll use to bring up the average volume for Apple. 

[Scene 6: TIM OFFSCREEN]


df.loc[:, "vol"].mean() 

This line of code finds the volume location within the DataFrame and calculates the mean across over 10,000 rows of Apple data, each row being a single trading day. When I go ahead and run that you'll see it gives me the mean for the volume across all the years of Apple data we have. That’s over 18,000,000 shares traded on average daily since Apple was founded! 

If you’re old-school like me, and cut your teeth on SQL, you can also do some pretty cool stuff at the SQL level. What I'm going to do here is take a look at the average volume for every year and month. 

df = db.raw_sql("""
    SELECT AVG(vol) AS avg_volume, cusip, DATE_PART('year', date) AS year, DATE_PART('month', date) AS month
    FROM crsp_a_stock.dsf
    WHERE cusip = '03783310' AND vol IS NOT NULL
    GROUP BY cusip, year, month
    ORDER BY year, month
""")
df 

I’m using an aggregate function here to group by the year and month. You can see that I'm asking for the average volume by month, and I’m selecting the year, and the month from the DATE column. Then I’m grouping the result by the year and month that I have aliased.  

This returns is the average daily volume for Apple for every year and month since the company went public. And since I ordered it by year and month it is easy to see how the volume has waxed and waned over time. We have aggregated data monthly for over 500 months of Apple’s existence. 

[Scene 7: TIM ONSCREEN - Slide behind him says "Thank You"] 


That’s a basic introduction on how you can access data using Python on Jupyter at WRDS. We showed examples that used raw SQL queries, as well as Pandas functions. For your next steps you may want to learn more about using pandas. Thanks for joining us here at WRDS and good luck with your research. 