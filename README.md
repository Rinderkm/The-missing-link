# "The Missing Link" – CAS Applied Data Science Final Project

**Author:** Matthias Rinderknecht  
**Institution:** Certificate of Advanced Studies (CAS) in Applied Data Science, University of Bern  
**Date:** June 28, 2024

## Project Overview

This project develops an automated system to link clinical trials between two healthcare databases: BASEC (Swiss Ethics Committee database) and ICTRP (International Clinical Trials Registry Platform). Using machine learning with LinkTransformer and FAISS, the system matches trials with high accuracy, addressing "the missing link" between these important registries.

## Repository Structure

This repository contains:

### Notebooks
- **`Notebook_1_Data_Cleaning.ipynb`** - Dataset preparation and cleaning
  - Loads and cleans raw ICTRP data
  - Filters for Switzerland-based trials
  - Prepares datasets for linking
  - Removes duplicates and validates data quality

- **`Notebook_2_Matching_with_Linktransformer.ipynb`** - Initial linking using LinkTransformer
  - Performs 1:1 record linking between BASEC and ICTRP
  - Generates match scores
  - Analyzes match percentages by confidence bins
  - Identifies incorrect matches for analysis

- **`Notebook_3_fine-tune_LLM.ipynb`** - Fine-tuning the language model
  - Fine-tunes `sentence-transformers/paraphrase-multilingual-mpnet-base-v2`
  - Uses LinkTransformer package for custom training
  - Achieves 97.69% accuracy on test set
  - Produces optimized model for trial linking

- **`Notebook_4_Faiss_vector_search.ipynb`** - FAISS-based matching and search
  - Creates precomputed FAISS index from corpus embeddings
  - Performs efficient vector similarity search
  - Links 1,535 unmatched BASEC trials to ICTRP corpus
  - Includes single-trial matching examples

- **`Notebook_5_Analysis.ipynb`** - Results analysis and performance evaluation
  - Analyzes match statistics by confidence score bins
  - Plots confusion matrices and ROC curves
  - Determines optimal confidence threshold (0.69)
  - Calculates precision, recall, and F1 scores

### Other Files
- **`Readme.md`** - This file
- **Sample data** - De-identified trial datasets

## Quick Start

### Requirements
- Python 3.8+
- Required libraries:
  - pandas
  - scikit-learn
  - sentence-transformers
  - faiss-cpu (or faiss-gpu for GPU acceleration)
  - linktransformer
  - matplotlib
  - numpy

### Installation

```bash
# Clone the repository
git clone https://github.com/Rinderkm/Applied-Data-Science-Course-incl.-Final-Project.git
cd Final_Project

# Install required packages
pip install pandas scikit-learn sentence-transformers faiss-cpu linktransformer matplotlib numpy
```

### Running the Notebooks

Execute notebooks in sequence:

1. **Start with Notebook 1** for initial data preparation
   - Cleans and validates raw data
   - Creates filtered datasets

2. **Run Notebook 2** to see LinkTransformer linking in action
   - Performs initial record matching
   - Generates analysis plots

3. **Execute Notebook 3** to fine-tune the model (optional, computationally intensive)
   - Requires GPU for reasonable training time
   - Creates optimized model for better matching

4. **Use Notebook 4** for FAISS-based search
   - Creates efficient search index
   - Matches remaining unlinked trials

5. **Run Notebook 5** for comprehensive results analysis
   - Evaluates model performance
   - Determines optimal confidence thresholds

## Data

### Privacy Notice
All published datasets have been **de-identified and stripped of personal information**, including:
- Email addresses (publicContactEmail, scientificContactEmail)
- Personal names (publicContactLastname, publicContactFirstname, scientificContactLastname, scientificContactFirstname)
- Phone numbers (publicContactTel, scientificContactTel)
- Full contact addresses

### Available Datasets

- **ICTRP_only(CH=true)_12820x23.csv** - ICTRP trials conducted in Switzerland (12,820 trials)
- **BASEC_with_ICTRP_3054x64.csv** - Already-linked trials between BASEC and ICTRP (3,054 trials)
- **BASEC_without_ICTRP_1535x12.csv** - Unmatched BASEC trials to be linked (1,535 trials)

