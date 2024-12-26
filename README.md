# 16S-MicrocoleusDB
Benthic cyanobacterial blooms, often associated with anatoxin-a and congeners (ATX) production, are increasingly reported in freshwater ecosystems. Among these, *Microcoleus* (Oscillatoriales) blooms are frequently documented.


This repository provides an updated *Microcoleus* 16S rRNA sequence database with 104 near-complete sequences from reported genomes (detailed in the [16SDb_description](https://github.com/Cecilio8422/16S-MicrocoleusDB/blob/225fbff55a98df842350cbe16e43ba2bbe55c74c/16SDb_description.xlsx) file). The sequences are available in FASTA format ([Microcoleus_16Sseq](https://github.com/Cecilio8422/16S-MicrocoleusDB/blob/225fbff55a98df842350cbe16e43ba2bbe55c74c/Microcoleus_16Sseq.fasta)), with taxonomy provided in a CSV file ([Microcoleus_16Stax](https://github.com/Cecilio8422/16S-MicrocoleusDB/blob/225fbff55a98df842350cbe16e43ba2bbe55c74c/Microcoleus_16Stax.csv)). Species clusters were defined using a 95% Average Nucleotide Identity (ANI) threshold.

Additional resources include a *Microcoleus*-curated Silva database compatible with QIIME2 for microbial community profiling, and the *Microcoleus-classifier* tool for identifying *Microcoleus* 16S sequences via global alignment methods.

## *Microcoleus*-refined SILVA Database for QIIME2
High-throughput 16S rRNA gene sequencing is an essential tool for studying microbial communities in benthic cyanobacterial blooms. Using the RESCRIPt QIIME2 plugin, the SILVA database (version 138.1) was refined to enhance taxonomic accuracy for *Microcoleus*:

* Misclassified *Microcoleus* species (e.g., *Microcoleus vaginatus* and *Microcoleus autumnalis*) previously annotated under the genus *Tychonema* were corrected.
* Sequences labeled as "uncultured" and assigned to *Tychonema* without any additional reference were removed.
* The database was augmented with the *Microcoleus* sequences from the 16S-MicrocoleusDB.

The [sequences](https://github.com/Cecilio8422/16S-MicrocoleusDB/blob/f18c08bb62bf7455a700f8d892c8eed1e0680f1d/silva-138.1-ssu-nr99-seqs_corrected-filt_Mc.qza) and [taxonomy](https://github.com/Cecilio8422/16S-MicrocoleusDB/blob/main/silva-138.1-ssu-nr99-tax_corrected_Mc.qza) of this improved SILVA database are fully compatible with QIIME2 and ensures more precise microbial community profiling, particularly for studies focusing on *Microcoleus*-suspected mats.

### Activate QIIME2
```
conda activate qiime2-amplicon-2024.10 # change this for your QIIME2 version
```

### Create an Amplicon-Region-Specific Classifier
Use the SILVA-corrected database to extract sequences matching your primers:
```
qiime feature-classifier extract-reads \
    --i-sequences silva-138.1-ssu-nr99-seqs_corrected-filt_Mc.qza \
    --p-f-primer AGRGTTYGATYMTGGCTCAG # Replace with your forward primer sequence \
    --p-r-primer RGYTACCTTGTTACGACTT \  # Replace with your reverse primer sequence \
    --p-n-jobs 2 \
    --p-read-orientation 'forward' \
    --o-reads silva-138.1-ssu-nr99-seqs_corrected-filt_Mc-27f-1492r.qza #change the name according to your data
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
### Build the Classifier
Train a Naive Bayes classifier for your amplicon region:
```
qiime feature-classifier fit-classifier-naive-bayes \
    --i-reference-reads silva-138.1-ssu-nr99-seqs_corrected_Mc-27f-1492r-uniq.qza \
    --i-reference-taxonomy silva-138.1-ssu-nr99-tax_corrected_Mc-27f-1492r-derep-uniq.qza \
    --o-classifier silva-138.1-ssu-nr99-tax_corrected_Mc-27f-1492r-uniq-classifier.qz
```

### Classifiying your data
Classify sequences using the trained classifier:
```
qiime feature-classifier classify-sklearn --i-classifier silva-138.1-ssu-nr99-tax_corrected_Mc-27f-1492r-uniq-classifier.qza \
    --i-reads your_sequences.qza # Replace with your sequences \
    --o-classification my_sequences_silva-138.1-ssu-nr99-tax_corrected_Mc-27f-1492r-uniq-classifier.qza # change your name ouput as desired
```

