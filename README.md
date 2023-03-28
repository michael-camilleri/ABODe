# The Activity Labelling Module (ALM)
This repository contains the Annotated Behaviour Dataset used to train and evaluate the Activity Labelling Module in Chapter 5 of my PhD Thesis “Automated Identification and Behaviour Classification for Modelling Social Dynamics in Group-Housed Mice” [1].

## 1. Repository Structure

***Note***: Due to size limitations, the per-snippet identities, positions and video in **Snippets** is stored as a zip-file on our University of Edinburgh [DataShare Project Page (Todo)](todo).
```
├── dataset                         # Curated Version of the DataSet
│    ├── Datasplits.df
│    ├── Annotations.df
│    └── Snippets                   # Per-Snippet Data (available separately through DataShare)
│         ├── A_17_12
│         │   ├── Video.mp4
│         │   ├── Identities.df
│         │   └── Positions.df
│         :
│         └── P_139_20
│
├── LICENSE
└── README.md
```

-------------

## 2. Dataset
 * This dataset contains behaviour labels and associated features for groups of three mice in the homecage: please refer to [1, §3.5] for details of the data collection process.
 * Recording periods, which we refer to as **Snippets** are two-minutes in length.
 * The basic unit of processing is the **Behavioural Time Interval (BTI)** (1 second duration), but within this, we are interested in the behaviour of each mouse separately (and hence, a single sample is a mouse-BTI combination).
 * The data is provided for reproducibility purposes and also to allow further research on the data: for this reason, it is provided in its complete form and with minimal processing.

### 2.1 Storage Organisation
 * The dataset is stored partly within this repository (under `data/dataset`), with the snippets available through our [DataShare Project Page (Todo)](todo) as a zip-file: an empty directory is provided to indicate where it should exist relative to the root.
 * We provide two groundtruths:
    * `Datasplits.df`: Assignment of snippets to datasplits (train/validation etc...)
    * `Annotations.df`: Per-BTI annotations
 * The snippets are organised within their own directory, with the name indicating the `[Cage-ID]_[Segment-ID]_[Snippet-ID]`
    * The CageID: An alphabetical assignment of Cage Identifiers (same as in the Thesis)
    * Segment ID: The original recordings were organised into 30 minute segments - this is the numeric segment identifier
    * Snippet ID: Numeric snippet identity within segment. This encodes the minute position of the start of the snippet and hence is in multiples of 2 (i.e. 0, 2, etc...)
 * For each snippet, we provide the following features:
    * `Video.mp4`: Infra-red (grayscale) side-view view of the mice in the cage at 25 FPS
    * `Identities.df`: Per-frame per-mouse location within image, as generated using the Tracking & Identification Module ([1, §4] and [2])
    * `Positions.df`: Per-frame per-mouse antenna baseplate pickup (1 of 18 antennas)

### 2.2 Format of individual Dataframes
 * The data is stored as pandas dataframes in pickle format with `bz2` compression. They can be read into python using `pd.read_pickle('...', compression='bz2')`.

#### `Datasplits.df`
 * Indexed by *Cage-ID*, *Segment-ID* and *Snippet ID* (same as snippet directory naming)
 * For each snippet, we provide the *Original* split into Train/Validate/Test [1, §3], the new *Fixed* split into Train/Validate/Test and the Cross-Validation Folds (1 through 14, with the test-set represented by -1). Note that the test-snippets are the same throughout all splits.

#### `Annotations.df`
 * Indexed by *Cage-ID*, *Segment-ID*, *Snippet ID* and *BTI*
 * Columns are two-level:
    * The first level enumerates the different mice (R/G/B)
    * For each mouse (and BTI), we provide:
        * `GT.Admissible`: Boolean admissibility --- This is true **only** iff none of the Annotator's and the subsequent Observability labellings are Ambiguous [1, §5.3.1]
        * `GT.Source`: **A** if the labellings are from the Annotator, **M** if modified by subsequent Observability labelling
        * `GT.Observable`: Observability Labelling --- **0**: Not Observable, **1**: Ambiguous, **2**: Observable
        * `GT.Behaviour`: Resolved Behaviour Labelling according to simplified schema in [1, §5.3.1]: **N/Adm** encodes inadmissible and **N/Obs** if not observable.
        * `GT.Ann.Behaviour`: Original Behaviour by Annotator according to full label schema in [1, Table 3.6]
        * `GT.Ann.Modifier`: Additional metadata for some of the labels (see [1, Table 3.6] for explanation)

#### `Identities.df`
 * Indexed by *BTI* and *Frame* number (relative to start of snippet)
 * Columns are two-level:
    * The first level enumerates the different mice (R/G/B)
    * For each mouse (and Frame), we provide the bounding-box in CoCo format: i.e. top-left corner co-ordinate (x,y) and size (w,h)

#### `Positions.df`
 * Indexed by *BTI* and *Frame* number (relative to start of snippet)
 * Columns indicate the antenna position for each mouse (1 through 18)

### 2.3 Accessing the Per-Snippet Data
 TODO

-------------

## References
 If you find this data useful, please consider citing our work.

 For the general dataset:
 > [1] M. P. J. Camilleri, “Automated Identification and Behaviour Classification for Modelling Social Dynamics in Group-Housed Mice,” University of Edinburgh, 2023.

 For the Tracking and Identification Module:
 > [2] M. P. J. Camilleri, L. Zhang, R. S. Bains, A. Zisserman, and C. K. I. Williams, “Persistent Object Identification Leveraging Non-Visual Markers,” CoRR (arXiv), cs.CV (2112.06809), Dec. 2021.
