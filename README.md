# Final-Project-Statistical-Modelling-with-Python

## Project/Goals
This project provides myself with the opportunity to combine and practice implementing what we have learned throughout this course, including:
1. Accessing data using APIs
2. Cleaning and transforming data using Python
3. Loading data into a database using Python
4. Performing EDA, including using both statistics and visualizations
5. Identifying trends and patterns in data using statistical models
6. Interpreting the results of the statistical models

## Process

My process followed the different parts of the assignment.

### Step 1 - Collect all data from CityBikes using its API on bike stations in a city of my choice (Vancouver) and store it into a CSV.

### Step 2 -  Collect all Points of Interest(POIs) in a 1000m radius of each bike station from Yelp and Foursquare API and store each into a CSV. I defined POIs as bars, restaurants and police stations. Compare the quality of Yelp and Foursquare's API. My definition of quality was 'which one provides me more accurate data with Step 4'. 

### Step 3 - Join the Data between the all collected CSV (Key aspects of this step where do while I was doing Step 2). Store data into a SQLite DB.

### Step 4 - Build a regression model that demonstrates a relationship between the number of bikes in a particular location and the characteristics of the POIs in that location. For that step, the 'characteristics of the POI' I decided to go with was the amount of venues surrounding each bike stations. 

### My initial hypothesis was that, the more restaurants there is around a bike station, the more bikes there will be available at that stat

## Results

### Foursquare vs Yelp
Yelp is returning more data and so we could conclude Yelp is providing us with more complete data, both in amount of results and in details for these results.

However, I would add the following consideration: 
- Are Yelp and Foursquare pulling exactly 1000m from the same point? Is Yelp pulling slightly over 1000m, or Foursquare slightly below? It should be noted Foursqure doesn't take the same level of precision in lat/long than Yelp is in its API query. It is possible that this is moving the point enough that the results are not the same and, consequently, could significantly disrupt the accuracy of our data.

Observing this data on a map would help to quickly see if the pulls are the same and if Yelp is indeed providing us additional results than Foursquare.

Nevertheless, Foursquare returns are capped to 50 (meaning even if there is 600 venues around a bikestation, Foursquare still returns us a value of 50). Yelp returns us 50 results as well, but it also includes a total number of returns, which will be more useful for us to use. 

Conclusion: Yelp wins.

### Model results
The R-squared value of our model is 0.007. This is a very low value as it means that our model is capable of explaining 0.7% of the pattern in the data...

With a p_value of 0.196, meaning that our variable has a greater p-value than the standard statistical treshold of 0.05. This is indicative that the variable should be removed from the model.

In conclusion, it appears there is no relationship between the number of bikes at a particular bike station and the number of bars/restaurants surrounding this bike station.

## Challenges 
### Pulling city bikes city
I had an issue with the url request.. couldn't find how to get the stations for a specific city.

Credits to Jamie, this worked (adding mobibikes at the end for Vancouver):
url = "https://api.citybik.es/v2/networks/mobibikes"

### Lat / Long  - Foursquare
Has some issues formatting the lat/long for Foursquare, until I properly read the error code. It had to be rounded!
    lat= round(j, 2)
    long= round(k,2)

### Yelp API - Authorization
I had some issues pushing my API key to yelp.. After some research online, adding bearer worked:
```python
headers = {'Authorization': 'Bearer {}'.format(api_key)}
```

### Assigning the Bike Station ID values to the Foursquare/Yelp dfs
When doing the API pull and putting it into a DF, I wanted to add the bikestation ID as a column, to make things easier to join later. 

The solution I found was to add ID in the get_venues() function so when I was calling get_venues from the for loop, I could push the ID to the function.

### Consolidated DF for Yelp and Foursquare
When doing the pulls from Yelp and Foursquare, I was working with 2x DF. One temporary and one which contained the consolidated results. 

I had an error code working with the consolidated one, probably from being moved around thru the for loop and then thru each instance of the get_venues function. Online research provided that adding global in the function would resolve the issue:
```python
    global yelp_df

    yelp_df = pd.concat([yelp_df, add_df], ignore_index=True)
```
Honestly still not sure why, but it works!


## Future Goals
### Getting more details on each venues
As we only had detailed results on 50 venues per bikestation, I didn't work with more details that Yelp provides on these venues (ie. rating).

However, as all our results are under a 1000, there is a way using the Yelp API to get the details for all (using the offset). However, with our API limit, it would take days, if not weeks to collect all this data.
