# Galectin / LAMP1 / LipidTOX / Hoechst Analysis

Notebook-driven image analysis for M17D 4-channel microscopy data.

**Notebook:** [M17D_4channel_analysis.ipynb](M17D_4channel_analysis.ipynb)

## What This Does

This workflow segments whole cells and nuclei from multi-channel .czi images, detects inclusion signals, measures per-cell features, and writes results to CSV.

The four channels are:

- Red: LipidTOX droplets
- Orange: LAMP1-RFP lysosomes
- Green: galectin-GFP
- Blue: Hoechst nuclei

## Quickstart

This repository only includes the notebook. You need to create the local input and output folders before running it.

1. Clone or download the repo, then open a terminal in the galectin folder.
2. Create the folders the notebook expects:

   ```bash
   mkdir -p images Results Results/masks
   ```

3. Create one subfolder per dataset name from `IMAGE_FOLDER_NAMES` if you want to keep files organized:

   ```bash
   mkdir -p images/M17D_52926_siRNA_galectinGFP_LAMP1RFP_LipidTOXfr_hoechst
   mkdir -p images/M17D_60426_siRNA_galectinGFP_LAMP1RFP_LipidTOXfr_hoechst
   mkdir -p images/M17D_60526_siRNA_OA_Fed_galectinGFP_LAMP1RFP_LipidTOXfr_hoechst
   mkdir -p images/M17D_60926_siRNA_OA_Fed_galectinGFP_LAMP1RFP_LipidTOXfr_Hoechst
   mkdir -p images/M17D_61126_siRNA_galectinGFP_LAMP1RFP_LipidTOXfr_hoechst
   mkdir -p images/M17D_61826_siRNA_galectinGFP_LAMP1RFP_LipidTOXfr_hoechst
   mkdir -p images/M17D_61826_siRNA_OA_Fed_galectinGFP_LAMP1RFP_LipidTOXfr_Hoechst
   ```

4. Move your .czi files into those folders, or keep them directly under `images/` if you prefer a simpler setup.

5. Install the notebook dependencies if needed:

   ```bash
   pip install cellpose numpy pandas matplotlib scikit-image czifile scipy
   ```

6. Open [M17D_4channel_analysis.ipynb](M17D_4channel_analysis.ipynb) and run the notebook top to bottom.
7. Check `Results/` for the CSV output and `Results/masks/` for saved masks.

## Requirements

- Python 3.10 or newer
- `cellpose`
- `numpy`
- `pandas`
- `matplotlib`
- `scikit-image`
- `czifile`
- `scipy`

GPU support is optional. If a CUDA-capable GPU is available, keep `USE_GPU = True` in the notebook.

## Inputs

- Multi-channel `.czi` files
- Folder names listed in `IMAGE_FOLDER_NAMES`

The notebook looks for each folder name first in the analysis directory and then in `images/`. If you are starting from scratch, create those folders yourself before running the notebook.

## Outputs

- Per-image masks in `Results/masks/<folder>/<image>/`
- Per-folder CSV files in `Results/`
- Inline QC figures for each image, when `SHOW_PLOTS = True`

## Workflow

1. Load and normalize the four channels.
2. Segment whole cells and nuclei.
3. Recover missed cells from nuclei when needed.
4. Detect green puncta, red lipid droplets, and orange lysosomes.
5. Remove likely orange bleed-through from red.
6. Measure per-cell features for green-positive cells.
7. Save masks and write a CSV for each folder.

## Main Parameters

Tune these near the top of the notebook if your images need different settings:

- Cell segmentation: `CYTO_DIAMETER`, `CYTO_FLOW_THRESH`, `CYTO_PROB_THRESH`, `CELL_MIN_AREA`
- Nucleus segmentation: `NUC_DIAMETER`, `NUC_FLOW_THRESH`, `NUC_PROB_THRESH`, `NUC_MIN_AREA`
- Green-positive calling: `GREEN_FILL_THRESH`, `GREEN_FILL_FRAC`
- Inclusion detection: `GREEN_TOPHAT_RADIUS`, `RED_TOPHAT_RADIUS`, `ORANGE_TOPHAT_RADIUS`

## Tips

- If Cellpose is slow or unstable on GPU, set `USE_GPU = False`.
- If cells are being split, increase `CYTO_DIAMETER` a bit.
- If nuclei are missed, lower `NUC_PROB_THRESH` or `NUC_INTENSITY_FLOOR`.
- If inclusion masks are too noisy, raise the threshold or minimum size for the relevant channel.

## Troubleshooting

- If images are not found, check the folder names in `IMAGE_FOLDER_NAMES`.
- If .czi loading fails, confirm that `czifile` is installed and the files are not corrupted.
- If the output CSV is empty, check whether green-positive cells are being detected.

## Notes

This notebook is designed for the Kara Neurodegenerative Disease Lab workflow and saves results in the local `Results/` tree.
