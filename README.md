### Step 1: Import Required Libraries 
   The first step is importing the necessary libraries:
   ```python
   import pandas as pd
   import geopandas as gpd
   import matplotlib.pyplot as plt
   from matplotlib.lines import Line2D
   from matplotlib.colors import Normalize
   ```
   - `pandas`: For working with tabular data like CSV files.
   - `geopandas`: For handling geospatial data.
   - `matplotlib.pyplot`: For plotting and visualizations.
   - `Line2D`: Used for creating custom legend elements.
   - `Normalize`: Used for normalizing data values to a specific range (e.g., for color mapping).
---
### Step 2: Load the data
```python
def load_data(intervention_path, population_path, shapefile_path):
    """Load intervention, population, and shapefile data."""
    intervention_data = pd.read_excel(intervention_path)
    population_data = pd.read_excel(population_path)
    shapefile = gpd.read_file(shapefile_path)
    intervention_data['year'] = intervention_data['year'].astype(int)  # Ensure year is integer
    return intervention_data, population_data, shapefile
```
The `load_data` function is designed to load and process intervention data, population data, and geographic shapefile data for further analysis. Below is a step-by-step explanation of the function:

#### Function Definition
   - **Code:** `def load_data(intervention_path, population_path, shapefile_path):`
   - **Explanation:** The function `load_data` takes three arguments:
     - `intervention_path`: The file path to the intervention data (Excel file).
     - `population_path`: The file path to the population data (Excel file).
     - `shapefile_path`: The file path to the geographic shapefile (GIS file).
#### **Docstring**
   - **Code:** `'''Load intervention, population, and shapefile data.'''`
   - **Explanation:** Provides a brief description of the function's purpose.
#### **Load Intervention Data**
   - **Code:** `intervention_data = pd.read_excel(intervention_path)`
   - **Explanation:** Uses the `pandas` library to read the Excel file specified by `intervention_path` and stores the data in a DataFrame called `intervention_data`.
#### **Load Population Data**
   - **Code:** `population_data = pd.read_excel(population_path)`
   - **Explanation:** Similarly, uses `pandas` to read the Excel file specified by `population_path` and stores the data in a DataFrame called `population_data`.
#### **Load Shapefile Data**
   - **Code:** `shapefile = gpd.read_file(shapefile_path)`
   - **Explanation:** Uses the `geopandas` library to read the shapefile specified by `shapefile_path` and stores the data in a GeoDataFrame called `shapefile`.
#### **Convert Year Column to Integer**
   - **Code:** `intervention_data['year'] = intervention_data['year'].astype(int)`
   - **Explanation:** Converts the `year` column in the `intervention_data` DataFrame to an integer data type to ensure consistency for further analysis. This ensures that year values are treated as numerical integers.
#### **Return Data**
   - **Code:** `return intervention_data, population_data, shapefile`
   - **Explanation:** Returns the loaded and processed `intervention_data`, `population_data`, and `shapefile` as outputs of the function.
---
### Step 3: Summarize intervention data by year
```python
def summarize_intervention(intervention_data):
    """Summarize llins_penta3_tot by adm3 for each year."""
    years = intervention_data['year'].unique()
    summarized = pd.DataFrame()
    for year in years:
        yearly_data = intervention_data[intervention_data['year'] == year]
        yearly_summed = yearly_data.groupby('adm3', as_index=False)['llins_penta3_tot'].sum()
        yearly_summed.rename(columns={'llins_penta3_tot': f'llins_penta3_tot_{year}'}, inplace=True)
        summarized = yearly_summed if summarized.empty else pd.merge(summarized, yearly_summed, on='adm3', how='outer')
    return summarized, years
```
The `summarize_intervention` function is designed to summarize intervention data for each year by grouping data at the `adm3` level and calculating the total values of a specific variable (`llins_penta3_tot`) for each year. Below is a detailed step-by-step explanation of the function:

#### Function Definition
   - **Code:** `def summarize_intervention(intervention_data):`
   - **Explanation:** The function `summarize_intervention` takes one argument:
     - `intervention_data`: A DataFrame containing intervention data with columns such as `year`, `adm3`, and `llins_penta3_tot`.

