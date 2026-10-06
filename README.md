
# End-to-End Power BI Project 1

## Problem Statement

This dashboard helps financial institutions analyze loan portfolio performance and identify patterns associated with loan defaults. It provides insights into loan amounts, default rates, borrower demographics, credit score categories, income, employment type, marital status, and education.

By analyzing these factors, the dashboard helps identify borrower segments with different levels of financial risk and understand how loan exposure varies across customer categories. It also provides year-over-year and year-to-date analysis of loan amounts and default loans, helping monitor changes in the loan portfolio over time.

The dashboard can therefore support data-driven analysis of loan portfolio risk and help identify areas that may require further attention.
### Data Source - Data Flow

### Steps followed

- Step 1 : Open Power BI Service and sign in to the account. Go to Workspaces and create a New Workspace by providing a workspace name.

- Step 2 : Download and install the Standard Mode On-premises Data Gateway. Configure the gateway by providing a Gateway name and Recovery key.

- Step 3 : Install Microsoft SQL Server to store and manage the project data before connecting it to the Power BI Dataflow.

- Step 4 : Open SQL Server Management Studio (SSMS), create a new database named Loan, and use the Loan database.

- Step 5 : In Object Explorer, go to Databases → Loan → Tasks → Import Flat File and import the Loan Default.csv file.

- Step 6 : Verify the imported data by querying the Loan Default table using the query `SELECT * FROM dbo.[Loan Default];`.

- Step 7 : Open Power BI Service, go to the newly created Workspace, select New Item → Dataflow, and choose Dataflow Gen1.

- Step 8 : Select Define new tables and choose Add new table. Select SQL Server as the data source.

- Step 9 : Provide the SQL Server connection details and select the On-premises Data Gateway. Select Windows authentication and provide the required username and password.

- Step 10 : After establishing the connection, select the Loan Default table. Power Query Online opens for data transformation.

- Step 11 : Save and close the Dataflow using the Save & Close option.

- Step 12 : Open Power BI Desktop and select Get Data → More → Dataflows.

- Step 13 : Select the Dataflows option and connect to the Dataflow created in Power BI Service.

- Step 14 : In the Navigator window, select the Dataflow, Loan Default, and Loan Data.

- Step 15 : Created a Year column from the Loan Date DDMMYYYY column using the DAX `YEAR` function.

- Step 16 : Created a separate Measures Table 1 to store the measures used for Page 1 of the report.

- Step 17 : A new measure was created to calculate the total loan amount for records where the loan amount is not blank.

  Following DAX expression was written to calculate the loan amount,

        Loan Amount by Purpose =
        SUMX(
            FILTER(
                'Loan Default',
                NOT (ISBLANK('Loan Default'[Loan Amount])
            )),
            'Loan Default'[Loan Amount]
        )

   A line chart was used to represent the loan amount by loan purpose, with Loan Purpose on the X-axis and Loan Amount by Purpose on the Y-axis.

  Snap of the line chart:

![Snap Loan Amount by Purpose](Loan_amt_by_purpose.png)

- Step 18 : The DAX measure was validated by creating a temporary table visual using Loan Purpose and Loan Amount with the aggregation set to SUM. The values were compared with the results obtained from the DAX measure.

  Snap of the table visual:

![Snap Loan Amount by Purpose validated by table visual](Loan_amt_by_pur_vldn_Table.png)

- Step 19 : The results were further validated using a PivotTable in the original Excel dataset by placing Loan Purpose in Rows and Loan Amount in Values.

  Snap of the PivotTable:

![Snap Loan Amount by Purpose validated by PivotTable](Loan_amt_by_pur_vldn_pivot.png)

 - Step 20 : A new measure was created to calculate the average income by employment type.

  Following DAX expression was written to calculate the average income by employment type,

        Average Income by Employment type = 
        CALCULATE(AVERAGE('Loan_default'[Income]),ALLEXCEPT('Loan_default','Loan_default'[EmploymentType]))

  A line chart was used to represent the average income by employment type, with Employment Type on the X-axis and Average Income by Employment type on the Y-axis.

  A line chart was used to represent the average income by employment type, with Employment Type on the X-axis and Average Income by Employment Type on the Y-axis.

  Snap of the line chart:

![Average Income by Employment Type](Average_Income_by_Employment_Type.png)

- Step 21 : The use of the ALLEXCEPT function ensures that the measure responds to the Employment Type filter while ignoring other filters affecting the calculation.

  When a value from the Average Income by Employment Type chart is selected, the Loan Amount by Purpose chart changes according to the selected Employment Type. However, selecting a value from the Loan Amount by Purpose chart does not change the Average Income by Employment Type values because the measure retains only the Employment Type filter.

- Step 22 : The Average Income by Employment type measure was validated using a table visual in Power BI by comparing the calculated values with the average Income for each Employment Type.

- Step 23 : The results were further validated using the original Excel dataset by creating a PivotTable with Employment Type in Rows and Income in Values, with the aggregation set to Average. The values matched the results obtained from the DAX measure.  

- Step 24 : A new measure was created to calculate the default rate by employment type. Variables were used to calculate the total number of records and the total number of default cases.

  Following DAX expression was written to calculate the default rate by employment type,

        Default Rate by Employment type = 

        VAR totalrecords=COUNTROWS(ALl('Loan_default'))

        VAR DefaultCases=COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE()))

        RETURN 

        CALCULATE(DIVIDE(DefaultCases,totalrecords),ALLEXCEPT('Loan_default',Loan_default[EmploymentType]))*100

  A line chart was used to represent the default rate by employment type, with Employment Type on the X-axis and Default Rate by Employment type on the Y-axis.



  Snap of the line chart:

  ![Default Rate by Employment Type](Default_Rate_by_Employment_Type.png)

- Step 25 : The Default Rate by Employment type measure was validated using a table visual in Power BI. Employment Type and Default were added to the visual. The Default column was added again and its aggregation was changed to Count, which was then displayed as a percentage. The percentage for Default = TRUE was compared with the values obtained from the DAX measure.

- Step 26 : The results were further validated using the original Excel dataset by creating a PivotTable with Employment Type in Rows and Default in Values with the aggregation set to Count. Default was also added to the Filters area and filtered for the value 1 (TRUE). The count for each Employment Type was divided by the total number of default cases (The total number of default cases obtained from the Power BI validation table was used as the denominator in Excel to calculate the default rate for each employment type ) to calculate the default rate. The resulting percentages matched the values obtained from the DAX measure.  

  Snap of the Excel validation:

  ![Default Rate by Employment Type validated in Excel](Default_Rate_by_Employment_Type_Excel_Validation.png)



- Step 27 : An Age Groups calculated column was created because the original dataset did not contain an age group column. The Age column was used to categorize customers into Teen, Adults, Middle Age Adults, and Senior Citizens.

  Following DAX expression was written to create the Age Groups column,

        Age Groups = 
        IF('Loan_default'[Age]<=19,"Teen",
            IF('Loan_default'[Age]<=39,"Adults",
                IF('Loan_default'[Age]<=59,"Middle Age Adults",
                "Senior Citizens")))

- Step 28 : A new measure was created in Measures Table 1 to calculate the average loan amount by age group using the AVERAGEX and VALUES functions.

  Following DAX expression was written to calculate the average loan amount by age group,

        Average Loan by Age Groups = 
        AVERAGEX(VALUES('Loan_default'[Age Groups]),AVERAGE('Loan_default'[LoanAmount]))

  A line chart was used to represent the average loan amount by age group, with Age Groups on the X-axis and Average Loan by Age Groups on the Y-axis.

  Snap of the line chart:

![Average Loan by Age Groups](Average_Loan_by_Age_Groups.png)

- Step 29 : The Average Loan by Age Groups measure was validated using a table visual in Power BI by adding Age Groups and the average of LoanAmount.

- Step 30 : The results were further validated using the original Excel dataset by using Age as a filter, selecting the required age groups, and calculating the Average of Loan Amount in the Values section. The values matched the results obtained from the DAX measure.

