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

- Used visualizations (scatter plots) to explore relationships.
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

**Preprocessing:**
- Scaled all numeric variables to standardize them (mean = 0, SD = 1).  
- Scaled numeric variables and checked for null values.
- Ran model diagnostics (R-squared, p-values, condition number).


---

## Results

- **R-squared = 0.037** → about 3.7% of the variance in free bikes was explained by the model.
- Model was statistically significant overall (F(4, 510) = 4.881, p = 0.0007).

**Individual predictors:**

- **Nearby restaurants (β = -3.3371, p = 0.015):**  
  Negative relationship → stations located near more restaurants tended to have fewer available bikes. This may reflect higher local demand—i.e., people are more likely to bike to areas with a high concentration of restaurants and amenities, resulting in lower bike availability.

- **Average distance to restaurants (β = -1.1163, p = 0.025):**  
  Slight negative relationship → stations further from restaurants had fewer bikes. This may indicate that stations in less amenity-dense areas experience less frequent bike redistribution or usage.

- **Longitude (β = 1.0473, p = 0.001):**  
  Positive effect → stations farther east had more available bikes This might reflect differences in urban planning, infrastructure, or commuting behavior across different neighborhoods of the city.

- **Interaction term (β = 2.6523, p = 0.041):**  
  Significant → combined effect of restaurant count and distance impacts availability. This term helped capture the combined effect of density and proximity on bike usage.

### API Coverage Quality

Although it was planned use additional APIs (such as Yelp) to incorporate restaurant popularity and rating metrics, account limitations (e.g., requiring a paid tier) restricted access. Still, the restaurant dataset I collected offered strong categorical coverage—including over 70 distinct cuisine and restaurant types.

While I couldn’t include popularity-based variables, the breadth and diversity of restaurant types allowed for meaningful insights into the spatial distribution of amenities and their potential relationship with shared mobility infrastructure.

### Reflection

Even though the model didn’t explain a large portion of variance, it did reveal subtle, real-world patterns. The data suggested that restaurant proximity and density are associated with reduced bike availability, likely due to increased foot traffic and bike usage in vibrant commercial areas. Additionally, the east-west variation in bike availability points to potential differences in neighborhood design, infrastructure investment, or commuter behavior.

This project validated the value of combining public APIs with open city data—and even with limited resources, I was able to extract  insights. It also reinforces the reality that real-world data science often involves incremental discovery rather than perfect prediction.

---

## Challenges

- **Joining datasets from Part 1 and Part 2:**  
  One of the biggest challenges was figuring out how to effectively merge the Bicing and restaurant datasets. It took significant time to align them properly, especially since the naming wasn’t consistent across both. I eventually created a new `'station'` column in `df_poi` using a loop to match the formatting, which allowed for a successful merge into `df_merged`.

- **Code structure and debugging:**  
  As the project progressed, my code became long and difficult to follow for EDA. It was originally written in a very linear way, with all steps run sequentially in one block. I could have used simple functions to avoid repeating similar code blocks—especially during the plotting section—and made the script more modular in case I wanted to expand it in the future.

- **Difficulty identifying patterns through EDA:**  
  It wasn't always clear what was considered "good" Even after a full exploratory analysis, many of the trends I expected—such as correlations between restaurant proximity and bike availability—-were not immediately apparent or obvious in the visualizations or correlation matrices, making it difficult to determine which features were worth keeping in the model.

- **Multicollinearity in the model:**  
 While interpreting the regression model results, I noticed signs of multicollinearity, especially with a high condition number. To address this, I standardized the numeric variables (scaling them to have a mean of 0 and standard deviation of 1), which helped reduce some of the multicollinearity and improved the interpretability of the coefficients. However, the issue still highlighted the need for more refined feature selection.
---

## Future Goals

- **Time-based analysis:**  
  In future versions, I would collect and analyze data at multiple time points throughout the day. This would allow me to see how bike availability changes during rush hours, weekends, or late nights—adding a valuable temporal layer to the analysis.

- **Incorporating external data:**  
  Adding contextual factors such as weather conditions, public transit stops, or socioeconomic indicators could give more depth to the model and help explain variability that wasn’t captured by restaurant data alone. 

- **Exploring nonlinear models:**  
  Since the linear model only explained a small portion of the variance, I’d consider exploring more flexible, nonlinear models like decision trees, random forests, or gradient boosting machines. These models can better capture complex, non-linear relationships between predictors and the outcome.

