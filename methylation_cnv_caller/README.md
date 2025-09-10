# AccuMethCNV
 This README contains the necessary instructions for installing and running AccuMethCNV, the methylation array CNV caller developed in Duckett et al. AccuMethCNV calls losses and amplifications in tumor suppressors and oncogenes based on methylation array data. It was developed using 371 training CNS samples and 159 validation CNS samples.

## Installation
AccuMethCNV is freely available via a Docker container, which includes all prerequisite software and reference data to run new samples. Docker is required and can be installed from https://www.docker.com/. After installing Docker, the Docker container can be pulled from a Quay repository using the following command

```sh
docker pull quay.io/bioinformaticsnm/public_resources/accumethcnv:0.1.0
```

## Input
The analysis requires
- The Docker container
- IDAT files from Illumina methylation chips (450K, EPICv1, or EPICv2)
- Tumor purity of the sample
- The patient's sex

## Use
CNV analyses can be run with the following command

```sh
docker run -v </path/to/idat_files>:/methylation/data -v </path/to/output_folder>:/methylation/results quay.io/bioinformaticsnm/public_resources/accumethcnv:0.1.0 Rscript /methylation/scripts/run_cnv_analysis.R --sample_name <chip_ind> --tumor_purity <purity> --sex <sex>
```

where </path/to/idat_files> is the full local path to the directory containing the IDAT files you want to run, </path/to/output_folder> is the full local path to the directory where you want the results to be written, <chip_ind> is the chip and index number for the sample to run (e.g. 208527730003_R06C01), purity is the tumor purity as a fraction (e.g. 0.8), and sex is the patient's sex (M or F). An example is included below:

```sh
docker run -v /data/208527730003:/methylation/data -v /results/208527730003:/methylation/results quay.io/bioinformaticsnm/public_resources/accumethcnv:0.1.0 Rscript /methylation/scripts/run_cnv_analysis.R --sample_name 208527730003_R06C01 --tumor_purity 0.8 --sex M
```

IMPORTANT: the IDAT files should be unzipped and named with the chip and index number, e.g. 208527730003_R06C01_Grn.idat and 208527730003_R06C01_Red.idat.

## Output
CNV results will be both printed to the screen and written to files in the user-specified output folder. Output files include
- cnv_bins_<chip_ind>.igv: A table with logR values for bins across the sample genome
- cnv_chr_sd_<chip_ind>.tsv: A table with the overall CNV QC score and logR standard deviations across chromosomes
- cnv_detail_<chip_ind>.txt: A table containing the logR scores for each focal gene
- cnv_plot_<chip_ind>.png: A CNV plot of logR scores across the genome
- cnv_probes_<chip_ind>.igv: A table containing the logR scores for each methylation probe
- cnv_results_<chip_ind>.csv: A table containing the CNV calls for each focal gene
- cnv_segment_sd_<chip_ind>.tsv: A table with the logR standard deviations across segments
- cnv_segments_<chip_ind>.seg: A table containing the logR scores for each segment across the genome

## Interpretation
The results printed to the screen and in the cnv results file represent the CNV calling results. The CNV calls are calculated using correction models that achieved comparable specificity and greater sensitivity than using raw logR values alone. However, performance varied significantly by gene. Therefore we recommend you consult the metrics for each gene in the table below when making interpretations. Additionally, the CNV QC listed in the cnv_chr_sd file has a large effect on CNV calling accuracy. Values above 0.7 are considered QC fails while values <= 0.5 provide even better accuracy.

| Gene     | Model | Specificity | Sensitivity |
|:--------:|:-----:|:-----------:|:-----------:|
| VHL      | Loss  | 0.99        | 0.75        |
| BAP1     | Loss  | 0.94        | 0.80        |
| DROSHA   | Loss  | 0.99        | 0.50        |
| MTAP     | Loss  | 0.92        | 0.94        |
| CDKN2A   | Loss  | 0.83        | 0.92        |
| CDKN2B   | Loss  | 0.67        | 0.83        |
| PTCH1    | Loss  | 0.99        | 0.80        |
| TSC1     | Loss  | 0.99        | 1.00        |
| PTEN     | Loss  | 0.85        | 0.82        |
| SUFU     | Loss  | 0.90        | 0.81        |
| MGMT     | Loss  | 0.96        | 0.85        |
| RB1      | Loss  | 0.79        | 0.75        |
| TP53     | Loss  | 0.93        | 0.80        |
| DGCR8    | Loss  | 0.94        | 0.94        |
| SMARCB1  | Loss  | 0.90        | 0.98        |
| NF2      | Loss  | 0.90        | 1.00        |
| DMD      | X     | 0.93        | 0.90        |
| BCOR     | X     | 0.91        | 0.84        |
| ATRX     | X     | 0.93        | 0.91        |
| 1p19q    | 1p19q | 0.93        | 0.97        |
| NRAS     | AMP   | 0.99        | 0.00        |
| MDM4     | AMP   | 0.96        | 0.50        |
| MYCN     | AMP   | 0.96        | 1.00        |
| TACC3    | AMP   | 1.00        | 0.00        |
| FGFR3    | AMP   | 0.99        | 0.00        |
| PDGFRA   | AMP   | 0.91        | 1.00        |
| SNCAIP   | AMP   | 0.97        | 1.00        |
| EGFR     | AMP   | 0.95        | 1.00        |
| CDK6     | AMP   | 0.89        | 1.00        |
| MET      | AMP   | 0.73        | 1.00        |
| KIAA1549 | AMP   | 0.76        | 1.00        |
| BRAF     | AMP   | 0.82        | 0.83        |
| FGFR1    | AMP   | 0.91        | NA          |
| TACC1    | AMP   | 1.00        | NA          |
| MYBL1    | AMP   | 0.91        | NA          |
| MYC      | AMP   | 0.82        | 1.00        |
| NTRK2    | AMP   | 0.83        | NA          |
| YAP1     | AMP   | 0.98        | 0.00        |
| CCND2    | AMP   | 0.86        | 1.00        |
| MDM2     | AMP   | 0.99        | 0.80        |
| PPM1D    | AMP   | 0.93        | 0.50        |
| C19MC    | AMP   | 0.99        | 1.00        |
| PLCB4    | AMP   | 0.97        | 1.00        |

## Citation
If you publish work using AccuMethCNV, please cite Duckett et al.

## Additional Information
AccuMethCNV is for research use only.
Methylation data is analyzed by conumee and resulting logR values are corrected using linear and spline regressions. Correction models were trained with 371 CNS samples.
CNV calls include losses and amplifications. Caller validation at NM resulted in high accuracy (median = 93% per sample) in a validation cohort of 159 CNS samples
This test was developed and its performance characteristics determined by Northwestern Memorial Hospital Pathology Laboratory. It has not been cleared or approved by the U.S. Food and Drug administration.