- Step 31 : A new measure was created to calculate the default rate by year. Variables were used to calculate the total number of loans and the total number of default cases for each year.

  Following DAX expression was written to calculate the default rate by year,

        Default rate by Year = 
        VAR TotalLoans=
                        CALCULATE(COUNTROWS('Loan_default'),ALLEXCEPT('Loan_default',Loan_default[Year]))

        VAR Default= CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),ALLEXCEPT('Loan_default',Loan_default[Year]))  

        RETURN
        DIVIDE(Default,TotalLoans)*100

  A line chart was used to represent the default rate by year, with Year on the X-axis and Default rate by Year on the Y-axis.

  Snap of the line chart:

![Default Rate by Year](Default_Rate_by_Year.png)

- Step 32 : The Default rate by Year measure was validated using a table visual in Power BI by adding Year and Default, with the Default field aggregated as Count. The calculated values were compared with the results obtained from the DAX measure.

### Loan Default Overview

The Loan Default Overview page looks like this:

![Loan Default Overview](Loan_Default_Overview.png)

- Step 33 : A new Measures Table 2 was created to store the measures used on the new report page.

- Step 34 : A new page was added to the Power BI report and named **Applicant Demographics and Financial Profile**.

- Step 35 : A new measure was created to calculate the median loan amount using the MEDIANX DAX function.

  Following DAX expression was written to calculate the median loan amount,

        Median by Credit Score Bins = 
        MEDIANX('Loan_default','Loan_default'[LoanAmount])

  A card visual was used to represent the calculated median loan amount. The card displayed a value of approximately 127.56K.

- Step 36 : The median loan amount was manually validated using the Loan Amount column.

  The Loan Amount column was sorted in ascending order, and the column profile was checked using the complete dataset. The total number of records was 255347, and the middle observation was identified based on the total number of records.

  An Index column starting from 1 was used to locate the middle observation. The Loan Amount corresponding to the middle observation was checked and compared with the value displayed in the card visual.

  The manually checked median value matched the value obtained from the MEDIANX measure.

- Step 37 : A Credit Score Bins calculated column was created in the Loan_default table to categorize customers based on their credit score.

  The credit score was divided into four categories: Very Low, Low, Medium, and High.

  Following DAX expression was written to create the Credit Score Bins column,

        Credit Score Bins = 
        IF(Loan_default[CreditScore]<=400,"Very Low",
            IF('Loan_default'[CreditScore]<=450,"Low",
                IF('Loan_default'[CreditScore]<=650,"Medium",
                "High")))



- Step 38 : The card visual used to represent the overall median loan amount was removed.

  A line chart was created to represent the median loan amount by credit score category, with Credit Score Bins on the X-axis and Median by Credit Score Bins on the Y-axis.

  Snap of the line chart:

![Median Loan Amount by Credit Score Category](Median_Loan_Amount_by_Credit_Score_Category.png)

- Step 39 : A new measure was created to calculate the average loan amount for customers with High credit score.

  Following DAX expression was written to calculate the average loan amount for High credit customers,

        Average Loan Amount(High Credit) = 
        AVERAGEX(FILTER('Loan_default','Loan_default'[Credit Score Bins]="High"),'Loan_default'[LoanAmount])

- Step 40 : A donut chart was created to represent the average loan amount for High credit customers by age group and marital status. Average Loan Amount(High Credit) was added to Values, Marital Status was added to Details, and Age Groups was added to Legend.

  Snap of the donut chart:

![Average Loan Amount(High Credit) by Age Groups and MaritalStatus](Average_Loan_Amount_High_Credit.png)

- Step 41 : The Average Loan Amount(High Credit) measure was validated using a table visual in Power BI by adding Marital Status, Age Groups, Credit Score Bins, and Loan Amount with the aggregation set to Average. The values were compared with the results obtained from the measure.

