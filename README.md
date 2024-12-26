# 16S-MicrocoleusDB
Benthic cyanobacterial blooms, often linked to anatoxin-a and congeners (ATX) production, are increasingly documented in rivers and lakes, with *Microcoleus* (Oscillatoriales) blooms reported frequently.

This repository provides an updated *Microcoleus* 16S rRNA sequence database with 104 near-complete sequences from reported genomes (detailed in the [16SDb_description](https://github.com/Cecilio8422/16S-MicrocoleusDB/blob/225fbff55a98df842350cbe16e43ba2bbe55c74c/16SDb_description.xlsx) file). The sequences are available in FASTA format ([Microcoleus_16Sseq](https://github.com/Cecilio8422/16S-MicrocoleusDB/blob/225fbff55a98df842350cbe16e43ba2bbe55c74c/Microcoleus_16Sseq.fasta)), with taxonomy provided in a CSV file ([Microcoleus_16Stax](https://github.com/Cecilio8422/16S-MicrocoleusDB/blob/225fbff55a98df842350cbe16e43ba2bbe55c74c/Microcoleus_16Stax.csv)). Species clusters were defined using a 95% Average Nucleotide Identity (ANI) threshold.

Additional resources include a *Microcoleus*-curated Silva database compatible with QIIME2 for microbial community profiling, and the *Microcoleus-classifier* tool for identifying *Microcoleus* 16S sequences via global alignment methods.

## *Microcoleus*-refined SILVA Database for QIIME2
High-throughput 16S rRNA gene sequencing is an essential tool for studying microbial communities in benthic cyanobacterial blooms. Using the RESCRIPt QIIME2 plugin, the SILVA database (version 138.1) was refined to enhance taxonomic accuracy for *Microcoleus*:

* Misclassified *Microcoleus* species (e.g., *Microcoleus vaginatus* and *Microcoleus autumnalis*) previously annotated under the genus *Tychonema* were corrected.
* Sequences labeled as "uncultured" and assigned to *Tychonema* without any additional reference were removed.
* The database was augmented with the *Microcoleus* sequences from the 16S-MicrocoleusDB.

This improved [SILVA database](https://github.com/Cecilio8422/16S-MicrocoleusDB/blob/f18c08bb62bf7455a700f8d892c8eed1e0680f1d/silva-138.1-ssu-nr99-seqs_corrected-filt_Mc.qza) is fully compatible with QIIME2 and ensures more precise microbial community profiling, particularly for studies focusing on *Microcoleus*-suspected mats.



### Creating an amplicon-region-specific Classifier
To tailor the SILVA database to your specific amplicon region, use the following command to extract sequences corresponding to your primers:

```
qiime feature-classifier extract-reads \
  --i-sequences silva-138.1-ssu-nr99-seqs_corrected-filt_Mc.qza \
  --p-f-primer AGRGTTYGATYMTGGCTCAG \ # Replace with your forward primer sequence
  --p-r-primer RGYTACCTTGTTACGACTT \  # Replace with your reverse primer sequence
  --p-n-jobs 2 \
  --p-read-orientation 'forward' \
  --o-reads silva-138.1-ssu-nr99-seqs_corrected-filt_Mc-27f-1492r.qza 
```

### Dereplicate
There are different modes to dereplicate the database, here we will use the 'unique' mode:
```
qiime rescript dereplicate \
  --i-sequences silva-138.1-ssu-nr99-seqs_corrected-filt_Mc-27f-1492r.qza \
  --i-taxa silva-138.1-ssu-nr99-tax_corrected_Mc.qza \
  --p-mode 'uniq' \
  --o-dereplicated-sequences silva-138.1-ssu-nr99-seqs_corrected_Mc-27f-1492r-uniq.qza \
  --o-dereplicated-taxa  silva-138.1-ssu-nr99-tax_corrected_Mc-27f-1492r-derep-uniq.qza
```
