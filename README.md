
# Metagenomic Body Site Classification Project

## Project Overview
This project aims to classify different human body sites (e.g., Colon, Oral cavity, Nasal cavity, Skin crease, Vagina) based on their metagenomic functional enzyme profiles. Using machine learning models, we investigate whether the unique enzymatic activities within a microbial community can accurately predict its origin within the human body. This project includes data loading, preprocessing, dimensionality reduction using PCA, and evaluation of several classification models: Logistic Regression with L1 regularization, k-Nearest Neighbors, and a Multi-Layer Perceptron (Neural Network).

## Setup Instructions
To run this notebook and reproduce the results, you need to have Python 3.8+ installed along with the following libraries. You can install them using pip:

```bash
pip install -r requirements.txt
```

The `requirements.txt` file was generated as part of the project and contains the exact versions of the libraries used.

## Data Description
The project utilizes two main datasets:
1.  `HMPWgs_Metagenomic_sequencing_assay.txt`: This file contains functional enzyme abundance data (metagenomic features). Each row represents a sample, and columns represent various enzyme commission (EC) numbers and pathway abundances. Values are enzyme counts.
2.  `HMPWgs-1_Sample_subsettedData.txt`: This file contains metadata for each sample, including `Sample_ID` and the `Host body habitat`, which serves as the target variable for classification.

### Key Data Points:
*   **Features**: Log-transformed enzyme counts and pathway abundances. Initial dimensionality ~4900 features.
*   **Target**: `Host body habitat [UBERON_0000466]` (e.g., 'Colon', 'Oral cavity', 'Nasal cavity', 'Skin crease', 'Vagina').

## Methodology
The project follows these main steps:

1.  **Data Loading and Initial Inspection**: Both datasets are loaded into pandas DataFrames, and their structure, content, and initial relationships are examined.
2.  **Data Preprocessing**:
    *   DataFrames are merged on `Sample_ID`.
    *   Rows with missing target labels are removed (though none were found in this specific run).
    *   Enzyme count features are log-transformed (log(x+1)) to handle skewed distributions and zero values.
    *   Missing values in enzyme columns (due to `pd.to_numeric` coercion or sparse data) are filled with 0.
    *   The `Host body habitat` target variable is label-encoded into numerical categories.
3.  **Dimensionality Reduction (PCA)**:
    *   PCA is initially applied to reduce the features to 2 components for 2D visualization to observe natural clustering of body sites.
    *   A second PCA is performed to reduce dimensionality while retaining approximately 95% of the total variance. This resulted in reducing the ~4900 features to 125 principal components for improved model efficiency.
4.  **Train-Test Split**: The data is split into training (70%) and testing (30%) sets using `train_test_split` with stratification to maintain class balance. The PCA transformation determined from the training data is applied to both training and test sets.
5.  **Model Training and Evaluation**:
    *   **Multiclass Logistic Regression (L1 regularization)**: Trained using `OneVsRestClassifier` and evaluated using stratified 5-fold cross-validation on the training set and on the dedicated holdout test set. Feature importances (top positive coefficients) are visualized.
    *   **k-Nearest Neighbors (k-NN)**: Trained on PCA-transformed data and evaluated using stratified 5-fold cross-validation on the training set and on the dedicated holdout test set.
    *   **Neural Network (Multi-Layer Perceptron - MLP)**: Trained on PCA-transformed data with a hidden layer architecture of (64, 32) and evaluated using stratified 5-fold cross-validation on the training set and on the dedicated holdout test set.

Model performance is assessed using the Macro F1-score and confusion matrices for both cross-validation and the final test set evaluations.

## Key Findings

### PCA Visualization:
*   The 2D PCA plot clearly showed distinct clustering of different body sites, particularly separating 'Colon' samples from others. 'Oral cavity' and 'Nasal cavity' samples clustered closely, indicating functional similarities.

### Model Performance (Macro F1-scores on Holdout Test Set):
*   **Logistic Regression**: Achieved a Macro F1-score of approximately 0.9326 on the test set. The coefficient analysis revealed distinct enzyme and pathway signatures for each body site, confirming biological relevance.
*   **k-Nearest Neighbors**: Achieved a Macro F1-score of approximately 0.9429 on the test set, demonstrating strong performance with PCA-reduced features.
*   **Neural Network (MLP)**: Achieved a Macro F1-score of approximately 0.8996 on the test set. While performing well, it was slightly behind Logistic Regression and k-NN in this specific setup.

Overall, the project successfully demonstrated that metagenomic enzyme profiles are highly effective in classifying human body sites. The models, especially Logistic Regression and k-NN, showed excellent predictive capabilities, validating the hypothesis that functional metagenomic data can reveal significant biological distinctions between different microbial habitats within the human body.
