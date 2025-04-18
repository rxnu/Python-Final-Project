# Final-Project-Statistical-Modelling-with-Python

## Project/Goals

This project explored how the urban environment—specifically nearby amenities—might influence bike availability at public stations in Barcelona. Using real-time data from the CityBikes and Foursquare APIs, I aimed to:

- Map and analyze the spatial distribution of Bicing bike stations across the city.
- Gather and summarize nearby Points of Interest (POIs) within a 1000-meter radius of each station to understand their urban context.
- Investigate whether certain types of amenities—especially food and restaurant-related categories—might be associated with differences in bike availability.
- Test out this relationship using exploratory data analysis (EDA) and multiple linear regression to identify potential patterns and quantify associations.
---

## Process

### Step 1: Access and Clean CityBikes API Data

- Sent a request to the CityBikes API for the "bicing" network in Barcelona.
- Parsed the response to extract useful station data:
  - Station name
  - Latitude and longitude
  - Free bikes
  - Empty slots
  - Station ID
- Saved the cleaned data to a CSV file (`bicing.csv`) for use in subsequent steps.

### Step 2: Enrich Data with Foursquare API

- Loaded the bike station data and looped through each station's coordinates.
- Queried the Foursquare Places API for nearby POIs within a 1000m radius.
- Extracted data for each POI:
  - Name, latitude/longitude, category, location, and distance from the station.
- Summarized categories for each station to get an overview of the POI landscape.
- Stored both raw responses and summarized data into JSON files for backup.
- Constructed a DataFrame (`poi.csv`) with parsed POI data for further analysis.

### Step 3: Merge and Analyze Datasets

- Merged the station and POI datasets using shared indices.
- Renamed and cleaned columns to avoid confusion during merging.
- Conducted initial exploratory data analysis (EDA) to identify potential relationships.

### Step 4: Visualize and Explore Relationships

- Used visualizations (scatter plots, boxplots, correlation heatmaps) to explore relationships.
- Focused on restaurant-related POIs (keywords: restaurant, tapas, bar, cafe, etc.).
- Counted the number of restaurants within proximity to each station.
- Merged this count with station-level free bike availability.
- Visualized the relationship using a scatter plot with regression line.
- Computed correlation and basic descriptive statistics.
- Created an SQLite3 database (`bike_poi_data.db`) in the `data/` directory, inserting data from `bicing.csv` and `poi.csv` with appropriate schema and validated joins.

### Step 5: Modeling the Data

- Built a multiple linear regression model using `statsmodels`.

**Predictors included:**

- Number of nearby restaurants  
- Average distance to restaurants  
- Longitude of the station  
- Interaction term between restaurant count and distance  

- Scaled numeric variables and checked for null values.
- Ran model diagnostics (R-squared, p-values, condition number).

---

## Results

- **R-squared = 0.037** → about 3.7% of the variance in free bikes was explained by the model.
- Model was statistically significant overall (F(4, 510) = 4.881, p = 0.0007).

**Individual predictors:**

- **Nearby restaurants (β = -2.39, p = 0.015):**  
  Negative relationship → fewer bikes in areas with more restaurants, likely due to higher demand.

- **Average distance to restaurants (β = -0.005, p = 0.025):**  
  Slight negative relationship → stations further from restaurants had fewer bikes.

- **Longitude (β = 42.71, p = 0.001):**  
  Positive effect → stations farther east had more available bikes.

- **Interaction term (β = 0.0028, p = 0.041):**  
  Significant → combined effect of restaurant count and distance impacts availability.

### API Coverage Quality

While I had initially planned to use additional APIs (like Yelp) to access popularity and rating metrics, I was unable to do so due to account restrictions (e.g., requiring paid access).

However, the restaurant data I was able to collect provided strong category diversity and coverage — including a wide range of cuisine and restaurant types (over 70 distinct types).

Although popularity metrics weren't available, the variety in restaurant types allowed for meaningful spatial and contextual analysis of urban amenity distribution.

### Reflection

Although the model didn’t explain a large portion of variance, it revealed subtle patterns.

Insights suggest that restaurant proximity may influence bike demand, with denser areas likely experiencing higher turnover.

The east-west variation in bike availability may point to differences in neighborhood infrastructure or commuting preferences.

It was rewarding to see even modest statistical effects emerge from real-world data—it validated the exploratory process and showed the value of combining public APIs with urban analytics.

---

## Challenges

- **Joining datasets from Part 1 and Part 2:**  
  One of the biggest challenges was figuring out how to effectively merge the Bicing and restaurant datasets. It took significant time to align them properly, especially since the naming wasn’t consistent across both. I eventually created a new `'station'` column in `df_poi` using a loop to match the formatting, which allowed for a successful merge into `df_merged`.

- **Code structure and debugging:**  
  As the project progressed, my code became long and difficult to follow for EDA. It was originally written in a very linear way, with all steps run sequentially in one block. I could have used simple functions to avoid repeating similar code blocks—especially during the plotting section—and made the script more modular in case I wanted to expand it in the future

- **Difficulty identifying patterns through EDA:**  
  Even after a full exploratory analysis, many of the trends I expected—such as correlations between restaurant proximity and bike availability—weren’t immediately obvious. They didn’t show up clearly in plots or correlation matrices, which made it hard to decide which features were worth keeping.

- **Multicollinearity in the model:**  
  While interpreting the regression model results, I noticed signs of multicollinearity—specifically, a very out of range high condition number. This added complexity to understanding how individual features contributed to the model and highlighted the importance of more thoughtful feature selection.

---

## Future Goals

- **Time-based analysis:**  
  In future versions, I’d like to collect and analyze data at multiple time points throughout the day. This would allow me to see how bike availability changes during rush hours, weekends, or late nights—adding a valuable temporal layer to the analysis.

- **Incorporating external data:**  
  Adding contextual factors such as weather conditions, public transit stops, or socioeconomic indicators could give more depth to the model and help explain variability that wasn’t captured by restaurant data alone. 

- **Exploring nonlinear models:**  
  Since the linear model only explained a small portion of the variance, I’d consider trying more flexible models like decision trees or random forests to uncover nonlinear relationships.

- **Addressing multicollinearity:**  
  To improve model interpretability and robustness, I’d explore regularization techniques like LASSO or use dimensionality reduction (e.g., PCA) to minimize the impact of correlated features.
