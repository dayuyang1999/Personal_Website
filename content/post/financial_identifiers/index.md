---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "The World of Financial identifiers"
subtitle: ""
summary: "My name is Bruce Wayne. I am the Batman. My home address is 1007 Mountain Drive, Gotham City."
authors: 
- Dylan Yang
tags:
- FinTech
categories: []
date: 2021-09-11T13:03:21-04:00
lastmod: 2021-09-11T13:03:21-04:00
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


# Some Identifier Changes

You think the person at 1007 Mountain Drive, Gotham City is the Batman? Not exactly. The Batman is pretty rich. He stays in different real properties in different time.

- So some financial identifiers are like "the home address of Batman",  they change over time:
  - CUSIP
  - Tickers


For example, if a company ceases to exist, its ticker may be reassigned to another company; a company may be allotted multiple CUSIPs caused by corporate structural changes. For these reasons, identifiers such as tickers or CUSIPs don’t work well with historical analysis.

# The Permanent Identifiers

Just as "Batman" is the only identifier of Batman, some financial identifiers are permanent. They never change for the duration of the company’s life span regardless of the name changes or other instances and are never being reused.

such as:
- PERMNO
  -  is a unique stock (share class) level identifier. While most of the companies have one class shares, some companies have more than one class shares traded at different prices, and this is the reason why a company can have more than one PERMNOs.
     -  For example, CBS Corporation (CBS) has two classes of shares (at the time of publishing of this document), and respectively two PERMNOs. On December 24, 2018 the closing price of shares with PERMNO 75104 was $42.440 and shares with PERMNO 76226 closed at $42.020.
- PERMCO
  - is a unique company level identifier that remains unchanged throughout the whole term of company’s existence even if the company changed name/s.
    - For example, the PERMCO 21566 stays unchanged through the whole history of the company that started with the name Consolidated Grocers Corp. in 1946, then changes to Consolidated Foods Corp.(CFD) in 1954, then became Sara Lee (SLE) in 1985, and as of 2012 is called Hillshire Brands Co. (HSH).
- GVKEY 
  - is a unique number assigned to each company in the Compustat-Capital IQ database.
    - Compustat-Capital IQ database mostly covers public firms, but some private firms also have GVKEY. 



# Linkings

## WRDS Linkings

Researchers can perform the foloowing operations with WRDS Linking tables:

1. Link any type of identifier (ticker, CUSIP, PERMNO, etc.) to GVKEY and one another.
On Home page, select CRSP > CRSP/Compustat Merged > Linking Table. To use, do the following:
  - Step 1: Apply company codes individually, or as a list, or choose the entire database.
  - Step 2 (optional): Select individual linking options if needed. The linking types are listed as mnemonics. To find the meaning of each mnemonic, in Step 3 click ? icon next to Link Type Code variable.
  - Step 3: Select the output variables if needed in addition to default selections of GVKEY code and the company name.
  - Step 4: Select the output format.
  - Click Submit Query.

1. Convert tickers or NCUSIP codes into PERMCO or PERMNO. (NCUSIP is a historical eight digit CUSIP assigned at the equity issue). 
  - On Home page, select CRSP > Tools > Translate to PERMCO/PERMNO​
1. Download link table between various heavily used databases on WRDS platform, such as IBES CRSP linking file.
  - On Home page, select Linking Suite by WRDS.
1. Convert 8-digit CUSIPS into 9 and 6-digit CUSIPs.
1. Convert 9-digit CUSIPS into 8 and 6-digit CUSIPs.
  - On Home page, select Compustat-Capital IQ > Tools > CUSIP Converter


## Other Linking Tables

Some researchers also constructed their own linking tables such as:

1. [CIK-CUSIP Mapping](https://github.com/leoliu0/cik-cusip-mapping)
2. [Takeover Deal-GVKEY Mapping](https://github.com/dayuyang1999/SDC-to-Compustat-Mapping)
3. ...

However, after manually compared, they are not as reliable as the WRDS mapping. So I recommend use WRDS mapping if your institution subscribe this.

End.