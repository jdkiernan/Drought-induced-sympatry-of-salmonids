# Drought-induced-sympatry-of-salmonids

Code and data repository for the manuscript:
**"Drought-induced sympatry of two salmonid species: Feeding ecology and trophic niches of potential competitors"** by Kiernan et al.

Questions regarding these analyses, data structures, or the R Markdown scripts should be directed to Dr. Joseph Kiernan at joseph.kiernan@noaa.gov.

---

## Repository Structure & File Descriptions

### Analysis Scripts
*   `Lagoon_SIA.Rmd`: R Markdown script containing all analysis workflows for natural abundance stable isotope data $\delta^{13}\text{C}$ and $\delta^{15}\text{N}$.
*   `Lagoon_GCA.Rmd`: R Markdown script containing all analysis workflows for juvenile salmonid diet (stomach/gut content) data.

### Raw Data Files (`/` or Root Input Directories)
*   `Lagoon_fish_isotope.csv`: Base stable isotope metadata including raw fisheries metrics, species identifications, fork lengths, and raw/lipid-corrected isotope values.

*   `OUT.Consumed.csv`: Raw count data of specific prey items found across individual stomach samples by species and month.

*   `TaxaGroupings.csv`: Lookup table linking raw prey items to aggregated composite categories and standardized shorthand codes (e.g., `AME`, `GAM`) used in downstream modeling and figure labels.

*   `IRI_input.csv`: Formatted input file containing calculated numeric percentages (`PercentNumber`, `PercentOccurrence`, `PercentMass`) grouped by predator species and month.

*   `IRI_input_entirestudy.csv`: Formatted input file mapping numeric percentage diet metrics collapsed globally for the entire study period.

*   `DietR_Sankey_Coho.csv` & `DietR_Sankey_Steelhead.csv`: Pre-sorted and arranged `%IRI` metrics used to render individual components of the network visualizations.

*   `DietR_IRI_Output_entirestudy.csv`: Reference dataset containing overall study importance metrics used to mathematically scale the monthly values in network plots.

---
## Data Wrangling & Analysis Workflow

### 1. Gut Content Analysis (GCA) Pipeline (`Lagoon_GCA.Rmd`)
This script processes traditional stomach content matrices to evaluate structural feeding dynamics, similarities, and prey preferences.

*   **Directory Setup:** Automatically checks for and creates `Lagoon_GCA_Input_Data/` and `Lagoon_GCA_Results/` (with subfolders for `IRI`, `Schoener_Overlap`, `IndVal`, `Figures`, and `Tables`).
*   **Trophic Modeling (DietR):** Uses the `CompositeIndices` function in `dietr` to calculate the Index of Relative Importance (IRI) using raw Percentage Number ($N\%$), Biomass ($M\%$), and Frequency of Occurrence ($FO\%$). Models are executed sequentially by species/month and globally as a study-wide aggregate. Outputs are exported to `IRI_Monthly_Output.csv`, `IRI_Study_Output.csv`, and organized into ranked summary matrix formats (Supplemental Table S3).
*   **Diet Overlap Characterization:** Employs the `FSAmisc` package to calculate **Schoener's Similarity Index ($D$)** dynamically across monthly data slices and global pooled frames, outputting metrics to `monthly_diet_overlap_summary.csv` to indicate significant ecological overlaps ($\ge 0.6$).
*   **Indicator Value Analysis:** Employs Dufrêne & Legendre's **IndVal method** via the `multipatt` function in `indicspecies` ($9,999$ permutations) to isolate specific prey types strictly associated with either Coho or Steelhead foraging patterns across changing time steps (Table 2).
*   **Community Ordinations & Dissimilarity (vegan):** Standardization of prey abundance matrices via `decostand` followed by arcsine square-root transformation. Performs:
    *   **Nonmetric Multidimensional Scaling (NMDS):** 2-D ordinations (`metaMDS`) using Bray-Curtis distance matrices ($1,000$ maximum steps) with an internal `envfit` vector-loading overlay to identify prey items with significant formatting impacts ($p \le 0.01$).
    *   **Analysis of Similarities (ANOSIM):** One-way permutation tests (`anosim`) evaluating group separation statistics ($R$) to distinguish feeding partition differences. Outputs are consolidated into an HTML dashboard matrix.
*   **Figures Generated:**
    *   `Figure_1.pdf / .tif`: Bi-directional Sankey network diagrams generated via `networkD3`, structurally adjusted to preserve overall baseline study weight bounds, capturing macro-dietary interactions over time.
    *   `Figure_S1_NMDS.pdf / .tif`: Multi-panel 2-D NMDS ordination plots mapped with $95\%$ centroid ellipses and diagnostic vector loading overlays.

### 2. Stable Isotope Analysis (SIA) Pipeline (`Lagoon_SIA.Rmd`)
This script executes a complete ecological niche analysis using carbon ($\delta^{13}\text{C}$) and nitrogen ($\delta^{15}\text{N}$) stable isotope ratios.

*   **Directory Setup:** Automatically checks for and creates `Lagoon_SIA_Input_Data/` and `Lagoon_SIA_Results/` (with internal subfolders for `SIBER`, `NicheROVER`, `Figures`, and `Tables`).
*   **Mathematical Lipid Correction:** Identifies fin clips with a $\text{C:N ratio} > 3.5$. Applies the linear percentage lipid calculation model (Post et al. 2007) combined with a nonlinear normalization framework (Kiljunen et al. 2006) to generate the standardized `d13C_corr` variable used in all downstream models.
*   **Statistical Contrasts:** Runs non-parametric **Wilcoxon Rank Sum tests** to contrast monthly differences in $\delta^{13}\text{C}$ and $\delta^{15}\text{N}$ between Coho Salmon (`Onki`) and Steelhead (`Onmy`), incorporating a Bonferroni correction for multi-comparison control. Exports outputs to `Isotope_Wilcoxon_Results.csv` and generates formatted summary tables (Supplemental Table S4).
*   **Core Niche Breadth Metrics (SIBER):** Utilizes the `SIBER` package to calculate Maximum Likelihood point estimates for Total Area (TA) and Standard Ellipse Area corrected for small sample sizes (SEAc). Runs a Bayesian Markov Chain Monte Carlo (MCMC) model ($2 \times 10^4$ iterations, $1 \times 10^3$ burn-in) to derive the Bayesian Standard Ellipse Area ($\text{SEA}_b$), extracting posterior distributions, mode estimates, and credible intervals ($50\%$, $75\%$, $95\%$). Exports results to `SIBER_Metrics_Summary.csv` and `QC_Data_Validation.csv`.
*   **Isotopic Niche Overlap (nicheROVER):** Implements a Bayesian framework via `nicheROVER` ($1,000$ posterior iterations) to calculate directional, probabilistic niche overlap between species across monthly intervals and pooled study-wide datasets. Generates niche sizes, standard errors, and $95\%$ credible interval bounds. Exports outputs to localized `Rover_Overlap_[Period].csv` and `NicheROVER_Overlap_Summary_Table.csv`.
*   **Figures Generated:**
    *   `Figure_2.pdf / .tif`: Multi-panel stable isotope biplots showing core niches via $40\%$ and $95\%$ SEAc boundary polygons.
    *   `Figure_3.pdf / .tif`: Credible interval distribution plots illustrating isotopic diet breadth changes.
    *   `Figure_4.pdf / .tif`: Scaled posterior density panels displaying probabilistic directional overlap distributions.

