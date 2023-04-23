# Uber Travels Power Query
## Intro

This is a "little bit of Power Query" that I did to build a Power BI dashboard. This is part of my whole "Uber Travels Dashboard Project", so don't forget to check the SQL code lines that I did to manipulate the raw data.

## Code

To begin with, I had to connect to the database. For this project I connected to a Postsgres database.
```M
Source = PostgreSQL.Database("localhost", "uberTravels"),
    travels_travels_2 = Source{[Schema="travels",Item="travels_2"]}[Data],
```

Then I changed the type of the columns that represented date/time. This was important so I could do some operations with the values.
```M
#"Changed Type" = Table.TransformColumnTypes(travels_travels_2,{{"request_time", type datetime}, {"begin_trip_time", type datetime}, {"dropoff_time", type datetime}}),
```

Right after, I calculated the spent hour in each travel, doing the difference between the beginning travel time and the arriving travel time.
```M
 #"Added Custom" = Table.AddColumn(#"Changed Type", "time_spent", each [dropoff_time] - [begin_trip_time]),
```

From that I could calculate the total spent hours.
```M
#"Calculated Total Hours" = Table.TransformColumns(#"Added Custom",{{"time_spent", Duration.TotalHours, type number}}),
```

Now I could add some columns to help the construction of the charts. I added a column to show the beginning travel hour in a 24-hour format, I added a column showing the name of the day of the week, the abbreviated name of the day of the week and the number of the day of the week from 0 (Sunday) to 6 (Saturday). And a couple of column names were changed, just to make sure each one was well-identified.
```M
    #"Calculated Total Hours" = Table.TransformColumns(#"Added Custom",{{"time_spent", Duration.TotalHours, type number}}),
    #"Inserted Hour" = Table.AddColumn(#"Calculated Total Hours", "Hour", each Time.Hour([begin_trip_time]), Int64.Type),
    #"Renamed Columns" = Table.RenameColumns(#"Inserted Hour",{{"Hour", "begin_time"}}),
    #"Inserted Day Name" = Table.AddColumn(#"Renamed Columns", "Day Name", each Date.DayOfWeekName([begin_trip_time]), type text),
    #"Renamed Columns1" = Table.RenameColumns(#"Inserted Day Name",{{"Day Name", "day_name"}}),
    #"Inserted First Characters" = Table.AddColumn(#"Renamed Columns1", "First Characters", each Text.Start([day_name], 3), type text),
    #"Renamed Columns2" = Table.RenameColumns(#"Inserted First Characters",{{"First Characters", "day_name_abv"}}),
    #"Inserted Day of Week" = Table.AddColumn(#"Renamed Columns2", "Day of Week", each Date.DayOfWeek([begin_trip_time]), Int64.Type),
    #"Renamed Columns3" = Table.RenameColumns(#"Inserted Day of Week",{{"Day of Week", "day_of_week"}})
```

And that was it :) I'm gonna leave here (in another file) the complete code, just to make it easier to understand. Thank you!