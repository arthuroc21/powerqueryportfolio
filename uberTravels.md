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