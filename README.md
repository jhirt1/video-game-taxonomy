# Video Game Taxonomy

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20936124.svg)](https://doi.org/10.5281/zenodo.20936124)

This repository contains notebook-based Python code for cleaning, preparing, and analyzing survey data used to develop a taxonomy of video game mood and experience terms. The workflow takes wide-format Qualtrics survey exports, restructures participant-provided mood terms into term-level records, applies manual cleaning and removal decisions, stems normalized terms, and generates descriptive figures for manuscript analysis.

The repository is designed as a research-code companion for a manuscript and Zenodo software record. It is notebook-first rather than package-first: the primary outputs are cleaned term tables and publication-oriented exploratory figures rather than an installable Python library.

## Repository status

This project is intended for archival and reproducible research use. The code and generated analysis notebooks are tracked in GitHub. Participant-level data files, intermediate CSV/XLSX files, and other local data exports should remain untracked.

The ZIP archive inspected for this README included local data files. Those files are intentionally treated here as local-only research artifacts and are not described as public repository contents.

## What the code does

The active manuscript workflow is organized around two notebooks in `Manuscript Analysis/`:

1. `data_cleaning.ipynb` prepares the survey data for analysis.
2. `data_analysis.ipynb` generates descriptive statistics and figures from the cleaned/stemmed term table.

A separate preliminary notebook, `Survey Data Analysis/survey_data_analysis.ipynb`, provides an earlier survey-completion and recruitment-oriented profile of responses. Archived notebooks are retained in `_Archive Scripts/` for provenance but are not required for the main workflow.

At a high level, the workflow:

- imports Qualtrics survey responses;
- removes Qualtrics metadata/header rows from the response table;
- filters responses by survey date and completion progress;
- extracts mood terms from three survey tracks: `Recent`, `Favorite`, and `Avoid`;
- preserves respondent metadata needed for analysis;
- cleans participant-provided terms through lowercasing, hashtag removal, whitespace stripping, and spell checking;
- captures user-assigned mood category mappings;
- preserves multiple-category mappings where respondents indicated that a term could belong to more than one category;
- applies a manually curated clean/remove workbook;
- creates a Porter stem for each cleaned term;
- flags removed terms;
- exports cleaned term tables; and
- generates descriptive plots, heatmaps, word clouds, and network visualizations.

## Project structure

```text
.
├── README.md
├── LICENSE
├── .gitignore
├── Manuscript Analysis/
│   ├── data_cleaning.ipynb
│   ├── data_analysis.ipynb
│   ├── Data/
│   │   ├── Inbound/              # local-only input data; not intended for public tracking
│   │   └── Outbound/             # generated outputs; tabular data should remain untracked
│   │       └── Graphs/           # generated analysis figures
│   └── _Archive Scripts/         # earlier exploratory notebooks retained for provenance
├── Survey Data Analysis/
│   ├── survey_data_analysis.ipynb
│   └── survey_data_analysis.html
└── filter table.html
```

## Main notebooks

### `Manuscript Analysis/data_cleaning.ipynb`

This is the primary data preparation notebook. It reads the raw survey export and manually curated term-cleaning workbook, then produces term-level analysis files.

Expected local inputs:

```text
Manuscript Analysis/Data/Inbound/final_data_export.csv
Manuscript Analysis/Data/Inbound/cleaned_terms_updated.xlsx
```

The workbook `cleaned_terms_updated.xlsx` is expected to contain two sheets:

- `Cleaned Terms`: manually reviewed mappings from participant-provided terms to cleaned terms.
- `Removed Terms`: terms that should be excluded from manuscript analysis while still being documented in the cleaning process.

The notebook performs the following major steps:

1. Loads the survey export and manual cleaning workbook.
2. Separates survey question rows and response rows from the Qualtrics export.
3. Converts `StartDate` and `EndDate` into datetime fields.
4. Filters responses to the data collection window ending on `2025-06-01`.
5. Converts `Progress` to numeric values.
6. Keeps responses with sufficient completion progress for the relevant term track.
7. Explodes wide-format survey responses into one row per participant-provided term.
8. Processes the three term tracks separately:
   - `Recent`: responses with `Progress >= 33`.
   - `Favorite`: responses with `Progress >= 57`.
   - `Avoid`: responses with `Progress >= 100`.
9. Captures primary category mappings and optional multiple-category mappings.
10. Applies spelling correction and manual term normalization.
11. Flags removed terms.
12. Applies Porter stemming to cleaned mood terms.
13. Exports term-level CSV and Excel files.

Primary generated outputs:

```text
Manuscript Analysis/Data/Outbound/recent_q_terms.csv
Manuscript Analysis/Data/Outbound/fav_q_terms.csv
Manuscript Analysis/Data/Outbound/avoid_q_terms.csv
Manuscript Analysis/Data/Outbound/all_terms.csv
Manuscript Analysis/Data/Outbound/all_terms.xlsx
Manuscript Analysis/Data/Outbound/all_terms_cleaned_stemmed.csv
Manuscript Analysis/Data/Outbound/all_terms_cleaned_stemmed.xlsx
```

### `Manuscript Analysis/data_analysis.ipynb`

This notebook generates descriptive analyses and manuscript-oriented figures from the cleaned/stemmed term table.

Expected local inputs:

```text
Manuscript Analysis/Data/Inbound/final_data_export.csv
Manuscript Analysis/Data/Outbound/all_terms_cleaned_stemmed.csv
```

The notebook filters removed terms from the analysis table and produces figures that summarize participant metadata, term frequencies, category coverage, term-category relationships, and category co-occurrence.

Generated figure outputs include:

```text
Manuscript Analysis/Data/Outbound/Graphs/Distribution of Survey Progress.png
Manuscript Analysis/Data/Outbound/Graphs/Distribution of Participant Age Groups.png
Manuscript Analysis/Data/Outbound/Graphs/Distribution of Participant Gender Groups.png
Manuscript Analysis/Data/Outbound/Graphs/Distribution of Stems by Term Group.png
Manuscript Analysis/Data/Outbound/Graphs/Distribution of Unique Stems per Term Group.png
Manuscript Analysis/Data/Outbound/Graphs/Unique vs Non-Unique Stem Counts per Term Group.png
Manuscript Analysis/Data/Outbound/Graphs/Mood Category Coverage by Term Group.png
Manuscript Analysis/Data/Outbound/Graphs/Distribution of Stems by Mood Category.png
Manuscript Analysis/Data/Outbound/Graphs/Distribution of Unique Stems by Mood Category.png
Manuscript Analysis/Data/Outbound/Graphs/Top 10 Stems per Mood Category - Part 1.png
Manuscript Analysis/Data/Outbound/Graphs/Top 10 Stems per Mood Category - Part 2.png
Manuscript Analysis/Data/Outbound/Graphs/Stem Clouds by Term Group.png
Manuscript Analysis/Data/Outbound/Graphs/Top 10 Stems by Term Group.png
Manuscript Analysis/Data/Outbound/Graphs/Co-Occurrence of Unique Stems Between Mood Categories.png
Manuscript Analysis/Data/Outbound/Graphs/Mood Category Network.png
```

### `Survey Data Analysis/survey_data_analysis.ipynb`

This notebook is an earlier exploratory analysis used to profile survey completion and recruitment progress. It summarizes response completion, terms provided across the three survey tracks, category assignments, multiple-category responses, and participant age/gender distributions.

This notebook expects its own local survey export file in the `Survey Data Analysis/` directory. It is not required for the main manuscript cleaning and figure-generation workflow.

### `_Archive Scripts/`

The archive directory contains earlier exploratory notebooks, including mapping analysis, no-match analysis, survey cleaning, and term-cleaning prototypes. These notebooks are retained for process transparency but are not the recommended entry point for reproducing the current analysis.

## Term-level output schema

The cleaned term table produced by `data_cleaning.ipynb` uses a term-level structure. Each row represents a participant-provided mood term from one survey track.

| Column | Description |
|---|---|
| `metadata.ResponseId` | Qualtrics response identifier for the participant response. |
| `metadata.Finished` | Qualtrics completion flag. |
| `metadata.Progress` | Qualtrics progress percentage associated with the response. |
| `metadata.TermTrack` | Survey track from which the term was collected: `Recent`, `Favorite`, or `Avoid`. |
| `metadata.QuestionId` | Original Qualtrics question column for the term. |
| `QX_X.UserProvidedMoodTerm` | Raw participant-provided mood or experience term. |
| `QX_X.CleanedMoodTerm` | Normalized version of the participant-provided term. |
| `QX_X.CleanedTermStem` | Porter stem generated from the cleaned term. |
| `QX_X.RemovedTermFlag` | Boolean flag indicating whether the term was excluded from manuscript analysis. |
| `QX_X.UserCategoryMapping` | Primary category assigned by the respondent. |
| `QX.MultipleCategoryTerm` | Boolean flag indicating whether the respondent marked the term as belonging to multiple categories. |
| `QX#1_X.MultipleCategoryMapping` | Additional respondent-selected categories, when applicable. |
| `QX#2_X_1.UserProvidedCategory` | Participant-provided custom category text, when applicable. |
| `QX#2_X_1.CleanedCategory` | Cleaned version of the participant-provided custom category, when applicable. |

## Installation

This repository uses Python notebooks. Python 3.10 or later is recommended. The notebooks in the inspected repository were run with Python 3.10 and Python 3.11 kernels.

Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
```

Install the required Python packages:

```bash
pip install pandas numpy openpyxl pyspellchecker nltk matplotlib seaborn wordcloud networkx jupyter ipykernel
```

Optional, but recommended, register the environment as a Jupyter kernel:

```bash
python -m ipykernel install --user --name video-game-taxonomy --display-name "Python (video-game-taxonomy)"
```

## Running the main workflow

The notebooks use relative paths. For the manuscript workflow, start Jupyter from the `Manuscript Analysis/` directory:

```bash
cd "Manuscript Analysis"
jupyter lab
```

Run the notebooks in this order:

1. `data_cleaning.ipynb`
2. `data_analysis.ipynb`

The first notebook creates the cleaned/stemmed term table required by the second notebook.

## Local data requirements

The public repository should not include participant-level survey exports or manually curated data-cleaning workbooks. To reproduce the workflow locally, place the required files in the expected directory structure:

```text
Manuscript Analysis/
└── Data/
    ├── Inbound/
    │   ├── final_data_export.csv
    │   └── cleaned_terms_updated.xlsx
    └── Outbound/
        └── Graphs/
```

The `Outbound/` and `Outbound/Graphs/` directories may need to be created before running the notebooks if they are not already present.

## Reproducibility notes

- The workflow is sensitive to Qualtrics column names. The notebooks assume the survey export uses the original Qualtrics question identifiers, such as `Q2_1`, `Q3_1`, `Q6_1`, `Q7_1`, `Q10_1`, and `Q11_1`.
- The manuscript cleaning workflow uses a fixed survey date cutoff of `2025-06-01`.
- Track-specific completion thresholds are hard-coded in the cleaning notebook.
- Term normalization depends on the manually curated `cleaned_terms_updated.xlsx` workbook.
- The notebook outputs may change if the manual cleaning workbook is revised.
- Generated figures are saved to `Manuscript Analysis/Data/Outbound/Graphs/`.

## Data and ethics statement

This repository is intended to share research code, not participant-level data. Survey exports may contain participant responses and should be handled according to the relevant consent, privacy, and data-management requirements for the study.

Do not commit raw survey exports, manually cleaned participant-level tables, or other sensitive data files to the public repository.

## Citation

This repository has been archived through Zenodo. Please cite the software record as:

```text
Hirt, J. (2026). Video Game Taxonomy Analysis (v1.0.1). Zenodo. https://doi.org/10.5281/zenodo.20936124
```

BibTeX:

```bibtex
@software{hirt_video_game_taxonomy_analysis_2026,
  author    = {Hirt, J.},
  title     = {Video Game Taxonomy Analysis},
  year      = {2026},
  publisher = {Zenodo},
  version   = {v1.0.1},
  doi       = {10.5281/zenodo.20936124},
  url       = {https://doi.org/10.5281/zenodo.20936124}
}
```

## License

This project is licensed under the MIT License. See [`LICENSE`](LICENSE) for details.

## Maintainer

Juliana Hirt  
GitHub: [`jhirt1`](https://github.com/jhirt1)
