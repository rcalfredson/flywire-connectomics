# flywire-connectomics
Tools and notebooks for analyzing the FlyWire connectome, beginning with cell-type partner diversity distributions and expanding to circuit-level analyses.

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
