---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "A Function That Helps You Write WRDS SQL Query"
subtitle: ""
summary: "if you are not familar with SQL, this function may be a life saver"
authors: 
- Dayu Yang
tags: 
- FinTech
categories: []
date: 2021-09-10T22:39:08-04:00
lastmod: 2021-09-10T22:39:08-04:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

- [Intro](#intro)
- [The Function](#the-function)
- [Examples](#examples)
  - [1st Example](#1st-example)
  - [2nd Example](#2nd-example)

# Intro

First, make sure you have logged into WRDS before doing any query:

```python
# load libraries
import wrds
import pandas as pd

# connect to WRDS
db = wrds.Connection(wrds_username = 'your_username')

```

Traditionally, you need to write raw SQL query to retreive data from WRDS.

Notice that you at least have to know:
- what is the data library name corresponding to the database you want to visit. You can check this [tutorial](https://wrds-www.wharton.upenn.edu/documents/1443/wrds_connection.html) from wrds. 
  - Basically, you need to browse the names of library, and found the one your need (In this case, Compustat is named as `comp`)
    - Why do I know that? You need to find the linking info from [wrds data vensors](https://wrds-www.wharton.upenn.edu/pages/about/data-vendors/)
  - Then, you need to browse all names of tables in the library you just chose.(For this step, you can only **guess the name of table** :(
- the variable names you need
  - How to find them? You should check the [wrds query submit tab](https://wrds-www.wharton.upenn.edu/pages/get-data/compustat-capital-iq-standard-poors/compustat/north-america-daily/fundamentals-annual/)

Here is an example:

```python

start_year = 2008
pd_afr = db.raw_sql(sql=f'''
    select gvkey, datadate, at, ceq, csho, prcc_f, txdb, dlc, dltt, ib, sale, ch, ppent, re, act, lct
    from comp.funda
    where extract(year from datadate) >= {start_year}
''', date_cols=['datadate'])


```

# The Function

However, here I provide a function that "helps" you write the SQL query if you are not a pro in SQL:

```python

def build_query(dataset, **kwargs):
    """Build Query for WRDS database.
        Args:
            dataset (str): dataset
            start (str): Start of time period with format YYYY-MM-DD, default to None
            end (str): End of time period with format YYYY-MM-DD, default to None
            columns (str or list): Filter of one or a list of columns, default to None
            sic (str or list): Filter of one or a list of SIC, default to None
            sich (str or list): Filter of one or a list of historical SIC, default to None
            gvkey (str or list):  Filter of one or a list of Global Company Key, default to None
            tic (str or list):  Filter of one or a list of ticker symbols, default to None
            cusip (str or list):  Filter of one or a list of CUSIP symbols, default to None
            limit (int): Return only a number of return records, defualt to None
        Returns:
            (str) with query
    """
    columns = kwargs.get("columns", None)
    sic = kwargs.get("sic", None)
    sich = kwargs.get("sich", None)
    start = kwargs.get("start", None)
    end = kwargs.get("end", None)
    gvkey = kwargs.get("gvkey", None)
    tic = kwargs.get("tic", None)
    cusip = kwargs.get("cusip", None)
    limit = kwargs.get("limit", None)


    if columns is not None:
        columns_filter = ",".join([x for x in columns])
    else:
        columns_filter = "*"

    date_filter = ""
    if start and end is not None:
        date_filter = "datadate between {} and {}".format("'" + start + "'" , "'" + end + "'")

    sic_filter = ""
    if sic is not None:
        sic_filter = "sic in ({}) ".format(
            ",".join(["'" + x + "'" for x in sic])
        )

    sich_filter = ""
    if sich is not None:
        sich_filter = "sich in ({}) ".format(
            ",".join(["'" + x + "'" for x in sich])
        )

    gvkey_filter = ""
    if gvkey is not None:
        gvkey_filter = "gvkey in ({}) ".format(
            ",".join(["'" + x + "'" for x in gvkey])
        )

    tic_filter = ""
    if tic is not None:
        tic_filter = "tic in ({}) ".format(
            ",".join(["'" + x + "'" for x in tic])
        )

    cusip_filter = ""
    if cusip is not None:
        cusip_filter = "cusip in ({}) ".format(
            ",".join(["'" + x + "'" for x in cusip])
        )

    # FIRST INSTANCE WITHOUT AND THEN SEQUENTIALLY ADD FILTERS (if non empty)
    filters = [date_filter, cusip_filter, tic_filter, gvkey_filter, sic_filter]
    filters_list = ""
    if any(filters):
        filters_list = " and ".join([x for x in filters if x != ""])
        filters_list = "where " + filters_list

    limit_number = ""
    if limit:
        limit_number += "LIMIT {} ".format(limit)

    cmd = (
        "select "
        + columns_filter
        + " from {} ".format(dataset)
        + filters_list
        + limit_number
        )

    print(cmd)

    return(cmd)

```




# Examples
Here I give you 2 examples about how to use this function.


## 1st Example

You already have the Gvkeys. You want to know what are the firm names corresponding to those GVKEYs you have.

```python

# get gvkey as a list of strings
gvkeys = df[key].astype(str).to_list()

# the variable you need to retreive from compustat database
compustat_needs = ['gvkey','conm']

# call the function which helps you write SQL
query = build_query(dataset = 'comp.funda',
                columns = compustat_needs,
                gvkey = gvkeys,
                start = '1997-01-01',
                end='2021-01-01')

# do the Query
gvkey_tmp = db.raw_sql(query)

# since I did not control the fiscal year , must have a lot duplicates, drop them
gvkey_tmp.drop_duplicates(subset=['gvkey','conm'], keep='last')

# merge with my original pandas df named "df"
df2 = df.merge(gvkey_tmp, left_on = key, right_on = "gvkey", how = "left")

# gvkey is used for merging dfs, drop after merge
df2 = df2.drop('gvkey', 1)

# rename the firm name info variable which original name is "conm"
df2 = df2.rename(columns={'conm':key+'compustat_firmname'})

```

## 2nd Example

I have SICs and I what to get the detail firm information about those SICs.
- you could define your own "firm information". In this case, the info I am interested in are `["gvkey","datadate","fyear","indfmt","consol",
           "popsrc","datafmt","tic","cusip", "conm","revt"]`

```python

# get identifier of companies from query result
gvkeys = df["gvkey"].astype(str).to_list()


columns = ["gvkey","datadate","fyear","indfmt","consol",
           "popsrc","datafmt","tic","cusip", "conm","revt"]

# use gvkeys for new query
query = build_query(dataset ="comp.funda",
                    columns = columns,
                    gvkey = gvkeys,
                    start = '2000-01-01',
                    end = '2020-01-01',
                   )
# do the query 
df = db.raw_sql(query)


```

Actually, the query that the helper function helps you write is:

```sql

select gvkey,datadate,fyear,indfmt,consol,popsrc,datafmt,tic,cusip,conm,revt from comp.funda where datadate between '2000-01-01' and '2020-01-01' and gvkey in ('002127','003104','003808','009725','010171','010174','010910','013036','013400','014136','017005','033113','062038','066405','105464','105572','107248','108326','108768','156014','160849','165672','171083','175060','179566','186093','186778','187597')


```

End.

