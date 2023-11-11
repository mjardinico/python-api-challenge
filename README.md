# Module 6 Challenge - API Challenge
### UC Berkeley Data Analytics Bootcamp 2023

**Author:** Michael Jardinico  
**Version:** 1.0  
**Date Created:** 11/10/2023

## Description
This project explores the relationship between the weather and the distance from the equator. Utilizing Python requests, APIs, and JSON traversal, we aim to answer the question: "What is the weather like as we approach the equator?" This project not only confirms the expected trend - that it gets hotter near the equator - but also provides empirical evidence to support this observation.


## Installation
Before running the project, ensure the following libraries are installed in your Python environment:

1. Jupyter Notebook
2. matplotlib
3. pandas
4. numpy
5. requests
6. time
7. scipy
8. pprint
9. citipy


## Project Setup
Follow these steps to set up the project environment:

1. Create a new repository on GitHub named `python-api-challenge`.
2. Clone the repository to your local machine.
3. Within your local repository, create a directory named `WeatherPy`.
4. Inside the `WeatherPy` directory, create the following files and folder:
    - WeatherPy.ipynb
    - VacationPy.ipynb
    - api_keys.py
    - output_data/

5. In `WeatherPy.ipynb`, start by importing the necessary libraries and setting up the environment:

    import matplotlib.pyplot as plt
    import pandas as pd
    import numpy as np
    import requests
    import time
    from scipy import stats
    from pprint import pprint
    from citipy import citipy
    from api_keys import weather_api_key   #include api_keys inside .gitignore


6. Generating the cities list using the `citipy` library.
   - One of the key features of this project is the generation of a list of cities based on random latitude and longitude.
      - Create random latitude and longitude values
      - Using hte 'citipy' library to find the nearest city to each latitude and longitude combination.
      - Ensure uniqueness of each city in the final list.
   
    # Create a set of rand lat and lng combinations
    lats = np.random.uniform(-90, 90, size=1000)
    lngs = np.random.uniform(-180, 180, size=1000)
    lat_lngs = zip(lats, lngs)

    ## Identify nearest city for each lat, lng combination
    cities = []
    for lat_lng in lat_lngs:
        city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name
        if city not in cities:
            cities.append(city)
    print(f"Number of cities in the list: {len(cities)}")


7. Requirement 1: Create plots to showcase the relationship between weather variables and latitude

    ## Data Retrieval Process
    This project involves fetching weather data for each city in the generated city list. The process includes:

    - Setting up the API base URL for OpenWeatherMap.
    - Creating a loop to process each city and fetch its weather data.
    - Handling API request limits by grouping cities and introducing a pause.
    - Parsing JSON responses to extract relevant weather information.
    - Handling exceptions for cities that might not be found in the API.

    # Below is an overview of how this is implemented:

  
    # Base URL for the OpenWeatherMap API
    url = "http://api.openweathermap.org/data/2.5/weather?"

    # Data retrieval process
    city_data = []
    record_count = 1
    set_count = 1

    for i, city in enumerate(cities):
        # Group cities for logging and pause the script to avoid API rate limits
        if (i % 50 == 0 and i >= 50):
            set_count += 1
            record_count = 1
            time.sleep(25)

        # Create the endpoint URL for each city
        city_url = url + "appid=" + weather_api_key + "&q=" + city
        print("Processing Record %s of Set %s | %s" % (record_count, set_count, city))
        record_count += 1

        # API request and data parsing (exception handling for cities not found)
        try:
            city_weather = requests.get(city_url).json()
            # Extract and convert relevant data here
            # Append data to city_data list
        except:
            print("City not found. Skipping...")
            pass

    print("Data Retrieval Complete")

    # Convert the cities weather data into a Pandas DataFrame
    city_data_df = pd.DataFrame(city_data)

    # Show Record Count
    city_data_df.count()

    # Export the City_Data into a csv
    city_data_df.to_csv("output_data/cities.csv", index_label="City_ID")



8. Create the Scatter Plots for Latitude Vs. Temperature
    # Build scatter plot for latitude vs. temperature
    plt.scatter(city_data_df['Lat'], city_data_df['Max Temp'], marker="o", edgecolors='black', linewidth=1)

    # Incorporate the other graph properties
    plt.title("Latitude vs Temperature in World Cities")
    plt.ylabel("Temperature (Fahrenheit)")
    plt.xlabel("Latitude")

    # Save the figure
    plt.savefig("output_data/Fig1.png")

    # Show plot
    plt.show()


