## Marketing Campaign Performance

## Dashboard Link:

https://bit.ly/PowerBI-MarketingCampaignPerformance

## Problem Statement

The Marketing Campaign Performance Dataset offers a comprehensive view of the effectiveness of marketing campaigns across diverse industries, target audiences, and geographical locations. With over 200,000 rows of data spanning one year, it provides detailed metrics on campaign types, channels, engagement, acquisition costs, conversion rates, and ROI. However, businesses and marketing teams often struggle to extract actionable insights from large datasets to optimize their strategies and improve campaign outcomes.

## Problem: 

How can we leverage this extensive dataset to identify key factors that drive successful marketing campaigns, optimize resource allocation, and improve return on investment (ROI) across different campaign types, audience segments, and geographical locations?

## Key Questions:

Which marketing campaign types (e.g., email, social media, and influencer) generate the highest conversion rates and ROI for different customer segments? 

What are the optimal campaign durations and channel combinations that yield the best engagement and ROI? 

How do customer demographics (age, gender, location) impact the effectiveness of campaigns across different channels and languages? 

Which marketing channels (e.g., email, social media, YouTube) deliver the most cost-effective customer acquisition, and how can businesses optimize their budgets across these channels? 

What role does language and location play in the success of marketing campaigns, and how can businesses tailor campaigns to improve their reach in specific regions or linguistic groups?

## Objective: 

To use advanced analytics techniques to uncover patterns, correlations, and actionable insights from the dataset, enabling businesses to:
Improve campaign targeting and personalization enhance channel selection and resource allocation maximize ROI by refining marketing strategies based on data-driven insights.

## Steps followed

•	Step 1: Load data into Power BI, dataset is a csv file.

•	Step 2: Open power query and in the fCampaign table added five conditional columns to create the indexes for Location_ID, Channel_ID, Customer_ID, Target_ID, Language_ID.

•	Step 3: Duplicate five times the fact table fCampaign and create five dimension tables: dLocation, dChannel, dCustomer, dTarget, dLanguage.

•	Step 4: Remove the extra columns from the fact table, in the dimension tables remove duplicates, and change data type for the corresponding columns.