- Step 42 : The results were further validated using the original Excel dataset. Since the original dataset did not contain a separate identifier for High credit customers, a helper column was created to identify customers with a High credit score.

  Following Excel formula was used to create the Credit Bin High Credit column,

        =IF(E2>650,"High","N/A")

  A PivotTable was then created with Marital Status in Rows and Average of Loan Amount in Values. The Credit Bin High Credit field was added to the Filters area and filtered for High.

  Age was then used as a filter, and the values 18 and 19 were selected to represent the Teen age group. The resulting average loan amounts were compared with the values obtained from the Power BI measure.

- Step 43 : A new measure was created in Measures Table 2 to calculate the total loan amount for Adults by credit score bins.

  Following DAX expression was written to calculate the total loan amount for Adults by credit score bins,

        Total Loan (Credit Bins) = 
        CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Age Groups]="Adults",ALLEXCEPT('Loan_default','Loan_default'[Age],'Loan_default'[Age Groups],'Loan_default'[CreditScore],'Loan_default'[Credit Score Bins]))

  A line chart was used to represent the total loan amount by credit score bins, with Credit Score Bins on the X-axis and Total Loan (Credit Bins) on the Y-axis.

  Snap of the line chart:

![Total Loan by Credit Score Bins for Adults](Total_Loan_Adults_by_Credit_Score_Bins.png)

- Step 44 : The Total Loan (Credit Bins) measure was validated using a table visual in Power BI by adding Age Groups, Credit Score Bins, and Loan Amount with the aggregation set to Sum. The values were compared with the results obtained from the measure.

- Step 45 : The results were further validated using the original Excel dataset. Since the original dataset did not contain an Age Groups column, a helper column was created to identify the Adult age group.

  Following Excel formula was used to create the Age Group Adults column,

        =IF(AND(B2>=20,B2<=39),"Adults","N/A")

  A PivotTable was then created with Age Group Adults in the Filters area and Credit Score Bins in Rows. The Age Group Adults filter was set to Adults, and Loan Amount was added to Values with the aggregation set to Sum.

  The resulting total loan amounts were compared with the values obtained from the Power BI measure.


- Step 46 : A new measure was created in Measures Table 2 to calculate the total loan amount for Middle Age Adults.

  Following DAX expression was written to calculate the total loan amount for Middle Age Adults,

        Total Loan (Middle Age Adults) = 
        SUMX(FILTER('Loan_default','Loan_default'[Age Groups]="Middle Age Adults"),'Loan_default'[LoanAmount])

- Step 47 : A clustered column chart was created to represent the total loan amount for Middle Age Adults by mortgage and dependent status. HasMortgage was added to the X-axis, Total Loan (Middle Age Adults) was added to the Y-axis, and HasDependents was added to the Legend.

  Snap of the clustered column chart:

![Total Loan Middle Age Adults by Mortgage and Dependents](Total_Loan_Middle_Age_Adults_by_Mortgage_Dependents.png)

- Step 48 : The Total Loan (Middle Age Adults) measure was validated using a table visual in Power BI by adding HasMortgage, HasDependents, and Loan Amount with the aggregation set to Sum. The values were compared with the results obtained from the measure.

- Step 49 : The results were further validated using the original Excel dataset. Since the original dataset did not contain an identifier for Middle Age Adults, a helper column was created to identify customers between 40 and 59 years of age.

  Following Excel formula was used to create the Middle Age Adults column,

        =IF(AND(B2>=40,B2<=59),"Middle Age Adults","N/A")

  A PivotTable was then created with the Middle Age Adults column in the Filters area and filtered for Middle Age Adults. HasMortgage was also added to the Filters area and filtered for Yes. HasDependents was added to Rows, and Loan Amount was added to Values with the aggregation set to Sum.

  The resulting total loan amounts were compared with the values obtained from the Power BI measure.

- Step 50 : A new measure was created in Measures Table 2 to calculate the total number of loans by education type.

  Following DAX expression was written to calculate the number of loans by education type,

        Loans by Education Type = 
        COUNTROWS(FILTER('Loan_default',NOT(ISBLANK('Loan_default'[LoanID]))))

  The measure uses COUNTROWS along with the ISBLANK and NOT functions to count only those records where Loan ID is not blank.

  A line chart was used to represent the number of loans by education type, with Education on the X-axis and Loans by Education Type on the Y-axis.

  Snap of the line chart:

