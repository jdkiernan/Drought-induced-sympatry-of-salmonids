# Drought-induced-sympatry-of-salmonids
Code for manuscript, "Drought-induced sympatry of two salmonid species: Feeding ecology and trophic niches of potential competitors" by Kiernan et al. 

Files Descriptions:

- The "Lagoon_GCA.Rmd" is a markdown script that contains all of the analyses for the juvenile salmonid diet samples.
- The "Lagoon_SIA.Rmd" is a markdown script that contains all of the analyses for the juvenile salmonid isotope samples

Datafiles called by the scripts:

- The "Consumed.csv" datafile contains the count data for prey items found in each fish's diet during each sampling event.
- The datafile "TaxaGroupings.csv" included prey items and composite prey categories used for labeling the figures (see Table 1 in the manuscript).
- The "IRI_input.csv" and "IRI_input_entirestudy.csv" datafiles are used to calulate the Index of Relative Importance (IRI) for each species and month and the overall (entire study period combined) respectively with the DietR package.
- The "DietR_Sankey_Coho.csv", "DietR_Sankey_Steelhead.csv", and "DietR_IRI_Output_entirestudy.csv" datafiles are the IRI output formatted for the network plot (presented as Figure 1 in the manuscript).
- The "Lagoon_fish_isotope.csv" datafile is used in both of the isotope markdown scripts as the isotope input file.