### Data Columns

**BASEC-specific columns:**
- basecId, snctpId, layTitle, laySummary, disease, intervention

**ICTRP-specific columns:**
- trialId, scientificTitle, publicTitle, interventions, healthConditions

**Common columns:**
- whoId (from ICTRP), various metadata fields

## FAISS Search Index

The repository includes a precomputed FAISS search index for efficient vector-based trial matching:

- **Coverage:** ~12,800 Swiss ICTRP trials
- **Embedding dimensions:** 768 (from multilingual MPNet model)
- **Search time:** <100ms per query on CPU

**Index embeds:** scientificTitle, publicTitle, interventions, healthConditions

**Usage:**
```python
import faiss
index = faiss.read_index('ICTRP_only_(CH=true)12820x23(sT,pT,int,hC).index')
```

## Key Results

| Metric | Value |
|--------|-------|
| **Linking Accuracy (k=1)** | 83.6% |
| **Linking Accuracy (k=3)** | 92.6% |
| **Optimal Confidence Threshold** | 0.69 |
| **Sensitivity at Threshold** | 0.89 |
| **False Positive Rate** | 0.14 |
| **Fine-tuned Model Accuracy** | 97.7% |

## Technologies & Methods

- **LinkTransformer** - Neural network-based record linking
- **FAISS** - Efficient similarity search with vector indexing
- **Sentence-Transformers** - Semantic embeddings from multilingual MPNet
- **Scikit-learn** - Model evaluation and metrics
- **Pandas** - Data manipulation and analysis
- **Matplotlib** - Visualization and ROC curve analysis

## Model Architecture

**Base Model:** sentence-transformers/paraphrase-multilingual-mpnet-base-v2
- Multilingual embeddings (supports 50+ languages)
- 768-dimensional output vectors
- Pretrained on semantic similarity tasks

**Fine-tuned on:**
- 3,054 linked BASEC-ICTRP trial pairs
- 4 matching fields per trial (title, disease/conditions, interventions)
- Information Retrieval task with triplet loss

## Performance Evaluation

The project uses several evaluation approaches:

1. **ROC Curve Analysis** - Determines optimal confidence threshold
2. **Bin-based Analysis** - Evaluates accuracy within confidence score ranges
3. **Manual Adjudication** - 100-trial validation set for ground truth
4. **Confusion Matrix** - True positives, false positives, sensitivity, specificity

## Project Workflow

```
Raw Data (BASEC + ICTRP)
        ↓
    [Notebook 1: Clean & Prepare]
        ↓
    [Notebook 2: Initial Linking with LinkTransformer]
        ↓
    [Notebook 3: Fine-tune Model (Optional)]
        ↓
    [Notebook 4: FAISS Vector Search]
        ↓
    [Notebook 5: Analyze Results]
        ↓
   Linked Trial Database
```

## Limitations & Future Work

### Current Limitations
- Matching is based on textual similarity only
- No integration with external identifiers (ClinicalTrials.gov, EudraCT, etc.)
- Confidence threshold optimization based on limited manual validation set

### Future Enhancements
- Incorporate structured metadata (phase, indication, sponsor)
- Multi-language support optimization
- Real-time matching API
- Integration with clinical trial registries worldwide
- Active learning for improving threshold selection

## License

This project includes publicly available clinical trial data from:
- **BASEC** - Swiss Ethics Committee database (public domain)
- **ICTRP** - WHO International Clinical Trials Registry Platform (public domain)

Please refer to respective registry licenses for data usage terms.

## Contact & Citation

**Author:** Matthias Rinderknecht  
**Email:** matthiasrinderknecht(at)gmx.ch
**University:** University of Bern, CAS Applied Data Science Program

If you use this project or methodology, please cite:
```
Rinderknecht, M. (2024). "The Missing Link: Automated Linking of Clinical Trials 
Between BASEC and ICTRP Using Machine Learning". CAS Applied Data Science Final Project, 
University of Bern.
```

## Acknowledgments

- University of Bern - CAS Applied Data Science Program
- LinkTransformer developers
- FAISS (Facebook AI Similarity Search) team
- Sentence-Transformers community