![Total Number of Loans by Education Type](Total_Loans_by_Education_Type.png)

- Step 51 : The Loans by Education Type measure was validated using a table visual in Power BI by adding Education and Loan ID with the aggregation set to Count. Loan ID was also filtered using advanced filtering to include only records where Loan ID is not blank.

  The resulting counts were compared with the values obtained from the measure.

- Step 52 : The second page of the Power BI report was completed with the above visuals and validations.

### Applicant Demographics & Financial Profile

The Applicant Demographics & Financial Profile page looks like this:

![Applicant Demographics & Financial Profile](Applicant_Demographics_Financial_Profile.png)

- Step 53 : A new page was added to the Power BI report and named **Financial Risk Matrix**.

- Step 54 : A new Measures Table 3 was created to store the measures used on the Financial Risk Matrix page.

- Step 55 : A new measure was created to calculate the Year-over-Year (YoY) change in loan amount.

  The YoY percentage change was calculated by dividing the difference between the current year's loan amount and the previous year's loan amount by the previous year's loan amount, and multiplying the result by 100.

  Following DAX expression was written to calculate the YoY loan amount change,

        YOY Loan Amount Change = 
        DIVIDE(
               CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Year]=YEAR(max('Loan_default'[Loan_Date_DD_MM_YYYY] )))-
               CALCULATE(Sum('Loan_default'[LoanAmount]),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1)

            , CALCULATE(Sum('Loan_default'[LoanAmount]),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1),0)*100

  A line chart was used to represent the YoY change in loan amount, with Year on the X-axis and YOY Loan Amount Change on the Y-axis.

  Snap of the line chart:

![YOY Loan Amount Change](YOY_Loan_Amount_Change.png)

- Step 56 : The YOY Loan Amount Change measure was formatted to display the required decimal precision. The decimal places were increased using the Measure tools formatting options.

  For the year 2013, the measure returns 0 because 2013 is the earliest year available in the dataset and there is no previous year available for comparison. The alternate result of 0 was provided in the DIVIDE function for this case.

  For subsequent years, the measure represents the percentage change in loan amount compared with the previous year.

- Step 57 : A new measure was created in Measures Table 3 to calculate the Year-over-Year (YoY) change in the number of default loans.

  Following DAX expression was written to calculate the YoY change in default loans,

        YOY Default Loans Change = 
        DIVIDE(
              CALCULATE(COUNTROWS(FILTER('Loan_default',Loan_default[Default]=TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])))
               -
              CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1)

            ,CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1),0) *100

  The measure was formatted to display up to five decimal places using the Measure tools formatting options.

  A line chart was used to represent the YoY change in default loans, with Year on the X-axis and YOY Default Loans Change on the Y-axis.

  Snap of the line chart:

![YOY Default Loans Change](YOY_Default_Loans_Change.png)

- Step 58 : A new measure was created in Measures Table 3 to calculate the Year-to-Date (YTD) loan amount.

  YTD loan amount represents the cumulative loan amount from the beginning of the year up to the latest available date in the dataset. The DATESYTD function was used to return the dates from the beginning of the year up to the current date context.

  ALLEXCEPT was used so that the calculation is affected only by Credit Score Bins and MaritalStatus when used with other visuals.

  Following DAX expression was written to calculate the YTD loan amount,

        YTD Loan Amount = 
        CALCULATE(SUM('Loan_default'[LoanAmount]),DATESYTD('Loan_default'[Loan_Date_DD_MM_YYYY].[Date]),ALLEXCEPT('Loan_default','Loan_default'[Credit Score Bins],'Loan_default'[MaritalStatus]))

  A ribbon chart was used to represent the YTD loan amount by Credit Score Bins and MaritalStatus, with Credit Score Bins and MaritalStatus used to analyze the YTD loan amount.

  Snap of the ribbon chart:

![YTD Loan Amount by Credit Score Bins and MaritalStatus](YTD_Loan_Amount_Credit_Score_Bins_MaritalStatus.png)

