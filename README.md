# Data Wrangling in Python using Tidy Data Principles

## 1. What is Data Wrangling and the Tidy Data Principles

[Data wrangling](https://online.hbs.edu/blog/post/data-wrangling) refers to the steps and processes taken to tramsform raw data to usable formats for further data analysis. It can include combining or seperating data sources, removing or filling in missing/outliers data, transforming the structure of the dataset, and other strategies employed to ensure the data is of high quality to safeguard the integrity of the data analysis. Why? Because garbage in, garbage out - having clean and useable data is the foundation of valid and reliable data analysis. 

The concept of "tidy data" originates from the paper published by Hadley Wickham in [the Journal of Statistical Software](https://vita.had.co.nz/papers/tidy-data.pdf). It is a set of best practices recommended by Wickham in preparing data for statistical analysis. If a dataset is organized using the tidy data principls, then it will follow the below structure: 

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
The dataset is a public Excel spreadsheet that contains six tables and was published by the United Nation in 2015, documenting the number of international migrants and flow of migrants from 1990 to 2015 of 232 countries. Below is a screenshot of what the original format looks like in Excel. 

![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/835c1384-95f9-4eda-a4c0-e6e6947dc70c)

Before I started delving into making the dataset "tidy" using Wickham's principles, I noticed some general cleanliness issues that needed to be addressed first. As seen in the screenshot below, all six tables in the dataset contain 14 empty rows when loaded into Python, as the original Excel file has a document header (e.g. UN logo, table name, copyright disclaimer etc.) that spreads across the top 14 rows in each table. As such, when I loaded the Excel file in Python, there were no meaningful column headers by default because all top rows are indeed blank. There were also symbols ("..") in some of the cells which impeded the use of functions such as *nlargest* in Python. 

To resolve the above issue, I removed those blank rows using *df.iloc[ ]* and gave the columns proper descriptive names with *df.rename( )*. Afterwards, I also replaced the ".." with "nan" using *df.replace( )*. Yes, these are all simple functions in Python but they make the dataset from non-usable at all to at least something we could get started on!

## 3. Tidy Data Issue (1): Column Headers Are Values, Not Variable Names 
According to Wickham, column headers are meant to denote variables names (e.g, Year, Sex/Gender, Location etc.) and should not contain any actual data points. This principle is clearly violated in the UN dataset which I will illustrate with an example. 

This original table has the year and sex in the column header and was formatted with a merge cell function across multiple cells in Excel. 
![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/901c2e9c-af38-4833-a54d-8ff4beb4b462)

After loading it in Python, I gave it a proper column name with sex and year combined such as F_1990, F_1995......, F_2015. 
![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/5c3d5cdf-593d-46a9-8715-14ae13e32d34)

However, to make the data fit with first tidy data principle, I have to"unpivot" or "transpose" the column headers as a column itself. To do so, I used the *pandas.melt( )* function to unpivot all column headers with values of the attribute “Year” into rows within a single column as these values represent the same variable. 
![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/6448f5de-01fc-4f7e-a656-c3f2a74e272b)
With that, all cells containing the actual number, in this case, th percentage of female international migrants as part of the total migrant stock (both sexes) are stored in one column too. But wait - it's not done yet! 

## 4. Tidy Data Issue (2): Multiple Variables Stored In One Column
Now that the column headers no longer contain any data value, there is another messy data issue that requires attention as well. As each column should only contain one variable, the Sex_Year column is a clear violation of this tidy data principle because it has both Sex and Year stored in it. For the above example, it might not be too big of an issue because all data stored in that data were pertaining only to female. However, in other tables, such as the following one, it is imperative to seperate Sex and Year because we have another value "male" in the Sex variable. 
![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/0a78d797-f632-48b6-8055-387fc63487c0)

Fortunately, when I renamed the columns after bringing in the dataset into Python, I intentionally chose to seperate the sex and year by a common delimiter "_". Thus, I used the *str.split( )* function in Python to split the two strings – values that indicates sex and year – into two separate columns. Afterwards, I renamed the shorthands for male (M) and female (F) to the proper nouns in case that might cause confustions to others. Here is what the new dataframe looks like after the cleaning. 
![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/8ba28a17-49f9-4760-b85a-c0f17cbd7fa9)

## 5. Tidy Data Issue (3): Variables Are Stored In Both Rows And Columns 
The next tidy data issue is a combination of the first and second principles. According the Wickham, individual data points should be stored in cells, and cells are organized as rows and defined by columns. However, if looked closely to the UN dataset prior to cleaning, it stores major areas and regions, such as continent names (e.g., Africa) and regions (e.g., Eastern Africa) in the same column as the countries (e.g., Burundi). Combining this issue with the fact that year and gender values are being stored in the column headers, important information is scattered across rows and columns in the data table, making it hard to analyze computationally 
![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/f615e674-a533-47ff-a785-b9d58395d029)
Following what has been discussed under tidy data issue (1) and (2), the natural next steps would be unpivoting the values (e.g., Sex and Year) from the column headers and seperate these two variables into respective columns. However, this method does not solve the biggest issue which is that the data of continent, regions, and invidual countries were stored in the same column. If this issue is not resolved, the population/migrant values will be double counted as the total will be added to the sum of individual countries.

While there are a lot of different ways to extract the continents and regions from the dataset, I recognized that performing an inner join with the annex table, which contains a list of individual countries and their respective continents and regions would be the easiest way. As seen in the below screenshot, the annex table has the exact same data structure I would like to organize the geography in. Therefore, I used the *df.set_indec ( )* to set the country code as the primary key, and performed an inner join using *df.join(how = inner)* to merge two tables together. 
![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/87ce482c-694e-4cb6-82c7-d1dd85b7d051)

Because the continent and region names were not in the annex table, they got dropped following the inner join. Here is what the new table looks like after the broader geographical regions got pulled from the country column. 
![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/695737d4-ed11-4a85-8dc1-a81780d8bca4)

## 6. Tidy Data Issue (4): Multiple Observations Types in One Table
This issue is only found in one of the six tables in the dataset. As seen in below screenshot, there are three types of data being stored in the same table: (1) Estimated refugees stock at mid-year (both sexes), (2) Refugees as percentage of the international migrant stock, and (3) Annual rate of change of the refugee stock. 
![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/ced10dbc-3224-4f46-b24c-9aaf0abb735a)
To clean this table, I assigned the name of the indicator the value represents when unpivoting the column headers. Afterwards, I created three new dataframes by extracting the rows from the original table.
![image](https://github.com/teresalau/Data-Wrangling-in-Python/assets/113483358/5234e4d9-dcc2-49f9-a17a-99e51d1095d8)

## 7. Tidy Data Issue (5): One Observation Type in Multitple Tables
The last tidy data issue is like the opposite of the first one. It concerns how the same type of obserbation unit (e.g., populuation, migrant percentage, etc.) is being stored across multiple tables. The UN dataset used did not exactly have this issue because each table contains unique information concerning international migrants. An example of this issue raised by Wickham is how storing the most popular baby names over the past 10 years in 10 seperate tables while they should be stored in one tables with an additional columns added to define the year. 

## 8. Conclusion: Clean Data is the Foundation for Data Visualization
Programming languages and many data viz software like Tableau and Power BI take columns as the foundation of variables, and subsequently take variables as the units of analysis. Therefore, it is imperative for the data to be organized in the tidy data framework to enable data visualization and other exploratory data analysis techniques.
