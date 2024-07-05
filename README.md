# Monitoring Barrage Massira Using Google Earth Engine & Python

This project utilizes Google Earth Engine (GEE) and Python to monitor the Barrage Massira, one of the largest reservoirs in Morocco. The analysis involves data processing, visualization, and generating insights about the water levels and changes over time.

## Project Overview

### Barrage Massira

Barrage Massira, also known as the Massira Dam, is a large dam located on the Oum Er-Rbia River in Morocco. It is a crucial water reservoir for agricultural irrigation, drinking water supply, and hydroelectric power generation. The dam's construction began in 1979 and it has since become a vital resource for the region.

### Objective

The main objective of this project is to monitor the changes in water levels at Barrage Massira using satellite imagery and remote sensing techniques provided by Google Earth Engine. The project aims to visualize these changes over time and provide meaningful insights.

## Prerequisites

Before running the notebook, ensure you have the following prerequisites:

1. **Google Earth Engine Account**: Sign up for a Google Earth Engine account [here](https://earthengine.google.com/signup/).
2. **Python Libraries**: Install the required Python libraries using the following commands:

    ```bash
    pip install geemap
    pip install matplotlib
    pip install pandas
    ```

## Project Structure

- **Data Collection**: Utilize Google Earth Engine to collect satellite imagery and relevant data for Barrage Massira.
- **Data Processing**: Process the collected data to extract meaningful information, filter images, and create visual representations.
- **Visualization**: Generate visualizations to understand the changes in water levels over time.
- **GIF Creation**: Create an animated GIF to show the temporal changes in Barrage Massira.
- **Add Text to GIF**: Annotate the GIF with relevant text information, such as year labels.

## How to Run the Notebook

1. **Authentication and Initialization**: Authenticate and initialize the Google Earth Engine API.

    ```python
    import geemap
    import ee
    import matplotlib.pyplot as plt
    import pandas as pd
    import subprocess

    ee.Authenticate()
    ee.Initialize(project='monitoring-inland-water')
    ```

2. **Define Geometry and Image Collection**: Define the geographical region and image collection.

    ```python
    geometry = ee.Geometry.Rectangle([-180, -90, 180, 90])
    image_collection = ee.ImageCollection('LANDSAT/LC08/C01/T1_32DAY_NDWI')
    areaImages = image_collection.map(lambda image: image.updateMask(image.select('waterclass').gt(0)))
    ```

3. **Filter and Visualize Images**: Filter images with zero area and set visualization parameters.

    ```python
    image_collection = areaImages.filter(ee.Filter.gt('area_km2', 0))
    visParams = {
        'bands': ['waterclass'],
        'palette': ['blue']
    }
    images = image_collection.map(lambda image: image.visualize(min=0, max=1, palette=['black', 'blue']).selfMask())
    ```

4. **Create and Download GIF**: Define GIF parameters, create the GIF, and download it.

    ```python
    gifParams = {
        'region': geometry,
        'dimensions': 600,
        'framesPerSecond': 1
    }
    url = images.getVideoThumbURL(gifParams)
    subprocess.run(["wget", url, "-O", "surface_water.gif"])
    ```

5. **Add Text to GIF**: Create a sequence of text for each year and add it to the GIF.

    ```python
    # Assuming 'df' is a DataFrame with a 'year' column
    df = pd.DataFrame({'year': range(2000, 2020)})  # Example DataFrame, replace with your actual data

    text_sequence = [f"Year: {year}" for year in df.year]
    new_file_name = "new_surface_water.gif"

    geemap.add_text_to_gif(
        "surface_water.gif",
        new_file_name,
        xy=("3%", "5%"),
        text_sequence=text_sequence,
        font_size=30,
        font_color="#ffffff",
        duration=1000  # in milliseconds
    )
    ```

## Conclusion

This project provides a comprehensive approach to monitoring water levels at Barrage Massira using advanced remote sensing and data processing techniques. By leveraging the power of Google Earth Engine and Python, significant insights can be gained into the temporal changes occurring in this critical water resource.
