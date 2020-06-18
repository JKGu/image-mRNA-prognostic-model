# image-mRNA-prognostic-model
This project builds a lasso-regularized Cox proportional hazards model (lassoCox) to predict the prognosis using a combination of image features extracted from H&E whole-slide tissue images and gene expression profiles. The effectiveness of the model is demonstrated in a large cohort of 410 clear cell renal cell carcinoma (ccRCC) patients acquired from The Cancer Genome Atlas. Patients' risk indices generated by our model are strongly associated with survival. Log-rank test p value for low- and high-risk patient stratification is 8.79e-10.

The code in this project will reproduce the results in our paper submitted to Cancer Research, "Integrative analysis of histopathological images and genomic data for predicting clear cell renal cell carcinoma prognosis". Our codes were implemented and tested using win10, matlab/2015b, and R 3.3.1.

**Outline**
* Overview
* Code organization and directory description
* Downloading whole-slide tissue images
* Running on other operating systems
* Contact information
* References

Overview
-----
In this project, we combine histopathological image data and genomic data to predict prognosis of ccRCC patients. For image data, 150 cellular morphological features are extracted. For genomic data, gene co-expression network mining [1] is performed using gene expression RNA-seq data resulting in 15 networks. Then each network (module) is described by its eigengene which is defined as the first principal component obtained by singular vector decomposition on the expression profiles of genes in this module [2]. For the 150 image features and 15 eigengenes, we use the median of each feature to cut the petients into two groups: low and high group. Next, log-rank test is used to select features that are associated with patient survival. After that, these survival-associated features are used to train a lasso-regularized Cox proportional hazards regression model, which can predict the death risk of patients. The risk index generated by our model is strongly related to patient survival. Multivariate Cox regression shows the predicted risk index is independent of other known prognostic facotrs including clinical variables (tumor grade and stage), six gene expression signatures, and five somatic mutation genes.

Code organization and directory description
-----
Not all patients have image data, gene expression data, and somatic mutation data simultaneously, so these three types of data are first prepared separately and then intersected, resulting in 410 patients with all three types of data available. The execution order of programs in each folder is indicated by their filenames. Suppose there are two files begining with "m": "m1_xxx.m" (or .R) and "m2_xxx.m". Files begining with "m" are main programs that should be executed. The number next to "m" indicates the execution order, that is, we should first run "m1_xxx.m" and then "m2_xxx.m". 

Directory description (codes should also be executed according to the following order)

1. "setup_addpath.m"
Adding the tools in folder "tools" to matlab search path.

2. "tools"
Providing tools (such as reading whole-slide images in .svs format and nucleus segmentation) to be used by other programs. You don't need to run the codes in this folder.

3. "imageAndClinicalInfo"
Preparing the images to be processed (we only focus on solid tumor image) and clinical information like tumor grade, stage, survival time.

4. "imageFeatures"
Extracting image features

5. "mRNA"
Doing gene co-expression network mining and generating eigengene for each network (module).

6. "somaticMutation"
Preparing somatic mutation data. The matrix mutData.mut stores the gene mutation information. 0 stands for wild-type, and 1 stands for mutant.

7. "m1_intersect_image_RNA_SM_clinical.m"
Intersecting image data, gene expression data, somatic mutation data, and clinical data. Patients with all these data available will be used in experiments.
 
8. "experiments"
Conducting experiments.

- 8.1. "clinical"
Survival analysis for clinical variables: tumor grade and stage.

- 8.2. "screenImFeatures"
Survival analysis for each image feature. Kaplan-Meier curves are plotted for survivial-associated features.

- 8.3. "screenEigengenes"
Survival analysis for each eigengene. Kaplan-Meier curves are plotted for survivial-associated features.

- 8.4. "correlateImWithEigengene"
Correlating survival-associated image features with all eigengenes.

- 8.5. "lassoCox"
Lasso-regularized Cox regression model built on both image features and eigengenes. The median of risk indices generated by the model is used to split the patients into two groups. Then log-rank test p value is calculated, and KM survival curve is plotted.

- 8.6. "uniAndmultivariateCox"
univariate and multivariate Cox regression analysis are used to evaluate the prognostic value of the risk index generated by our prognosic model, and other other known prognosic factors including clinical variables (tumor grade and stage), six gene expression signatures, and five somatic mutation genes. Experiments show that our lassoCox model and tumor stage are strongly related to survival, and they are independent prognosic facotrs. So we also show KM survival curves of patients stratified by combining the risk index of lassoCox model and tumor stage.


Downloading whole-slide tissue images
-----
Gene expression data, and somatic mutation data are already included in this project. Image data are not included because of its huge size (about 310GB), but the computed image features are included.

If you'd like to download the data from the Genomic Data Commons (GDC), you first need to install [GDC data transfer tool](https://gdc.cancer.gov/access-data/gdc-data-transfer-tool). Once installed, go to the directory, "imageAndClinicalInfo/images", open a command window in this folder, and download the image data using the following command: 
"gdc-client download -m ../gdc_manifest_20161130_195535.txt"

This may take a long time depending on your internet speed.

Running on other operating systems
-----
All codes are implemented and tested on win10. However if you use other operating systems, you need to delete the openslide package in the folder, "tools", and reconfigure it based on your own systems. You can download source code or binaries for [OpenSlide](http://openslide.org/download/). If downloading the source, make sure to follow instructions provided by OpenSlide for compiling the binaries. Remember to add the folders of these libraries and the include files to the MATLAB path.

Contact information
-----
Jun Cheng, Southern Medical University, Guangzhou, China. Email: chengjun583@qq.com

References
---
[1] Zhang J, Huang K. Normalized lmQCM?: an Algorithm for Detecting Weak Quasi-clique Modules in Weighted Graph with Application in Functional Gene Cluster Discovery in Cancer. Cancer Inform. 2016;1:1.  
[2] Alter O, Brown PO, Botstein D. Singular value decomposition for genome-wide expression data processing and modeling. Proc Natl Acad Sci. 2000;97:10101¨C6.