9. Create the Scatter Plots for Latitude Vs. Humidity

   # Build the scatter plots for latitude vs. humidity
    plt.scatter(city_data_df['Lat'], city_data_df['Humidity'], marker="o", edgecolors='blue', linewidth=1)

    # Incorporate the other graph properties
    plt.title("Latitude vs Humidity in World Cities")
    plt.ylabel("Humidity")
    plt.xlabel("Latitude")

    # Save the figure
    plt.savefig("output_data/Fig2.png")

    # Show plot
    plt.show()


10. Create the Scatter Plots for Latitude Vs. Cloudiness

    plt.scatter(city_data_df['Lat'], city_data_df['Cloudiness'], marker="o", edgecolors="red", linewidth=1)

    # Incorporate the other graph properties
    plt.title("Latitude vs Cloudiness in World Cities")
    plt.ylabel("Cloudiness")
    plt.xlabel("Latitude")

    # Save the figure
    plt.savefig("output_data/Fig3.png")

    # Show plot
    plt.show()


11. Create the Scatter Plots for Latitude Vs. Wind Speed

    plt.scatter(city_data_df['Lat'], city_data_df['Wind Speed'], marker='o', edgecolors='orange', linewidth=1)

    # Incorporate the other graph properties
    plt.title('Latitude vs Wind Speed in World Cities')
    plt.ylabel('Wind Speed')
    plt.xlabel('Latitude')

    # Save the figure
    plt.savefig("output_data/Fig4.png")

    # Show plot
    plt.show()


12. Requirement 2: Compute Linear Regression for Each Relationship 
    
    def linear_regression_plot(x_values, y_values, x_label, y_label):
    
    # perform linear regression
    (slope, intercept, r_value, p_value, std_err) = stats.linregress(x_values, y_values)
    
    # Get regression values
    regress_values = x_values * slope + intercept
    
    # Create line equation string
    line_eq = "y = " + str(round(slope,2)) + "x +" + str(round(intercept,2))
    
    # create plot
    plt.scatter(x_values, y_values)
    plt.plot(x_values, regress_values, "r-")
    
    # label plot and annotate the line equation
    plt.xlabel(x_label)
    plt.ylabel(y_label)
    plt.annotate(line_eq, (x_values.median(), y_values.median()), fontsize=15, color="red")
    
    # Show plot
    plt.show()
    return slope, intercept, r_value, p_value, std_err



13. Create Linear Regression Plot of Temperature vs. Latitude
    # Linear regression on Northern Hemisphere
    x_values = northern_hemi_df['Lat']
    y_values = northern_hemi_df['Max Temp']
    x_label = 'Latitude'
    y_label = 'Temperature (F)'
    linear_regression_plot(x_values,y_values,x_label,y_label)

    # Linear regression on Southern Hemisphere
    x_values = southern_hemi_df['Lat']
    y_values = southern_hemi_df['Max Temp']
    x_label = 'Latitude'
    y_label = 'Temperature (F)'
    linear_regression_plot(x_values,y_values,x_label,y_label)


14. Create Linear Regression Plot of Humidity vs. Latitude
    # Northern Hemisphere
    x_values = northern_hemi_df['Lat']
    y_values = northern_hemi_df['Humidity']
    x_label = 'Latitude'
    y_label = 'Humidity'
    linear_regression_plot(x_values,y_values,x_label,y_label)

    # Southern Hemisphere
    x_values = southern_hemi_df['Lat']
    y_values = southern_hemi_df['Humidity']
    x_label = 'Latitude'
    y_label = 'Humidity'
    linear_regression_plot(x_values,y_values,x_label,y_label)

15. Create Linear Regression Plot of Cloudiness vs. Latitude
    # Northern Hemisphere
    x_values = northern_hemi_df['Lat']
    y_values = northern_hemi_df['Cloudiness']
    x_label = 'Latitude'
    y_label = 'Cloudiness'
    linear_regression_plot(x_values,y_values,x_label,y_label)

    # Southern Hemisphere
    x_values = southern_hemi_df['Lat']
    y_values = southern_hemi_df['Cloudiness']
    x_label = 'Latitude'
    y_label = 'Cloudiness'
    linear_regression_plot(x_values,y_values,x_label,y_label)


16. Create Linear Regression Plot of Wind Speed vs. Latitude
    # Northern Hemisphere
    x_values = northern_hemi_df['Lat']
    y_values = northern_hemi_df['Wind Speed']
    x_label = 'Latitude'
    y_label = 'Wind Speed'
    linear_regression_plot(x_values,y_values,x_label,y_label)

    # Southern Hemisphere
    x_values = southern_hemi_df['Lat']
    y_values = southern_hemi_df['Wind Speed']
    x_label = 'Latitude'
    y_label = 'Wind Speed'
    linear_regression_plot(x_values,y_values,x_label,y_label)




## Contributing
Contributions to this project are welcome. Please ensure that any pull requests are clearly described and have been tested before submission.

## License
TBD



