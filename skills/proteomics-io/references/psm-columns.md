# PSM Table Column Mapping

## Standardized Column Names

| Column | Description |
|--------|-------------|
| `sequence` | Peptide amino-acid sequence without modifications |
| `modified_sequence` | Peptide sequence with modification annotations |
| `mods` | List of modifications in unimod standardization |
| `charge` | Charge state of precursor |
| `rt` | Retention time in minutes |
| `mobility` | Ion mobility value |
| `ccs` | Collision Cross Section |
| `proteins` | Protein names (human readable) |
| `uniprot_ids` | UniProt identifiers |
| `genes` | Gene names (HGNC nomenclature) |
| `score` | Primary identification score |
| `fdr` | False discovery rate / q-value |
| `intensity` | Protein group intensity |
| `precursor_intensity` | Precursor-level intensity |
| `precursor_id` | Unique precursor identifier |
| `raw_name` | Original filename / run identifier |
| `scan_num` | MS/MS scan number |
| `decoy` | Boolean decoy indicator |

## Search Engine Column Mapping

### AlphaDIA
| Standard | AlphaDIA Column(s) |
|----------|-------------------|
| raw_name | `run`, `raw.name` |
| charge | `charge`, `precursor.charge` |
| rt | `rt_observed`, `precursor.rt.observed` |
| mobility | `mobility` |
| proteins | `proteins`, `pg.proteins` |
| sequence | `sequence`, `precursor.sequence` |
| score | `score`, `precursor.score` |
| genes | `genes`, `pg.genes` |
| intensity | `intensity`, `pg.intensity` |
| precursor_intensity | `precursor.intensity` |
| precursor_id | `precursor_idx`, `precursor.idx` |
| mods | `mods`, `precursor.mods` |
| peptide_intensity | `peptide.intensity` |

### DIA-NN
| Standard | DIA-NN Column |
|----------|---------------|
| raw_name | `Run` |
| charge | `Precursor.Charge` |
| rt | `RT` |
| mobility | `IM`, `IonMobility` |
| proteins | `Protein.Group` |
| sequence | `Stripped.Sequence` |
| score | `CScore` |
| uniprot_ids | `Protein.Ids` |
| genes | `Genes` |
| scan_num | `MS2.Scan` |
| ccs | `CCS` |
| fdr | `Q.Value` |
| intensity | `PG.MaxLFQ` |
| precursor_intensity | `Precursor.Normalised` |
| precursor_id | `Precursor.Id` |
| fdr1_search1 | `Global.Q.Value` |
| fdr2_search1 | `Global.PG.Q.Value` |
| fdr1_search2 | `Lib.Q.Value` |
| fdr2_search2 | `Lib.PG.Q.Value` |
| gene_intensity | `Genes.MaxLFQ` |

### MaxQuant
| Standard | MaxQuant Column(s) |
|----------|-------------------|
| raw_name | `Raw file` |
| charge | `Charge` |
| rt | `Retention time` |
| mobility | `Mobility`, `IonMobility`, `K0`, `1/K0` |
| proteins | `Proteins` |
| sequence | `Sequence` |
| score | `Score` |
| genes | `Gene Names`, `Gene names` |
| scan_num | `Scan number`, `MS/MS scan number`, `MS/MS Scan Number`, `Scan index` |
| decoy | `Reverse` |
| precursor_mz | `m/z` |
| ccs | `CCS` |
| intensity | `Intensity` |

### MSFragger (PSM TSV)
| Standard | MSFragger Column |
|----------|-----------------|
| raw_name | `raw_name` |
| charge | `Charge` |
| rt | `Retention` |
| mobility | `Ion Mobility` |
| proteins | `Protein` |
| sequence | `Peptide` |
| score | `Hyperscore` |
| scan_num | `scan_num` |
| decoy | `Is Decoy` |
| precursor_mz | `Observed M/Z` |
| intensity | `Intensity` |
| _tmp_mods | `Assigned Modifications` |

### Sage
| Standard | Sage Column |
|----------|-------------|
| raw_name | `filename` |
| charge | `charge` |
| rt | `rt` |
| mobility | `mobility` |
| proteins | `proteins` |
| sequence | `stripped_peptide` |
| score | `sage_discriminant_score` |
| decoy | `is_decoy` |
| fdr | `spectrum_q` |
| modified_sequence | `peptide` |
| scannr | `scannr` |
| peptide_fdr | `peptide_q` |
| protein_fdr | `protein_q` |

### Spectronaut Report
| Standard | Spectronaut Column(s) |
|----------|----------------------|
| raw_name | `R.FileName` |
| rt | `EG.ApexRT`, `EG.MeanApexRT` |
| mobility | `FG.ApexIonMobility` |
| proteins | `PG.ProteinNames`, `PG.ProteinGroups` |
| uniprot_ids | `PG.UniProtIds` |
| genes | `PG.Genes` |

### pFind
| Standard | pFind Column |
|----------|-------------|
| raw_name | `raw_name` |
| charge | `Charge` |
| rt | `RT` |
| proteins | `Proteins` |
| sequence | `Sequence` |
| score | `Final_Score` |
| uniprot_ids | `Proteins` |
| scan_num | `Scan_No` |
| decoy | `Target/Decoy`, `Targe/Decoy` |
| fdr | `Q-value` |

### AlphaPept
| Standard | AlphaPept Column |
|----------|-----------------|
| raw_name | `raw_name` |
| charge | `charge` |
| rt | `rt` |
| mobility | `mobility` |
| score | `score` |
| scan_num | `scan_no` |
| decoy | `decoy` |
| precursor_mz | `mz` |
| fdr | `q_value` |
| spec_idx | `raw_idx` |