#### **Docstring**
   - **Code:** `'''Summarize llins_penta3_tot by adm3 for each year.'''`
   - **Explanation:** Provides a brief description of the function's purpose, specifying the column to be summarized (`llins_penta3_tot`) and the grouping level (`adm3`).

#### **Extract Unique Years**
   - **Code:** `years = intervention_data['year'].unique()`
   - **Explanation:** Retrieves all unique values from the `year` column in the `intervention_data` DataFrame. These unique years will be used to loop through and summarize the data year by year.
#### **Initialize Empty DataFrame**
   - **Code:** `summarized = pd.DataFrame()`
   - **Explanation:** Creates an empty DataFrame named `summarized` to store the results of the yearly summaries.
#### **Loop Through Each Year**
   - **Code:** `for year in years:`
   - **Explanation:** Iterates over each unique year in the `years` array. The loop processes the intervention data one year at a time.
#### **Filter Data for the Current Year**
   - **Code:** `yearly_data = intervention_data[intervention_data['year'] == year]`
   - **Explanation:** Filters the `intervention_data` DataFrame to include only rows corresponding to the current year being processed in the loop. This creates a subset of the data specific to that year, stored in `yearly_data`.
#### **Group and Summarize Data**
   - **Code:** `yearly_summed = yearly_data.groupby('adm3', as_index=False)['llins_penta3_tot'].sum()`
   - **Explanation:** Groups the `yearly_data` by the `adm3` column and calculates the total (`sum`) of the `llins_penta3_tot` column for each `adm3`. The result is a new DataFrame, `yearly_summed`, containing the summarized data for that year.
#### **Rename Summarized Column**
   - **Code:** `yearly_summed.rename(columns={'llins_penta3_tot': f'llins_penta3_tot_{year}'}, inplace=True)`
   - **Explanation:** Renames the column `llins_penta3_tot` in the `yearly_summed` DataFrame to include the year in its name (e.g., `llins_penta3_tot_2024`). This ensures that each year’s data has a unique column in the final output.
#### **Merge with Summarized Data**
   - **Code:**
     ```python
     summarized = yearly_summed if summarized.empty else pd.merge(
         summarized, yearly_summed, on='adm3', how='outer'
     )
     ```
   - **Explanation:** Combines the `yearly_summed` DataFrame for the current year with the existing `summarized` DataFrame:
     - If `summarized` is empty (first iteration), it is directly assigned the `yearly_summed` data.
     - Otherwise, the `yearly_summed` data is merged with the existing `summarized` DataFrame on the `adm3` column, using an outer join to include all `adm3` values across years.

---

#### **Return Summarized Data and Years**
   - **Code:** `return summarized, years`
   - **Explanation:** Returns two outputs:
     - `summarized`: A DataFrame containing the summarized data for all years, with columns named by year (e.g., `llins_penta3_tot_2024`).
     - `years`: The array of unique years extracted from the input data. This provides a reference to the years included in the summary.
---

### Step 4: Merge data and calculate coverage
```python
def merge_and_calculate_coverage(population_data, shapefile, intervention_summary, years):
    """Merge population and shapefile data, and calculate coverage."""
    merged_pop = population_data.merge(intervention_summary, on='adm3', how='left', validate='1:1')
    gdf = shapefile.merge(merged_pop, how='left', on=['FIRST_DNAM', 'FIRST_CHIE'], validate='1:1')

    for year in years:
        pop_column, penta_column, coverage_column = f'pop{year}', f'llins_penta3_tot_{year}', f'coverage_{year}'
        if pop_column in gdf.columns and penta_column in gdf.columns:
            gdf[coverage_column] = (gdf[penta_column] / (gdf[pop_column] * 0.027)) * 100
            print(f"Calculated {coverage_column} for year {year}")
        else:
            print(f"Skipping {year}: Missing {pop_column} or {penta_column}")
    return gdf
```
The `merge_and_calculate_coverage` function is designed to merge population data, shapefile data, and summarized intervention data, and then calculate the coverage for each year. Below is a step-by-step explanation of the function:

#### Function Definition
   - **Code:** `def merge_and_calculate_coverage(population_data, shapefile, intervention_summary, years):`
   - **Explanation:** The function `merge_and_calculate_coverage` takes four arguments:
     - `population_data`: A DataFrame containing population data.
     - `shapefile`: A GeoDataFrame containing geographic information.
     - `intervention_summary`: A DataFrame containing summarized intervention data.
     - `years`: A list or array of years to calculate coverage for.
