# Experiment Metadata

| Field | Value |
|-------|-------|
| **Overall biological question of study** | How does deletion of the proteasome biogenesis regulator RPN4 affect the global proteome, and what protein categories show differential expression? |
| **Organism** | Saccharomyces cerevisiae (Baker's yeast) |
| **Cell Type** | Whole cell lysate |
| **Control group** | Wild-type (WT) strain |
| **Test group** | RPN4 (Δrpn4 deletion mutant) strain |
| **Treatment** | Tunicamycin treatment |
| **Time points** | 0h, 2h, 4h |
| **Experiments** | 'RPN4 0 hours', 'RPN4 2 hours', 'RPN4 4 hours', 'wt 0 hours', 'wt 2 hours', 'wt 4 hours' |
| **Biological Replicates** | 4 per condition |
| **Technical Replicates** | None (each sample run once) |
| **Sample Identifiers - Control** | wt_0h_1, wt_0h_2, wt_0h_3, wt_0h_4 <br> wt_2h_1, wt_2h_2, wt_2h_3, wt_2h_4 <br> wt_4h_1, wt_4h_2, wt_4h_3, wt_4h_4 |
| **Sample Identifiers - Test** | RPN4_0h_1, RPN4_0h_2, RPN4_0h_3, RPN4_0h_4 <br> RPN4_2h_1, RPN4_2h_2, RPN4_2h_3, RPN4_2h_4 <br> RPN4_4h_1, RPN4_4h_2, RPN4_4h_3, RPN4_4h_4 |
| **Methods** | Wild-type (SSY122) and Δrpn4 (SSY784) cells were grown to mid-log phase in SCD medium. Cultures were diluted to OD 0.4, and 5 ODs of cells were harvested as untreated samples. Tunicamycin was added to the remainder of each culture at a final concentration of 5 μg/ml, cells were grown for 4 hr, and 5 ODs of cells were harvested as treated samples. Cells were collected by centrifugation and snap frozen in liquid nitrogen. Samples from four independent experiments were used for proteomic analysis. <br><br> Cells were resuspended in ice-cold lysis buffer (50 mM Tris, pH 7.5, 0.5 mM EDTA, 1x Roche complete protease inhibitors) and disrupted by bead beating. Proteins were solubilized by adding SDS to a final concentration of 1.8% (w/v) and heating to 95°C for 10 min. Protein concentrations were determined with a BCA assay kit (Thermo Fisher Scientific). Per sample, 200 μg protein was precipitated with acetone and resuspended in digestion buffer (8 M Urea, 50 mM Tris pH 8.5, 1 mM DTT) to a concentration of 4 mg/ml. 40 μg protein was alkylated using 5 mM iodoacetamide for 1 hr, digested with LysC (enzyme to protein = 1:50 (w/w)) for 5.5 hr, diluted to 2 M Urea, and digested with Trypsin (1:50 (w/w)) for another 13 hr at room temperature. Digested peptides were acidified to 1% (v/v) trifluoroacetic acid, cleared of precipitates by brief centrifugation, desalted via SDB-RPS cleanup, and analyzed on a Q Exactive HF-X Hybrid Quadrupole-Orbitrap mass spectrometer (Thermo Fisher Scientific), essentially as described (Itzhak et al., 2016). Raw files were processed using MaxQuant version 1.6, using the MaxLFQ algorithm for label free-quantification. |