- Step 59 : An Income Bracket calculated column was created in the Loan_default table to categorize customers based on their income.

  The income was divided into three categories: Low Income, Medium Income, and High Income.

  Following DAX expression was written to create the Income Bracket column,

        Income Bracket = 
        SWITCH(
            TRUE(),
            'Loan_default'[Income]<30000,"Low Income",
            'Loan_default'[Income]>=30000 && 'Loan_default'[Income]<60000,"Medium Income",
            'Loan_default'[Income]>=60000,"High Income")

- Step 60 : A decomposition tree was added to the canvas to analyze the breakup of the total loan amount.

  Loan Amount with the aggregation set to Sum was added to Analyze. Income Bracket and Employment Type were added to Explain by.

  The decomposition tree was used to explore the loan amount by selecting different categories and further breaking down the result based on the selected fields.

  Snap of the decomposition tree:

![Loan Amount Decomposition Tree](Loan_Amount_Decomposition_Tree.png)

- Step 61 : The decomposition tree was further explored by selecting different breakdown options. The required breakdown could also be locked after selecting the desired category.

### Financial Risk Matrix

The Financial Risk Matrix page looks like this:

![Financial Risk Matrix](Financial_Risk_Matrix.png)

- Step 62 : The refresh settings for the Dataflow were configured so that the data in the Dataflow can be updated when the source data in SQL Server changes.

  The SQL Server database was used as the source for the Dataflow. The Dataflow can be refreshed manually or configured for a scheduled refresh.

- Step 63 : A full refresh updates the complete dataset in the Dataflow whenever the refresh is performed. An incremental refresh can be used when only a defined portion of the data needs to be refreshed, which can reduce the refresh time.

- Step 64 : The Dataflow was opened from the Power BI Workspace and the **Schedule Refresh** option was selected.

  Under **Data Source Credentials**, the required access was provided by the administrator.

  Under **Refresh**, the refresh schedule was configured using the required time zone of UTC +05:30 and the required refresh frequency.

- Step 65 : Incremental refresh was configured for the Dataflow.

  The SQL Server Dataflow was selected and the **Incremental Refresh** option was opened. A DateTime column was required to configure incremental refresh.

- Step 66 : Since the available date column was not in DateTime format, its data type was changed in Power Query Online.

  The Dataflow table was opened for editing and Power Query Online was opened. The Loan_Date_DD_MM_YYYY column was changed from Date to DateTime data type.

  The changes were then saved using the **Save & Close** option.

- Step 67 : The Incremental Refresh settings were configured using the DateTime column.

  The data was configured to retain a period of the past five years, with the latest 10 days configured for incremental refresh. This allows the latest 10 days of data to be refreshed while maintaining the historical data from the defined five-year period.

- Step 68 : The option to remove data when the maximum value in the selected column changes was configured as required.

  The **Only request complete days** option was also considered for cases where the data for the current day may be incomplete. This prevents partial-day data from being requested when complete-day data is required.

- Step 69 : After configuring the Dataflow refresh settings, the report refresh settings were also configured.

  The report was published, and the required refresh schedule and data source credentials were configured from the report settings. The **Schedule Refresh** option was enabled and the required credentials were provided through **Data Source Credentials → Edit Credentials**.

 ## Insights

- Home loans have the highest loan amount by purpose at **6,545M**, while Other loans have the lowest at **6,498M**.
- The default rate varies across employment types, with **Unemployed applicants at 3.39%** and **Full-time employees at 2.36%**.
- Average loan amount is highest among **Adults at 127,901** and lowest among **Teens at 126,674**.
- Median loan amount is highest for the **Low credit score category at 128,397** and lowest for the **High credit score category at 127,149**.
- Among Adults, total loan exposure is highest for the **Medium credit score category at approximately 4.6bn**.
- The highest year-over-year loan amount change is **1.72877% in 2018**, while the lowest is **-1.53072% in 2014**.
- The decomposition tree shows a total loan amount of **32,576,880,572**, with the **High Income** bracket contributing **21,731,557,581**. 