#### Docstring
   - **Code:** `'''Merge population and shapefile data, and calculate coverage.'''`
   - **Explanation:** Provides a brief description of the function's purpose.
#### Merge Population Data with Intervention Summary
   - **Code:**
     ```python
     merged_pop = population_data.merge(intervention_summary, on='adm3', how='left', validate='1:1')
     ```
   - **Explanation:** Merges the `population_data` DataFrame with the `intervention_summary` DataFrame on the `adm3` column using a left join:
     - `on='adm3'`: Specifies the column to merge on.
     - `how='left'`: Ensures that all rows from the `population_data` are retained.
     - `validate='1:1'`: Validates that the merge is one-to-one (each `adm3` in both datasets appears only once).
#### Merge with Shapefile
   - **Code:**
     ```python
     gdf = shapefile.merge(merged_pop, how='left', on=['FIRST_DNAM', 'FIRST_CHIE'], validate='1:1')
     ```
   - **Explanation:** Merges the `shapefile` GeoDataFrame with the `merged_pop` DataFrame:
     - `on=['FIRST_DNAM', 'FIRST_CHIE']`: Specifies the columns to merge on.
     - `how='left'`: Ensures all rows from the `shapefile` are retained.
     - `validate='1:1'`: Validates that the merge is one-to-one for these columns.
#### Loop Through Each Year
   - **Code:** `for year in years:`
   - **Explanation:** Iterates over each year in the `years` list or array to calculate coverage.
#### Define Column Names for the Year
   - **Code:**
     ```python
     pop_column, penta_column, coverage_column = f'pop{year}', f'llins_penta3_tot_{year}', f'coverage_{year}'
     ```
   - **Explanation:** Dynamically generates the column names for the population (`pop_column`), intervention data (`penta_column`), and coverage (`coverage_column`) for the current year.
### Check for Required Columns
   - **Code:**
     ```python
     if pop_column in gdf.columns and penta_column in gdf.columns:
     ```
   - **Explanation:** Checks if both the population and intervention data columns for the current year exist in the GeoDataFrame (`gdf`). If they are present, coverage calculation proceeds; otherwise, the year is skipped.
### 8. Calculate Coverage
   - **Code:**
     ```python
     gdf[coverage_column] = (gdf[penta_column] / (gdf[pop_column] * 0.027)) * 100
     ```
   - **Explanation:** Calculates the coverage for the current year using the formula:
     - `(gdf[penta_column] / (gdf[pop_column] * 0.027)) * 100`
     - The constant `0.027` represents the proportion of infants in the population. This assumes that 2.7% of the total population consists of infants.
#### Log Calculation Status
   - **Code:**
     ```python
     print(f"Calculated {coverage_column} for year {year}")
     ```
   - **Explanation:** Prints a confirmation message indicating that the coverage calculation for the current year was completed successfully.

---

#### Handle Missing Columns**
   - **Code:**
     ```python
     else:
         print(f"Skipping {year}: Missing {pop_column} or {penta_column}")
     ```
   - **Explanation:** Logs a message if the required columns for the current year are missing, and skips the calculation for that year.

#### Return GeoDataFrame
   - **Code:** `return gdf`
   - **Explanation:** Returns the final GeoDataFrame (`gdf`) containing the merged data and calculated coverage columns.
---
### Step 5: Calculates coverage for each year
```python
def categorize_coverage(gdf, years):
    """Categorize coverage into predefined intervals."""
    def coverage_category(value):
        if pd.isnull(value):
            return None
        elif value < 20:
            return '<20%'
        elif value <= 40:
            return '20-40%'
        elif value <= 60:
            return '41-60%'
        elif value <= 80:
            return '61-80%'
        else:
            return '81-100%'

    for year in years:
        coverage_column, category_column = f'coverage_{year}', f'category_{year}'
        if coverage_column in gdf.columns:
            gdf[category_column] = gdf[coverage_column].apply(coverage_category)
    return gdf
```
The `categorize_coverage` function is designed to categorize yearly coverage data into predefined intervals for easier analysis and visualization. Below is a detailed step-by-step explanation of the function:

#### Function Definition
   - **Code:** `def categorize_coverage(gdf, years):`
   - **Explanation:** The function `categorize_coverage` takes two arguments:
     - `gdf`: A GeoDataFrame containing the coverage data for each year.
     - `years`: A list or array of years for which coverage categorization is to be applied.

#### Docstring
   - **Code:** `'''Categorize coverage into predefined intervals.'''`
   - **Explanation:** Provides a brief description of the function's purpose, specifying that it assigns coverage values to predefined categories.

#### Define Coverage Category Function
   - **Code:**
     ```python
     def coverage_category(value):
         if pd.isnull(value):
             return None
         elif value < 20:
             return '<20%'
         elif value <= 40:
             return '20-40%'
         elif value <= 60:
             return '41-60%'
         elif value <= 80:
             return '61-80%'
         else:
             return '81-100%'
     ```
   - **Explanation:** Defines an inner function, `coverage_category`, to classify coverage values into predefined intervals:
     - `<20%`: Coverage less than 20%.
     - `20-40%`: Coverage between 20% and 40% (inclusive).
     - `41-60%`: Coverage between 41% and 60% (inclusive).
     - `61-80%`: Coverage between 61% and 80% (inclusive).
     - `81-100%`: Coverage greater than 80% but less than or equal to 100%.
     - Returns `None` for null or missing values.
#### Loop Through Each Year
   - **Code:** `for year in years:`
   - **Explanation:** Iterates over each year in the `years` list or array to categorize coverage data for that year.
#### Define Column Names for the Year
   - **Code:**
     ```python
     coverage_column, category_column = f'coverage_{year}', f'category_{year}'
     ```
   - **Explanation:** Dynamically generates column names:
     - `coverage_column`: Refers to the column containing coverage data for the current year (e.g., `coverage_2024`).
     - `category_column`: Refers to the new column that will store the coverage category for the current year (e.g., `category_2024`).

#### Check for Coverage Column
   - **Code:** `if coverage_column in gdf.columns:`
   - **Explanation:** Checks if the `coverage_column` for the current year exists in the GeoDataFrame (`gdf`). If it exists, the categorization process proceeds.
#### Apply Coverage Category Function
   - **Code:** `gdf[category_column] = gdf[coverage_column].apply(coverage_category)`
   - **Explanation:** Applies the `coverage_category` function to the `coverage_column` for the current year and stores the resulting categories in the `category_column`.

#### Return Updated GeoDataFrame
   - **Code:** `return gdf`
   - **Explanation:** Returns the updated GeoDataFrame (`gdf`) containing the new category columns for each year.
### Step 6: Plot coverage maps
```python
def plot_coverage_maps(gdf, years, output_path):
    """Plot coverage maps for each year and save as an image."""
    rows = len(years) // 2 + len(years) % 2
    fig, axes = plt.subplots(nrows=rows, ncols=3, figsize=(15, 5 * rows))
    axes = axes.flatten()

    cmap = plt.cm.RdYlGn
    norm = Normalize(vmin=0, vmax=100)
    coverage_categories = ['<20%', '20-40%', '41-60%', '61-80%', '81-100%']
    category_bounds = [0, 20, 40, 60, 80, 100]

    # Create legend elements
    legend_elements = [
        Line2D([0], [0], color=cmap(norm((category_bounds[i] + category_bounds[i + 1]) / 2)), lw=4, label=coverage_categories[i])
        for i in range(len(coverage_categories))
    ]

    for i, year in enumerate(years):
        if i >= len(axes): break
        category_column = f'category_{year}'
        ax = axes[i]
        if category_column in gdf.columns:
            gdf.plot(column=category_column, cmap='RdYlGn', legend=False, ax=ax, edgecolor='black')
            ax.set_title(f'{year}')
            ax.axis('off')

    # Remove unused axes
    for ax in axes[len(years):]:
        fig.delaxes(ax)

    # Add legend
    fig.legend(handles=legend_elements, title='LLINs given during penta3 coverage (%)', loc='upper center', bbox_to_anchor=(0.5, 1.02), ncol=len(coverage_categories))
    plt.tight_layout()
    plt.savefig(output_path, bbox_inches='tight', dpi=300)
    plt.show()
```
The `plot_coverage_maps` function generates visual maps of LLIN coverage for specified years and saves the output as an image. Below is a detailed explanation of how the function works:

