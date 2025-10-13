# flywire-connectomics
Tools and notebooks for analyzing the FlyWire connectome, beginning with cell-type partner diversity distributions and extending to analyses of input primacy and circuit-level reciprocity.

## Setup

This repository is designed to work with the public FlyWire connectome dataset.  
You’ll need to download two files from the FlyWire **Downloads** page:  

- **Cell Types (888 KB)**  
  File: `consolidated_cell_types.csv.gz`  
  Contains the mapping of neuron IDs (`root_id`) to their annotated primary (`primary_type`) and additional (`additional_type(s)`) cell types.  

- **Connections (Unfiltered) (277 MB)**  
  File: `connections_princeton_no_threshold.csv.gz`  
  Raw, no-threshold synaptic connectivity table. One row per connected pair of neurons, including `pre_root_id`, `post_root_id`, neuropil, synapse count, and predicted NT type.  

> ⚠️ You will need a FlyWire account to access the downloads. Sign up or log in at [codex.flywire.ai](https://codex.flywire.ai).

### Folder structure

After cloning this repository, you should place both downloaded files into the `data/` folder:

```text
flywire-connectomics/
├── data/
│   ├── consolidated_cell_types.csv.gz
│   ├── connections_princeton_no_threshold.csv.gz
│   └── .gitkeep
├── LICENSE
└── README.md
```

The `.gitkeep` file ensures that the `data/` directory is tracked by git even if empty.

### Running the analysis

Once the required files are in place, you can open the provided Jupyter notebook(s) and run the analysis. The first available notebook will reproduce the **cell-type partner diversity distribution** for all neurons in the connectome.

---

## Notebooks

This section serves as an index of analyses implemented in Jupyter notebooks. Over time, more notebooks will be added here as the project expands.

- [**partnering_cell_types.ipynb**](partnering_cell_types.ipynb)  
  Constructs the distribution of the number of unique partner cell types per neuron in the FlyWire connectome.  
  - Loads raw FlyWire data (`connections_princeton_no_threshold.csv.gz` and `consolidated_cell_types.csv.gz`).  
  - Maps each neuron to the set of unique partner cell types it connects with.  
  - Produces the global distribution, comparable to Hemibrain results, and highlights neurons of interest (e.g., oviIN, oviEN, PAL02, PAL04, Earmuff, Tophat).

- [**input_primacy.ipynb**](input_primacy.ipynb)  
  Quantifies **input primacy** for a specified neuron type — that is, how strongly each of its presynaptic cell types targets it relative to that type’s other outputs.  
  - Loads the raw FlyWire connectivity table (`connections_princeton_no_threshold.csv.gz`) and cell-type annotation file (`consolidated_cell_types.csv.gz`).  
  - Applies a minimum synapse threshold (default: 3) to remove extremely weak or potentially spurious connections.  
  - For each input cell type connecting to the chosen target neuron:
    1. Collapses all synaptic inputs to the target by presynaptic **cell type**.  
    2. Fetches the full set of **outputs** of that presynaptic type across the connectome.  
    3. Determines where the target neuron’s **cell type** ranks among those outputs.  
  - Produces a ranked table summarizing each input type’s weight and rank, with “strong” vs “weak” designation (default cutoff: ≥ 100 synapses).  
  - Generates a **stacked bar plot** visualizing the distribution of “strong” and “weak” input types across the first few primacy ranks (default: top 10).  

  This analysis highlights whether a target neuron (e.g., *SMP544*) is a **dominant output** for many of its presynaptic partners or whether its inputs are more **distributed** across different targets.

---

## Folders

- **data/**  
  Local folder to store downloaded FlyWire datasets. This folder is ignored by git (except for `.gitkeep`).  

- **figures/**
  Holds plots and visualizations produced by the notebooks.

