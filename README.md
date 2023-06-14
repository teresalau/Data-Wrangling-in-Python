# Data Wrangling in Python using Tidy Data Principles

## 1. What is the Tidy Data Principles
The concept of "tidy data" originates from a paper published by Hadley Wickham in the Journal of Statistical Software. It is a set of best practices recommended by Wickham in preparing data for statistical analysis. If a dataset is organized using the tidy data principls, then it will follow the below structure: 
1. Every column is a variable
2. Every row is an observation
3. Every cell is a single value

It might be hard to envision the above principles in practice, but it might be easier to see how a dataset could be messy. According to Wickham, messy data often falls under one or more of the below common problems: 
1. Column headers are values, not variable names
2. Multiple variables are stored in one column
3. Variables are stored in both rows and columns
4. Multiple types of observational units are stored in the same table
5. A single observational unit is stored in multiple tables.

I would like to demonstrate how I used the tidy data principles to identify the "messy" elements from a public dataset and cleaned it in Python for initial eexploratory data analysis. This project is a part of an individual assignment I did for the course INF1344 - Programming for Data Science taught by Professor Shion Guha from the University of Toronto Faculty of Information.

## 2. Dataset
The dataset is a public excel spreadsheet that contains six tables and was published by the United Nation in 2015, documenting the number of international migrants and flow of migrants from 1990 to 2015 of 232 countries.

Before I started delving into making the dataset "tidy" using Wickham's principles, I noticed some general cleanliness issues that needed to be addressed first. As seen in the screenshot below, all six tables in the dataset contain 14 empty rows when loaded into Python, as the original Excel file has a document header (e.g. UN logo, table name, copyright disclaimer etc.) that spreads across the top 14 rows in each table. As such, when I loaded the Excel file in Python, there were no meaningful column headers by default because all top rows are indeed blank. There were also symbols ("..") in some of the cells which impeded the use of functions such as *nlargest* in Python. 

To resolve the above issue, I removed those blank rows using *df.iloc[ ]* and gave the columns proper descriptive names with *df.rename( )*. Afterwards, I also replaced the ".." with "nan" using *df.replace( )*. Yes, these are all simple functions in Python but they make the dataset from non-usable at all to at least something we could get started on!

## 3. Tidy Data Issue (1): Column Headers Are Values, Not Variable Names 