#### Function Definition
   - **Code:** `def plot_coverage_maps(gdf, years, output_path):`
   - **Explanation:** The function `plot_coverage_maps` takes three arguments:
     - `gdf`: A GeoDataFrame containing coverage data and geometry for plotting maps.
     - `years`: A list or array of years for which coverage maps should be created.
     - `output_path`: The file path where the generated image will be saved.
#### Docstring
   - **Code:** `'''Plot coverage maps for each year and save as an image.'''`
   - **Explanation:** Provides a brief description of the function's purpose.
#### Calculate Number of Rows for Subplots
   - **Code:** `rows = len(years) // 2 + len(years) % 2`
   - **Explanation:** Determines the number of rows needed for the subplot grid:
     - Ensures sufficient rows to display maps for all specified years, accommodating up to two maps per row.

#### Create Subplots
   - **Code:**
     ```python
     fig, axes = plt.subplots(nrows=rows, ncols=3, figsize=(15, 5 * rows))
     axes = axes.flatten()
     ```
   - **Explanation:**
     - Creates a grid of subplots with dimensions `rows x 3`.
     - Sets the figure size based on the number of rows to ensure readable and visually balanced plots.
     - Flattens the axes array to simplify indexing.
#### Define Color Map and Normalization
   - **Code:**
     ```python
     cmap = plt.cm.RdYlGn
     norm = Normalize(vmin=0, vmax=100)
     ```
   - **Explanation:**
     - Uses the `RdYlGn` colormap, which ranges from red (low values) to green (high values).
     - Normalizes values between 0 and 100 for consistent scaling across maps.
#### Define Coverage Categories and Legend
   - **Code:**
     ```python
     coverage_categories = ['<20%', '20-40%', '41-60%', '61-80%', '81-100%']
     category_bounds = [0, 20, 40, 60, 80, 100]
     legend_elements = [
         Line2D([0], [0], color=cmap(norm((category_bounds[i] + category_bounds[i + 1]) / 2)), lw=4, label=coverage_categories[i])
         for i in range(len(coverage_categories))
     ]
     ```
   - **Explanation:**
     - Defines the coverage intervals (`coverage_categories`) and their corresponding boundaries (`category_bounds`).
     - Creates `legend_elements` to display the colormap for each category in the final output.
#### Loop Through Each Year
   - **Code:**
     ```python
     for i, year in enumerate(years):
         if i >= len(axes): break
         category_column = f'category_{year}'
         ax = axes[i]
         if category_column in gdf.columns:
             gdf.plot(column=category_column, cmap='RdYlGn', legend=False, ax=ax, edgecolor='black')
             ax.set_title(f'{year}')
             ax.axis('off')
     ```
   - **Explanation:**
     - Iterates through each year in `years` to generate a map for that year.
     - Dynamically identifies the `category_column` for the current year (e.g., `category_2024`).
     - If the column exists in `gdf`, plots the map for that year on the respective subplot (`ax`):
       - Colors regions based on coverage categories using the `RdYlGn` colormap.
       - Removes axis labels and adds a title showing the year.

#### Remove Unused Axes
   - **Code:**
     ```python
     for ax in axes[len(years):]:
         fig.delaxes(ax)
     ```
   - **Explanation:** Deletes any extra subplot axes created in the grid if they are not needed (e.g., for years that don’t exist).
#### Add Legend
   - **Code:**
     ```python
     fig.legend(handles=legend_elements, title='LLINs given during penta3 coverage (%)', loc='upper center', bbox_to_anchor=(0.5, 1.02), ncol=len(coverage_categories))
     ```
   - **Explanation:**
     - Adds a legend to the figure, displaying the coverage categories and their corresponding colors.
     - Positions the legend at the top center of the figure.
#### Save and Display the Plot
   - **Code:**
     ```python
     plt.tight_layout()
     plt.savefig(output_path, bbox_inches='tight', dpi=300)
     plt.show()
     ```
   - **Explanation:**
     - Adjusts the layout to minimize overlap between elements.
     - Saves the figure as an image to the specified `output_path` with high resolution (300 DPI).
     - Displays the generated plots.
