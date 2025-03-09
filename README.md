# AutoDock DLG Histogram Generator

This Python script analyzes AutoDock Vina `.dlg` result files, extracts binding energies, and generates a histogram to visualize their distribution. It is designed to be used in Google Colaboratory (Colab).

## Features

* **File Upload:** Uses `google.colab.files.upload()` to allow users to upload their `.dlg` files directly in Colab.
* **Data Extraction:** Parses the `.dlg` file to extract binding energies for each docked pose.
* **Histogram Visualization:** Generates a histogram of binding energy distribution using `matplotlib`.
* **Export to Excel:** Allows users to save the results table (pose, binding energy, RMSD) as an Excel file (`.xlsx`).
* **Save Plot as Image:** Allows users to save the histogram plot as a PNG image.
* **Robust Error Handling:** Includes `try...except` blocks to handle potential errors during data extraction.

## Usage

1.  **Open in Google Colab:** Open a new or existing Colab notebook.
2.  **Copy and Paste:** Copy the provided Python script into a code cell in Colab.
3.  **Run the Cell:** Execute the code cell. You will be prompted to upload your `.dlg` file.
4.  **Upload File:** Select and upload your AutoDock Vina `.dlg` result file.
5.  **View Results:** The script will display the results table and the binding energy histogram in the output.
6.  **Download Files (Optional):** If `save_excel=True` and `save_plot=True` are set when calling the function, the Excel file and PNG plot will be available for download.

## Code

```python
import pandas as pd
import matplotlib.pyplot as plt
from google.colab import files
import io

def analyze_dlg_with_visualization(save_excel=False, save_plot=False):
    """آنالیز فایل DLG، نمایش جدول و نمودار، و ذخیره اختیاری."""

    # آپلود فایل
    uploaded = files.upload()
    dlg_file_name = next(iter(uploaded))
    dlg_content = uploaded[dlg_file_name].decode('utf-8').splitlines()

    # استخراج نتایج داکینگ
    docking_data = []
    capture_results = False

    for line in dlg_content:
        if "DOCKED: MODEL" in line:
            capture_results = True
        elif "DOCKED: ENDMDL" in line:
            capture_results = False
        if capture_results:
            docking_data.append(line.strip())

    # پردازش امتیازات داکینگ
    binding_energies = []
    rmsd_values = []
    poses = []  # Store pose numbers explicitly

    for line in docking_data:
        if "DOCKED: MODEL" in line:
            poses.append(int(line.split()[-1]))  # Extract pose number
        if "DOCKED: USER    Estimated Free Energy of Binding" in line:
            try:
                energy = float(line.split()[-2])
                binding_energies.append(energy)
            except (ValueError, IndexError):
                print(f"Warning: Could not extract binding energy from line: {line}")
        if "DOCKED: USER    RMSD from best mode" in line:  # Use the actual text
            try:
                rmsd = float(line.split()[-1])
                rmsd_values.append(rmsd)
            except (ValueError, IndexError):
                print(f"Warning: Could not extract RMSD value from line: {line}")

    # Ensure all lists have the same length
    min_len = min(len(poses), len(binding_energies), len(rmsd_values))
    poses = poses[:min_len]
    binding_energies = binding_energies[:min_len]
    rmsd_values = rmsd_values[:min_len]

    # Create DataFrame
    df_results = pd.DataFrame({
        "Pose": poses,  # Use extracted pose numbers
        "Binding Energy (kcal/mol)": binding_energies,
        "RMSD (Å)": rmsd_values
    })

    # Display table
    print("\nDocking Results Table:\n")
    print(df_results)

    # Create histogram plot
    plt.figure(figsize=(10, 6))
    plt.hist(binding_energies, bins=50, color='blue', edgecolor='black')  # Adjust bins as needed
    plt.title("Histogram of Binding Energies")
    plt.xlabel("Binding Energy (kcal/mol)")
    plt.ylabel("# Conformations")
    plt.grid(True)
    plt.show()

    # Save to Excel (optional)
    if save_excel:
        excel_file_name = "docking_results.xlsx"
        df_results.to_excel(excel_file_name, index=False)
        files.download(excel_file_name)

    # Save plot as image (optional)
    if save_plot:
        plot_file_name = "binding_energy_histogram.png"
        plt.savefig(plot_file_name)
        files.download(plot_file_name)

# Run the function with optional saving
analyze_dlg_with_visualization(save_excel=True, save_plot=True)
