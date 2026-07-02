
Analysis code for Kara Neurodegenerative Disease Lab | Written for Galectin LAMP1 Lipidtox Hoescht Experiment
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

1. Open [M17D_4channel_analysis.ipynb](M17D_4channel_analysis.ipynb).
2. Put your .czi files in one of the folders listed in `IMAGE_FOLDER_NAMES` inside the notebook, or in the local `images/` subfolder.
3. Install the notebook dependencies if needed:

   ```bash
   pip install cellpose numpy pandas matplotlib scikit-image czifile scipy
   ```

4. Run the notebook top to bottom.
5. Check `Results/` for the CSV output and `Results/masks/` for saved masks.

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

The notebook looks for each folder name first in the analysis directory and then in `images/`.

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
