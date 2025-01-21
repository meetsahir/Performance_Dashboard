
# Performance Dashboard

### We are developing an interactive dashboard to analyze and visualize the sales performance of a company, comparing actual results against set targets. This tool aims to provide insightful metrics and trends to help drive strategic decision-making

## Problem Statement

Our company is focused on selling products and aims to understand whether we are meeting our sales targets. To achieve this, we need a comprehensive analysis across various dimensions:

- Monthly Performance: Identify which months we are meeting or exceeding our sales targets and which months we are falling short.
- Sales Personnel Analysis: Determine which salespersons are meeting their individual targets and which are not, allowing us to recognize top performers and provide support where needed.
- Variance Analysis: Calculate the variance between actual sales and targets to understand the extent of overperformance or underperformance.
To facilitate this analysis, we propose the development of a dynamic and interactive dashboard. This tool will consolidate all relevant data in one place, providing a clear and intuitive interface for tracking sales performance. The dashboard will offer:

- Real-time Data Visualization: Interactive charts and graphs to visualize sales trends and performance metrics.
- Customizable Views: Filters and options to drill down into specific time periods, regions, or sales teams.
- Automated Reporting: Generate reports that highlight key insights and actionable recommendations.
By leveraging this dashboard, our stakeholders will be empowered to make informed strategic decisions, ensuring that we stay on track to meet our sales objectives and drive business growth

### Source

The source file is an Excel file named "actual-vs-target-dataset" - https://github.com/meetsahir/Performance_Dashboard/blob/main/actual-vs-target-dataset.xlsx

The Excel file contains 4 worksheets which correspond to 4 tables, the first table/tab "Actual" lists the actual sales done by the Salesperson every month, the next table/tab "Targets"  lists the target sales to be done by the Salesperson every month, third table/tab "Calendar" is created manually to date calculation, and fourth table/tab "People" lists the Salespersons details like the team he belongs to etc.


### Data cleaning

Before loading the data to Power BI we will be transforming the data using the "Transform" option in Power Query of Power BI 
- In all the tables, the first row is made Header using "Use first row as header"
- The data in the "Actual" and "Targets" table are in the pivoted format so it hard to analyze them, hence we will unpivot the data in them. So select the Salesperson field on the "Actual" and "Targets" table and right click >>Unpivot other columns 
- Change the field names and datatypes if required 
- In the "Calendar" table, we will add a few more columns like Year, Month, Name of the month, Quarter, etc. 

Once done, we will load the data into Power BI 

### Data modeling

We need to move to the data model tab and create relationships between the tables. Power BI by default will make some relationships for us, rest we need to make on our own 
-  Salesperson_Actual >> Salesperson_People
- Salesperson_Targets>> Salesperson_People
- Month_Actual>>Date_Calendar
- Month_Targets>>Date_Calendar

### DAX calculations

We will need create a few measures which will be required in the dashboard. The measures created would be:
1. Total Sales - Actual = SUM(Actual[Sales])
2. Total Sales - Target = SUM(Targets[Targets])
3. Variance = [Total Sales - Actual]-[Total Sales - Target]
4. Variance% = Divide([Variance],[Total Sales - Target])
5. YTD Sales - Actual = CALCULATE([Total Sales - Actual], DATESYTD('Calendar'[Date]))
6. YTD Sales - Target = CALCULATE([Total Sales - Target], DATESYTD('Calendar'[Date]))
7. YTD Variance = CALCULATE([Variance], DATESYTD('Calendar'[Date]))
8. YTD Variance% = CALCULATE([Variance%], DATESQTD('Calendar'[Date]))
9. Target reached - Month count = 
         var months_with_target_reached = FILTER(all('Calendar'), [Variance]>0)
RETURN 
        COUNTROWS(months_with_target_reached)
10. Target status = If([Variance]>0, 1, -1)
11. Variance Pct Label = 
            var up = "🟢"
            var down ="🔴"
            var formatted_var_pct = FORMAT(abs([Variance%]), "0.0%")
return
            formatted_var_pct & " " & IF([Variance%]>0,up, down)
12. Trend chart title = 
        var month_count =   countrows('Calendar')
return
        "We met targets for" & [Target reached - Month count] & "out of" & month_count

### Visualization

We have everything ready now to start creating the dashboard

- Cards are used to display the KPIs on the left side

https://github.com/meetsahir/Performance_Dashboard/blob/main/1.png
