# Meningioma Risk Classifier
 This README contains the necessary instructions for installing and running the meningioma risk classifier developed in Duckett et al. The K-nearest neighbor classifier categorizes a given input sample as LOW, MEDIUM, or HIGH risk based on methylation data. The three risk groups were identified by performing K-means clustering on 217 meningioma samples and characterizing progression free survival (PFS) in the resuting clusters.

## Installation
The classifier is freely available as a Docker container, which includes all prerequisite software and reference data to run new samples. Docker is required and can be installed from https://www.docker.com/. After installing Docker, the Docker container with the classifier can be pulled from a Quay repository using the following command

```sh
docker pull quay.io/xxx/meningioma-risk-classifier:v0.1.0
```

## Input
The classifier requires
- The Docker container
- IDAT files from Illumina methylation chips (450K, EPICv1, or EPICv2)

## Use
The classifier can be run with the following command

```sh
docker run -v </path//to/idat_files>:/methylation/input -v </path/to/output_folder>:/methylation/output meningioma-risk-classifier:v0.1.0 Rscript run_meningioma_risk_analysis.R --sample_name <chip_ind>
```

where </path/to/idat_files> is the full local path to the directory containing the IDAT files you want to run, </path/to/output_folder> is the full local path to the directory where you want the results to be written, and <chip_ind> is the chip and index number for the sample to run, e.g. 208527730003_R06C01.

IMPORTANT: the IDAT files should be unzipped and named with the chip and index number, e.g. 208527730003_R06C01_Grn.idat and 208527730003_R06C01_Red.idat.

## Output
Classifier results will be both printed to the screen and written to files in the user-specified output folder. Output files include
- classification_results_meningioma_<chip_ind>.csv: A table with the highest scoring class for the sample and the associated classifier score
- cluster_plot_<chip_ind>.png: A PCA cluster plot of the given sample compared to reference samples
- prediction_scores_meningioma_<chip_ind>.csv: A table containing each classifier class and score for the given sample

## Interpretation
The results printed to the screen and in the classifification results file represent the highest scoring class. Based on classifier validation, we recommend using a score threshold of 0.75, which provides high accuracy, specificity, and sensitivity. Scores higher than the threshold are considered a "match" and represent high classifier confidence while scores below the threshold indicate greater classifier uncertainty.

Classifier and PCA cluster plot results represent two different types of analysis. The Classifier uses a K-nearest neighbor (KNN) machine learning algorithm to classify a defined set of meningioma tumor samples into risk classes while PCA is a dimensionality reduction and data visualization method. Additionally, the classifier uses differentially methylated probes among the K-means risk classes while the cluster plot uses the most variable probes in the reference dataset. While these two approaches will often produce concordant results, discrepant results are possible. Assay validation was solely based on the KNN Classifier. The PCA plot is provided as additional information for samples that are not a match to any of the risk categories in the KNN Classifier.

## Citation
If you publish work using the meningioma risk classifier,, please cite Duckett et al.

## Additional Information
The meningioma risk methylation classifier is for research use only. While it has been clinically validated at Northwestern Medicine, this validation does not apply to other laboratories.
Methylation data is analyzed by a Machine Learning algorithm that classifies the sample into one of 3 possible meningioma risk categories. Classifier validation at NM resulted in high accuracy (96.1% concordant, 90.8% match) in a cohort of 76 meningioma samples.
Meningioma progression risk classes include LOW RISK, MEDIUM RISK, and HIGH RISK based on k-means clustering of methylation data from 217 meningioma samples and represent significant differences in progression free survival (PFS).
This test was developed and its performance characteristics determined by Northwestern Memorial Hospital Pathology Laboratory. It has not been cleared or approved by the U.S. Food and Drug administration.

