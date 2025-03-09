# AutoDock DLG Analyzer

This Python script is designed to analyze AutoDock DLG files, extract binding energies, and visualize the results. It's particularly useful for researchers working with molecular docking simulations using AutoDock.

## Features

- **Uploads:** Accepts AutoDock DLG files directly from your local machine (or Google Colab environment).
- **Extraction:** Extracts binding energy values using robust regular expressions.
- **Data Handling:** Stores extracted data in a Pandas DataFrame for easy manipulation.
- **Visualization:** Generates a plot of "Pose vs Binding Energy" using Matplotlib and Seaborn for insightful analysis.
- **Saving:** Saves extracted binding energies in a CSV file for future use.


## Requirements

- **Python 3.x**
- **Pandas**
- **Matplotlib**
- **Seaborn**
- **re (Regular Expressions)**
- **google.colab (optional, for Google Colab environment)**


## How to Use

1. **Installation:**
    - Make sure you have Python 3.x installed.
    - Install the necessary libraries using pip:


  bash pip install pandas matplotlib seaborn

  2. **Execution:**
   - Run the script in a Jupyter Notebook or Google Colab environment.
   - When prompted, upload your AutoDock DLG file.
3. **Output:**
   - The script will display a table of extracted binding energies.
   - A plot of "Pose vs Binding Energy" will be generated.
   - A CSV file named "binding_energies.csv" will be saved containing the extracted data.


## How to Use

1. **Run the script** in a Jupyter Notebook or Google Colab environment.
2. **Upload your AutoDock DLG file** when prompted.
3. The script will:
   - Display a table of extracted binding energies.
   - Generate a plot of "Pose vs Binding Energy".
   - Save the extracted data to a CSV file named "binding_energies.csv".

## Note

- This script is specifically designed for AutoDock DLG files and may not be compatible with other docking software outputs.
- Ensure your DLG file is in the correct format for accurate extraction.
- Feel free to modify the script for your specific needs or to add more advanced analysis features.

## License

This script is released under the [MIT License](LICENSE).