### Step 7: Run the main function to generate the plot and outpu the excel file
```python
def main():
    intervention_path = '/content/intervention_data_new (1).xlsx'
    population_path = '/content/pop_chie_data.xlsx'
    shapefile_path = '/content/Chiefdom 2021.shp'
    output_excel_path = '/content/summed_llins_penta3_data.xlsx'
    output_image_path = 'LLINs_given_during_penta3_coverage.png'

    # Load data
    intervention_data, population_data, shapefile = load_data(intervention_path, population_path, shapefile_path)

    # Summarize intervention data
    summarized_data, years = summarize_intervention(intervention_data)
    summarized_data.to_excel(output_excel_path, index=False)
    print("Summed data saved to Excel.")

    # Merge data and calculate coverage
    gdf = merge_and_calculate_coverage(population_data, shapefile, summarized_data, years)

    # Categorize coverage
    gdf = categorize_coverage(gdf, years)

    # Plot coverage maps
    plot_coverage_maps(gdf, years, output_image_path)

# Run the main function
if __name__ == "__main__":
    main()
```
The `main` function orchestrates the complete workflow for analyzing and visualizing LLINs (Long-Lasting Insecticidal Nets) given during penta3 vaccinations. Below is a detailed explanation of each step:

#### Function Definition
   - **Code:** `def main():`
   - **Explanation:** Defines the `main` function, which serves as the entry point for the script. It coordinates all data loading, processing, and visualization tasks.
#### File Paths
   - **Code:**
     ```python
     intervention_path = '/content/intervention_data_new (1).xlsx'
     population_path = '/content/pop_chie_data.xlsx'
     shapefile_path = '/content/Chiefdom 2021.shp'
     output_excel_path = '/content/summed_llins_penta3_data.xlsx'
     output_image_path = 'LLINs_given_during_penta3_coverage.png'
     ```
   - **Explanation:**
     - Defines file paths for:
       - `intervention_path`: Path to the Excel file containing intervention data.
       - `population_path`: Path to the Excel file containing population data.
       - `shapefile_path`: Path to the shapefile containing geographic data.
       - `output_excel_path`: Path for saving the summarized intervention data as an Excel file.
       - `output_image_path`: Path for saving the coverage maps as an image.
#### Load Data
   - **Code:**
     ```python
     intervention_data, population_data, shapefile = load_data(intervention_path, population_path, shapefile_path)
     ```
   - **Explanation:**
     - Calls the `load_data` function to load and preprocess:
       - `intervention_data` from the intervention file.
       - `population_data` from the population file.
       - `shapefile` from the shapefile.
#### Summarize Intervention Data
   - **Code:**
     ```python
     summarized_data, years = summarize_intervention(intervention_data)
     summarized_data.to_excel(output_excel_path, index=False)
     print("Summed data saved to Excel.")
     ```
   - **Explanation:**
     - Calls `summarize_intervention` to group and summarize the intervention data by `adm3` for each year.
     - Saves the summarized data to an Excel file at `output_excel_path`.
     - Prints a confirmation message once the file is saved.
#### Merge Data and Calculate Coverage
   - **Code:**
     ```python
     gdf = merge_and_calculate_coverage(population_data, shapefile, summarized_data, years)
     ```
   - **Explanation:**
     - Calls `merge_and_calculate_coverage` to:
       - Merge the population, shapefile, and summarized intervention data.
       - Calculate LLIN coverage for each year.
#### Categorize Coverage**
   - **Code:**
     ```python
     gdf = categorize_coverage(gdf, years)
     ```
   - **Explanation:**
     - Calls `categorize_coverage` to classify coverage values into predefined intervals (e.g., `<20%`, `20-40%`, etc.) for each year.
     - Updates the GeoDataFrame (`gdf`) with new category columns.
#### Plot Coverage Maps
   - **Code:**
     ```python
     plot_coverage_maps(gdf, years, output_image_path)
     ```
   - **Explanation:**
     - Calls `plot_coverage_maps` to generate visual maps of LLIN coverage for each year.
     - Saves the resulting maps as an image to the specified `output_image_path`.
#### Run the Main Function
   - **Code:**
     ```python
     if __name__ == "__main__":
         main()
     ```
   - **Explanation:**
     - Checks if the script is being run as the main program (not imported as a module).
     - Executes the `main` function to begin the workflow.
---
