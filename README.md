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

![Image](https://github.com/user-attachments/assets/09e7c1a0-46b8-48f0-9de0-9e4ec1501298)

- We calculate the measures as above which are later shown on the cards 
- Below the cards we have a Clustered Column chart to show the months on which the target was met, where the formula is set to show a green column for the month the target is met and a red column for the month the target is not met 

![Image](https://github.com/user-attachments/assets/e80e1d51-de67-421a-8af3-3a4219c88202)

- We then bring a rectangular shape which is then sent backwards which gives a nice background to the cards
- We also introduce the dashboard title and company logo to the rectangular shape 
- Next we bring in another Clustered Column chart which would show the Actual sales vs the Target sales every month 

![Image](https://github.com/user-attachments/assets/9c8b4794-f3db-4a01-89bf-18efc3ce3719)

- For the title of this Clustered Column chart we will use the Trend chart title DAX formula which will make the title dynamic 
- After this we bring in a table which shows the Actual sales, Target sales and Variance according to the salesperson 

![Image](https://github.com/user-attachments/assets/844943ad-3e17-4697-87f2-b22bf379da22)

Note: Before bringing in the salesperson picture column in this chart, we need to make the picture field in the actual table's data category as "Image URL"
- Next we insert a pie chart which shows the actual sales against the different quarters 

![Image](https://github.com/user-attachments/assets/9dd67bb0-dc9c-404f-bfb9-4d67a5165dc5)

- We also have a text box which gives a brief summary about the trends/insights seen after the data analysis 

![Image](https://github.com/user-attachments/assets/1bc432bd-5166-4c39-b204-a23970cb762b)

- We add a slicer which helps to drill down the data according to teams 

![Image](https://github.com/user-attachments/assets/62c08876-6913-4a61-b51e-b32d6e57f249)

Finally we apply the customizations required, shape the charts, create the color combinations to make the dashboard stand out 

![Image](https://github.com/user-attachments/assets/72cf27d1-561d-4b73-98ea-6f605b293cb9)

### Dynamic and interactivity

The dashboard is designed to be highly dynamic and interactive, offering several key features to enhance user experience and data analysis:

- Dynamic Indicators: Key performance indicators such as variance and sales are color-coded for quick insights. If targets are not met, these indicators will display in red, and they will automatically switch to green when targets are achieved.
- Adaptive Titles: The titles of our visualizations are dynamic, providing immediate feedback on performance. They update to reflect the number of months targets were met versus those when they were not, offering a clear and concise summary at a glance.
- Interactive Elements: Leveraging the inherent interactivity of Power BI dashboards, we have incorporated a team slicer. This allows users to filter and interact with the data based on different teams, making it easier to drill down into specific segments and gain deeper insights.
These features ensure that our dashboard not only presents data effectively but also makes it easy for users to engage with and interpret the information, driving better decision-making

### Insights

The dashboard we have created effectively addresses the needs of our stakeholders. It clearly displays all the important and requested KPIs, highlights the months when targets were met, identifies the individuals who achieved their targets, and provides a brief summary of the overall performance. This comprehensive solution ensures that stakeholders have a clear and actionable understanding of the company's sales performan

