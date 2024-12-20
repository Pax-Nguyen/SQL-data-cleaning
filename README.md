# SQL-data-cleaning
This is an educational project on data cleaning and preparation using SQL. The original database in CSV format is located in the file club_member_info.csv. Here, we will explore the steps that need to be applied to obtain a cleansed version of the dataset.

Let's inspect the initial rows to analyze the data in its original format.

|full_name|age|martial_status|email|phone|full_address|job_title|membership_date|
|---------|---|--------------|-----|-----|------------|---------|---------------|
|addie lush|40|married|alush0@shutterfly.com|254-389-8708|3226 Eastlawn Pass,Temple,Texas|Assistant Professor|7/31/2013|
|      ROCK CRADICK|46|married|rcradick1@newsvine.com|910-566-2007|4 Harbort Avenue,Fayetteville,North Carolina|Programmer III|5/27/2018|
|Sydel Sharvell|46|divorced|ssharvell2@amazon.co.jp|702-187-8715|4 School Place,Las Vegas,Nevada|Budget/Accounting Analyst I|10/6/2017|
|Constantin de la cruz|35||co3@bloglines.com|402-688-7162|6 Monument Crossing,Omaha,Nebraska|Desktop Support Technician|10/20/2015|
|  Gaylor Redhole|38|married|gredhole4@japanpost.jp|917-394-6001|88 Cherokee Pass,New York City,New York|Legal Assistant|5/29/2019|
|Wanda del mar       |44|single|wkunzel5@slideshare.net|937-467-6942|10864 Buhler Plaza,Hamilton,Ohio|Human Resources Assistant IV|3/24/2015|
|Joann Kenealy|41|married|jkenealy6@bloomberg.com|513-726-9885|733 Hagan Parkway,Cincinnati,Ohio|Accountant IV|4/17/2013|
|   Joete Cudiff|51|divorced|jcudiff7@ycombinator.com|616-617-0965|975 Dwight Plaza,Grand Rapids,Michigan|Research Nurse|11/16/2014|
|mendie alexandrescu|46|single|malexandrescu8@state.gov|504-918-4753|34 Delladonna Terrace,New Orleans,Louisiana|Systems Administrator III|3/12/1921|
| fey kloss|52|married|fkloss9@godaddy.com|808-177-0318|8976 Jackson Park,Honolulu,Hawaii|Chemical Engineer|11/5/2014|

## Copy the table

### Create a new table for cleaning 

Let's generate a new table where we can manipulate and restructure the data without modifying the original dataset

    -- club_member_info definition

    CREATE TABLE club_member_info_cleaned (
	    full_name VARCHAR(50),
	    age INTEGER,
	    martial_status VARCHAR(50),
	    email VARCHAR(50),
    	phone VARCHAR(50),
    	full_address VARCHAR(50),
    	job_title VARCHAR(50),
    	membership_date VARCHAR(50)
    );

### Copy all values from original table

    INSERT INTO club_member_info_cleaned
    SELECT * FROM club_member_info;

## Data cleaning

- ### 'full_name' column

#### Cleaning whitespace

    UPDATE club_member_info_cleaned 
    SET full_name = TRIM(full_name);

#### Upper full name

    UPDATE club_member_info_cleaned 
    SET full_name = UPPER(full_name);
    
- ### 'age' column

#### Find ages out of the realistic range

    select *
    from club_member_info_cleaned
    where age > 100;

#### Find the average age to replace the outlier age

    select AVG(age) 
    from club_member_info_cleaned
    where age <= 100;

  The result is: 42

#### Replace the ages out the the realistic range or empty cells by the average age

    UPDATE club_member_info_cleaned 
    SET age = 42
    WHERE age > 100;

- ### 'martial_status' column

#### Check the frequency of the martial status

    SELECT martial_status, COUNT(full_name)
    FROM club_member_info_cleaned
    GROUP BY martial_status ;
    
Result:
|martial_status|count(full_name)|
|--------------|----------------|
||20|
|divorced|449|
|divored|4|
|married|881|
|single|656|

we can see that: 
- there is wrong martial status "divored", we have to correct the name to "divorced"
- 20 cells are blanks, we will update with the most frequence status which is "married"

#### Correct the martial status name

    UPDATE club_member_info_cleaned 
    SET martial_status = 'divorced'
    WHERE martial_status ='divored';

#### Update the blank cells

    UPDATE club_member_info_cleaned 
    SET martial_status = 'married'
    WHERE martial_status = "";

- ### 'email' column

#### Check if there is any email in wrong format

    SELECT *
    FROM club_member_info_cleaned
    WHERE email NOT LIKE '%@%';

The result is that there is no email in wrong format, so "email" column is okay.

- ### 'phone' column

#### Check whether there is any phone in wrong format

    SELECT *
    FROM club_member_info_cleaned
    WHERE phone NOT LIKE "___-___-____";

The result shows that:
- there are wrong format phones
- blank cells

#### Update the wrong phone number by 999-999-9999

    UPDATE club_member_info_cleaned 
    SET phone = '999-999-9999'
    WHERE phone NOT LIKE "___-___-____";

- ### "full_address" column

#### Check whether there is any blank cell

    SELECT *
    FROM club_member_info_cleaned
    WHERE full_address = "";
    
The result shows that data in "full_address" is okay

- ### "job_title" column

#### Check whether there is any blank cell

    SELECT COUNT(job_title) AS 'Job title blank cells'
    FROM club_member_info_cleaned
    WHERE job_title = "";

The result:

|Job title blank cells|
|---------------------|
|39|

#### Update the job title as "NULL"

    UPDATE club_member_info_cleaned 
    SET job_title = 'NULL'
    WHERE job_title ="";

- ### "membership_date" column

#### Check whether there is any blank cell

    SELECT *
    FROM club_member_info_cleaned
    WHERE membership_date = "";
    
The result shows that data in "membership_date" is okay
