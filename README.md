# Glioma Survival Modelling

Prognostic modelling of overall survival in adult diffuse glioma using clinical, somatic mutation and gene expression data. Survival-weighted gene-set scores and Cox prognostic models are developed in The Cancer Genome Atlas (TCGA) and externally validated in the Chinese Glioma Genome Atlas (CGGA), separately for glioblastoma (GBM) and lower-grade glioma (LGG).

## Notebooks

The notebooks are run in the following order. Later notebooks load files saved by earlier ones.

| Step | Notebook | Purpose |
|---|---|---|
| 1 | `Biological Feature Selection/Bio_Feature_Selection.ipynb` | Selects the somatic mutation genes and the gene expression panel, and defines the gene sets |
| 2 | `TCGA/TCGA_GBM.ipynb` | Model development in TCGA GBM |
| 2 | `TCGA/TCGA_LGG.ipynb` | Model development in TCGA LGG |
| 3 | `CGGA/CGGA_GBM.ipynb` | External validation in CGGA GBM |
| 3 | `CGGA/CGGA_LGG.ipynb` | External validation in CGGA LGG |
| 4 | `Combined Results/Combined_Results.ipynb` | Figures combining both tumour types and both cohorts |

The TCGA notebooks cover data cleaning, exploratory analysis, a comparison of survival modelling methods, construction of cross-fitted, survival-weighted gene-set scores, multivariable Cox models, the prognostic models and their performance. They also save the weights, scalers and fitted models used for external validation.

The CGGA notebooks apply the saved TCGA weights to construct the scores, refit the prognostic models to assess whether the associations replicate, and apply the TCGA models with their coefficients fixed to assess discrimination, calibration and risk stratification.

### Score construction setting

The TCGA notebooks contain a `SCORE_METHOD` setting:

- `"enet"`: survival-weighted scores from a cross-fitted elastic-net Cox model (main analysis)
- `"mean"`: unweighted mean of the standardised gene expression (sensitivity analysis)

Both settings write to the same saved files, so the TCGA notebooks must be run with `"enet"` before running the CGGA and combined notebooks for the main analysis.

## Data

The data are publicly available and are not included in this repository.

- **TCGA:** projects TCGA-GBM and TCGA-LGG, downloaded from the [Genomic Data Commons](https://portal.gdc.cancer.gov/): clinical data, masked somatic mutation files and RNA-sequencing gene expression (STAR gene counts, TPM).
- **CGGA:** downloaded from the [CGGA portal](https://www.cgga.org.cn/): clinical and gene expression data of the mRNAseq_693 dataset, and somatic mutation data of the WEseq_286 dataset.
- **cBioPortal:** mutated-gene frequency tables, used to select the somatic mutation genes, downloaded from [cBioPortal](https://www.cbioportal.org/) for the Glioblastoma Multiforme (TCGA GDC, 2025) and Brain Lower Grade Glioma (TCGA, PanCancer Atlas) studies and saved as `cBioPort_GBM.txt` and `cBioPort_LGG.txt`.
- **MSigDB:** gene set overlap tables, used to select the pathway genes. Each table was created with the Compute Overlaps tool of [MSigDB](https://www.gsea-msigdb.org/gsea/msigdb), comparing one hallmark gene set with the C6, CP:KEGG_MEDICUS and CP:REACTOME collections and showing the top 50 overlaps:
  - HALLMARK_PI3K_AKT_MTOR_SIGNALING: `pi3k_overlap.tsv`
  - HALLMARK_EPITHELIAL_MESENCHYMAL_TRANSITION: `emt_overlap.tsv`
  - HALLMARK_HYPOXIA: `hypoxia_overlap.tsv`
  - HALLMARK_G2M_CHECKPOINT: `g2m_overlap.tsv`

  These files are not included because they contain gene set content subject to the MSigDB licence.

### Folder structure

The notebooks expect the data and saved outputs in a separate project folder with this structure:

```
<project folder>/
├── Biological Feature Selection/
│   ├── Somatic Mutations/        cBioPortal mutation frequency tables
│   ├── Gene Expression/Pathways/ pathway gene overlap tables
│   └── Final Feature Sets/       selected mutation and expression features (created by step 1)
├── TCGA/
│   ├── TCGA-GBM/                 GDC downloads (clinical, somatic mutation, gene expression)
│   ├── TCGA-LGG/                 GDC downloads (clinical, somatic mutation, gene expression)
│   └── saved_models/             created by step 2
└── CGGA/
    ├── mRNAseq_693/              clinical and gene expression files
    ├── WEseq_286/                somatic mutation file
    └── saved_models/             created by step 3
```

All file paths in the notebooks begin with `C:\Users\abbyn\OneDrive\Desktop\Research Project\`. Before running, replace this with the location of your project folder, for example using find and replace in each notebook.

The CGGA notebooks expect each downloaded CGGA file to be extracted into a folder of the same name, for example `CGGA/mRNAseq_693/Clinical/CGGA.mRNAseq_693_clinical.20200506.txt/CGGA.mRNAseq_693_clinical.20200506.txt`.

## Environment

The analyses were run in Python 3.11.9 with:

- lifelines 0.30.3
- scikit-survival 0.28.0
- scikit-learn 1.9.1
- SciPy 1.17.1
- statsmodels 0.15.0

together with pandas, NumPy, Matplotlib, seaborn, joblib and PyTorch (used for the neural network in the comparison of survival modelling methods).

## Reproducibility

Random seeds are fixed in all cross-validation, cross-fitting and bootstrap procedures and in the neural network, so rerunning the notebooks in the stated environment should reproduce the reported results.