•	Step 5: Create a new query and use the advance editor and type the code to create a dCalendar table.

        let
    MinDate = List.Min (fCampaign [Date]),
    MaxDate = List.Max (fCampaign [Date]),
    YearMinDate = Date.Year (MinDate),
    YearMaxDate = Date.Year (MaxDate),
    TotalDays = Duration.Days (Date.FromText ("12/31/" & Text.From (YearMaxDate)) - Date.FromText ("01/01/" & Text.From(YearMinDate))) + 1,
    ListDays = List.Dates (Date.FromText ("01/01/" & Text.From (YearMinDate)) , TotalDays, #duration (1,0,0,0)),
    CalendarTable = Table.FromList (ListDays, Splitter.SplitByNothing (), null, null, ExtraValues.Error),
    #"Inserted Year" = Table.AddColumn(CalendarTable, "Year", each Date.Year([Column1]), Int64.Type),
    #"Inserted Month" = Table.AddColumn(#"Inserted Year", "Month", each Date.Month([Column1]), Int64.Type),
    #"Inserted Month Name" = Table.AddColumn(#"Inserted Month", "Month Name", each Date.MonthName([Column1]), type text),
    #"Inserted First Characters" = Table.AddColumn(#"Inserted Month Name", "First Characters", each Text.Start([Month Name], 3), type text),
    #"Inserted Merged Column" = Table.AddColumn(#"Inserted First Characters", "Month/Year", each Text.Combine({[First Characters], Text.From([Year], "en-US")}, "/"), type text),
    #"Changed Type" = Table.TransformColumnTypes(#"Inserted Merged Column",{{"Column1", type date}}),
    #"Renamed Columns" = Table.RenameColumns(#"Changed Type",{{"Column1", "Date"}}),
    #"Inserted Week of Year" = Table.AddColumn(#"Renamed Columns", "Week of Year", each Date.WeekOfYear([Date],  Day.Monday))
        in
    #"Inserted Week of Year"
    
•	Step 6: In Power BI check the model view, choosing a start schema and double check the table relationship, cardinality, and cross filter direction.

•	Step 7: Create the calculated measures with DAX:

        Avg. Conversion Rate = AVERAGE(fCampaign[Conversion_Rate])

        Avg. Engagement Score = AVERAGE(fCampaign[Engagement_Score])

        Avg. ROI = AVERAGE(fCampaign[ROI %]) 

        CPC cost-per-click = DIVIDE([total acquisition cost], [total clicks])

        CPM cost-per-mille = (SUM(fCampaign[Acquisition_Cost]) / SUM(fCampaign[Impressions])) * 1000

        CTR click-through rate = DIVIDE([total clicks], [total impressions])

        Last Month Avg. Conversion Rate = CALCULATE([Avg. Conversion Rate], PARALLELPERIOD(LASTDATE(dCalendar[Date]),-1,MONTH))

        Last Month Avg. Engagement Score = CALCULATE([Avg. Engagement Score], PARALLELPERIOD(LASTDATE(dCalendar[Date]),-1,MONTH))

        Last Month Avg. ROI = CALCULATE([Avg. ROI], PARALLELPERIOD(LASTDATE(dCalendar[Date]),-1,MONTH))

        Last Month CPC = CALCULATE([CPC cost-per-click], PARALLELPERIOD(LASTDATE(dCalendar[Date]),-1,MONTH))

        Last Month CPM = CALCULATE([CPM cost-per-mille], PARALLELPERIOD(LASTDATE(dCalendar[Date]),-1,MONTH))

        Last Month CTR = CALCULATE([CTR click-through rate], PARALLELPERIOD(LASTDATE(dCalendar[Date]),-1,MONTH))

        Total Acquisition Cost = SUM(fCampaign[Acquisition_Cost])

        Total Clicks = SUM(fCampaign[Clicks])

        Total Impressions = SUM(fCampaign[Impressions])

•	Step 8 : For creating the reference labels in the Card New, the following DAX expression was written:

        Dif. Conversion Rate = ([Avg. Conversion Rate] - [Last Month Avg. Conversion])

        Dif. ROI = ([Avg. ROI] - [Last Month Avg. ROI])

        Dif. Engagement Score = ([Avg. Engagement Score] - [Last Month Avg. Engagement Score])

        Dif. CPC = ([CPC cost-per-click] - [Last Month CPC])

        Dif. CTR = ([CTR click-through rate] - [Last Month CTR])

        Text dif. Conversion Rate = FORMAT([Dif. Conversion Rate], "0.00% 🟢 ; -0.00% 🔴")

        Text dif. ROI = FORMAT([Dif. ROI], "0.00% 🟢 ; -0.00% 🔴")

        Text dif. Engagement Score = FORMAT([Dif. Engagement Score], "0.00 🟢 ; -0.00 🔴")

        Text dif. CPC = FORMAT([Dif. CPC], "$0.00 🟢 ; $-0.00 🔴")

        Text dif. CTR = FORMAT([Dif. CTR], "0.00% 🟢 ; -0.00% 🔴")

•	Step 9 : In the report view, create the visualization using a canvas background previously designed in Figma.

•	Step 10: Visual filters (Slicers) were added for four fields named "Company", "Customer Segment", "Month" & "Duration".

•	Step 11 : Five cards new were added to the canvas representing Avg. Conversion Rate, Avg. Engagement Score, CPC, CTR, together with the comparison of the Last Month.

•	Step 12: Bookmarks and icon buttons were created to view the different type of charts built: line chart, line and clustered column chart, ribbon chart, scatter chart, and table chart.

## Snapshop of Dashboard (Power BI Service)

![Page 1](https://github.com/user-attachments/assets/388ef865-7b0c-4cac-9d41-64e8dfc56a89)

![Page 2](https://github.com/user-attachments/assets/be54dc34-9903-42ed-ba1f-00ca3cb550ae)



