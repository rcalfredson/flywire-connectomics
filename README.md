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

- **Classification / Hierarchical Annotations (1 MB)**  
  File: `classification.csv.gz`  
  Provides hierarchical anatomical annotations for every neuron, including its **super_class**, **class**, **sub_class**, **hemilineage**, **side**, and **nerve**.  
  This file enables grouping neurons by broad anatomical or functional categories such as *SMP*, *LAL*, *ascending*, *descending*, and *central*.  

  | Column | Description | Example values |
  |---------|--------------|----------------|
  | `root_id` | Unique neuron ID | 720575940632369439 |
  | `flow` | One of three possible values describing directionality | ascending / descending / central |
  | `super_class` | Highest-level anatomical grouping | e.g., sensory, central, descending |
  | `class` | Intermediate grouping | e.g., LAL, SMP, FB |
  | `sub_class` | Finer-grained subdivision | 96 unique values |
  | `hemilineage` | Developmental lineage identifier | 199 unique values |
  | `side` | Left / Right / Midline designation | left / right / midline |
  | `nerve` | Nerve association (if applicable) | e.g., antennal, abdominal |

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

- [**reciprocity_analysis.ipynb**](reciprocity_analysis.ipynb)  
  Examines **reciprocal connectivity** between a target neuron and its partner cell types, quantifying how balanced their bidirectional synaptic weights are.  
  - Loads the FlyWire connectivity table (`connections_princeton_no_threshold.csv.gz`) and cell-type annotation file (`consolidated_cell_types.csv.gz`).  
  - Aggregates inbound (partner → target) and outbound (target → partner) synaptic weights for each partner neuron.  
  - Merges these into a unified table showing both directions of connectivity (`in_w`, `out_w`) and associated cell-type annotations.  
  - Visualizes reciprocity as a **scatter plot** of `in_w` vs. `out_w` with a shaded **wedge** around the diagonal (default: ±20°), highlighting pairs with approximately balanced bidirectional strength.  
  - Annotates the most reciprocal partners by geometric mean synaptic weight, color-coding each labeled cell type and including a legend for clarity.  

  This analysis helps identify whether the target neuron (e.g., *oviIN Right*) engages in **mutual, directional, or unidirectional** synaptic relationships with its partners, providing insight into potential feedback loops or bidirectional motifs in the circuit.

- [**input_composition_pal02_pal04.ipynb**](input_composition_pal02_pal04.ipynb)  
  Compares the **input composition** of two closely related neuron types, *PAL02* and *PAL04*, across both hemispheres.  
  - Uses all three FlyWire datasets (`connections_princeton_no_threshold.csv.gz`, `consolidated_cell_types.csv.gz`, and `classification.csv.gz`).  
  - Aggregates total input synaptic weights (`in_w`) for each **presynaptic cell type** and **super_class**.  
  - Computes each cell type’s **fraction of total input** by summing synaptic weights across all upstream neurons of that type and dividing by the target’s total input.  
  - Merges left and right hemispheric data and averages input fractions to produce hemisphere-independent summaries.  
  - Generates bar plots of input composition by both `primary_type` and `super_class`, exporting figures to the `figures/` folder.  

  This notebook provides a clear comparison of input sources to *PAL02* and *PAL04*, highlighting differences in both **quantitative strength** and **qualitative composition** of their upstream partners.  
  Future extensions may test for enrichment of specific **neuropil domains** or **modality biases** between the two neuron types.

- [**modality_composition_pal02_pal04.ipynb**](modality_composition_pal02_pal04.ipynb)  
  Compares the **sensory modality composition** of inputs to *PAL02* and *PAL04* neurons using a hop-limited propagation model.  
  - Uses all three FlyWire datasets (`connections_princeton_no_threshold.csv.gz`, `consolidated_cell_types.csv.gz`, and `classification.csv.gz`).  
  - Infers each sensory neuron’s **modality** (e.g., visual, olfactory, gustatory, mechanosensory) from its `class`, `super_class`, and `nerve` annotations, grouping non-sensory and ambiguous types as *other*.  
  - Propagates modality signals from sensory seeds to each target neuron (1–2 synaptic hops) with a tunable attenuation factor (α = 0.5 by default).  
  - Aggregates normalized modality fractions for *PAL02* and *PAL04* (mean of left and right hemisphere homologs).  
  - Produces two complementary visualizations:  
    - **Stacked bar plot** showing the proportional sensory drive to each target, with the legend dynamically restricted to modalities contributing ≥ 0.5%.  
    - **Δ-fraction (modality shift) plot** comparing *PAL02* − *PAL04* differences, filtered to the same set of visible modalities for consistent interpretation.

  This notebook highlights differences in **sensory bias** between *PAL02* and *PAL04* inputs, clarifying which sensory modalities preferentially influence each neuron type.  
  Future extensions may explore separate propagation weights for **first- vs second-order** sensory relays or incorporate **direction-specific normalization** to refine cross-modality comparisons.

---

## Folders

- **data/**  
  Local folder to store downloaded FlyWire datasets. This folder is ignored by git (except for `.gitkeep`).  

- **figures/**  
  Holds plots and visualizations produced by the notebooks.

