# AutoDock DLG Analysis with Visualization

This Python script analyzes AutoDock Vina `.dlg` result files, extracting binding energies and RMSD values, and generates a table and a plot for visualization. It is designed to be used in Google Colaboratory (Colab).

## Features

* **File Upload:** Uses `google.colab.files.upload()` to allow users to upload their `.dlg` files directly in Colab.
* **Data Extraction:** Parses the `.dlg` file to extract binding energies and RMSD values for each docked pose.
* **Table Generation:** Creates a pandas DataFrame to display the results in a table format.
* **Plot Visualization:** Generates a scatter plot of binding energy vs. pose number using `matplotlib`.
* **Export to Excel:** Allows users to save the results table as an Excel file (`.xlsx`).
* **Save Plot as Image:** Allows users to save the plot as a PNG image.

## Usage

1.  **Open in Google Colab:** Open a new or existing Colab notebook.
2.  **Copy and Paste:** Copy the provided Python script into a code cell in Colab.
3.  **Run the Cell:** Execute the code cell. You will be prompted to upload your `.dlg` file.
4.  **Upload File:** Select and upload your AutoDock Vina `.dlg` result file.
5.  **View Results:** The script will display the results table and the binding energy plot in the output.
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
    for line in docking_data:
        if "DOCKED: USER    Estimated Free Energy of Binding" in line:
            energy = float(line.split()[-2])
            binding_energies.append(energy)
        if "DOCKED: USER    RMSD from best mode" in line:
            rmsd = float(line.split()[-1])
            rmsd_values.append(rmsd)

    # ایجاد DataFrame
    df_results = pd.DataFrame({
        "Pose": list(range(1, len(binding_energies) + 1)),
        "Binding Energy (kcal/mol)": binding_energies,
        "RMSD (Å)": rmsd_values
    })

    # نمایش جدول
    print("\nDocking Results Table:\n")
    print(df_results)

    # ایجاد نمودار پراکندگی
    plt.figure(figsize=(10, 6))
    plt.scatter(df_results["Pose"], df_results["Binding Energy (kcal/mol)"])
    plt.title("Binding Energy vs. Pose")
    plt.xlabel("Pose")
    plt.ylabel("Binding Energy (kcal/mol)")
    plt.grid(True)
    plt.show()

    # ذخیره در اکسل (اختیاری)
    if save_excel:
        excel_file_name = "docking_results.xlsx"
        df_results.to_excel(excel_file_name, index=False)
        files.download(excel_file_name)

    # ذخیره نمودار به عنوان تصویر (اختیاری)
    if save_plot:
        plot_file_name = "binding_energy_plot.png"
        plt.savefig(plot_file_name)
        files.download(plot_file_name)

# اجرای تابع با ذخیره اختیاری
analyze_dlg_with_visualization(save_excel=True, save_plot=True)
