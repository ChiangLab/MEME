# Multimodal Clinical Pseudo-notes for Emergency Department Prediction Tasks using Multiple Embedding Model for EHR (MEME)

![GitHub](https://img.shields.io/github/license/Simonlee711/MEME) [![arXiv](https://img.shields.io/badge/arXiv-2402.00160-brightgreen.svg)](https://arxiv.org/abs/2402.00160) [![PWC](https://img.shields.io/endpoint.svg?url=https://paperswithcode.com/badge/multimodal-clinical-pseudo-notes-for)](https://paperswithcode.com/paper/multimodal-clinical-pseudo-notes-for)

# Abstract

In this work, we introduce Multiple Embedding Model for EHR (MEME), an approach that views Electronic Health Records (EHR) as multimodal data. It uniquely represents tabular concepts like diagnoses and medications as structured natural language text using our "pseudo-notes" method. This approach allows us to effectively employ Large Language Models (LLMs) for individual EHR representation, proving beneficial in a variety of text-classification tasks. We demonstrate the effectiveness of MEME by applying it to diverse tasks within the Emergency Department across multiple hospital systems. Our findings show that MEME surpasses the performance of both single modality/embedding methods and traditional machine learning approaches, highlighting its effectiveness. Additionally, our tests on the model's generalizability reveal that training solely on the MIMIC-IV database does not guarantee effective application across different hospital institutions.

# Motivation

![Model Image](https://github.com/Simonlee711/MEME/blob/main/img/model.png "Model Architecture")

Electronic Health Records are heterogenous containing a mixture of numerical, categorical, free text data. However traditional machine learning models struggle to learn representations of categorical data due to traditional one hot encoding schemes that result in sparse matrices. Therefore in this work we present **pseudo-notes**, which is a data transformation from EHR tabular data to text that allows us to leverage recent Large Language Models that have better understanding of English and context. These in turn generate better representations of EHR data as demonstrated by this research which benchmarks text vs tabular input as well as multimodal vs singular modality input. 

**TLDR** Multiple Embedding Model for EHR (MEME) outperforms all benchmark models on various tasks recorded in the Emergency Department. A further description is referenced in the paper.

### Modality Specific Pseudo-notes

| Modality  | Sentence |
|-----------|----------|
| arrival   | Patient 10000032, a 52 year old white female... |
| triage    | At triage: temperature was 98.4, pulse was 70.4... |
| medrecon  | The patient was previously taking the following medication: ibuprofen, nsaid analgesics (cox non-specific) - propionic acid derivatives... |
| vitals    | The patient had the following vitals: pulse was 128... |
| codes     | The patient received the following diagnostic codes: [0389], septicemia nos... |
| pyxis     | The patient received the following medications: At 2125-03-19 13:05:00, lorazepam, lorazepam were administered... |
---

# Data
The [MIMIC-IV-ED dataset](https://physionet.org/content/mimic-iv-ed/2.2/), part of the extensive MIMIC-IV collection, concentrates on emergency department records from a major hospital. It anonymizes and details patient demographics, triage, vitals, tests, medications, and outcomes, aiding research in emergency care and hospital operations. Access follows strict privacy regulations.

For UCLA Authorized Mednet users, institutional data access is granted. However, due to privacy constraints, UCLA EHR Data distribution is restricted outside these parameters.

# Installation and Setup

To replicate our results, install the MedBERT model from *Hugging Face*. We recommend using PyTorch 1.13. Ensure you use the specified seed for consistent train, validation, and test splits with our provided split function.

```python
from transformers import AutoTokenizer, AutoModel

# Load MedBERT from Hugging Face
tokenizer = AutoTokenizer.from_pretrained("Charangan/MedBERT")
model = AutoModel.from_pretrained("Charangan/MedBERT")
...

# Split your dataset
train, validate, test = train_validate_test_split(data, seed=7)
```

More details on MedBERT can be found on its [Hugging Face page](https://huggingface.co/Charangan/MedBERT).

# Training Configuration and Execution

For training, ensure you have GPU access. Adjust the batch size based on your GPU memory; we used a batch size of 64. Note that changes in batch size might slightly affect the results. Set up your training environment by creating a `.json` configuration file and a `logs/` directory. Here's a sample structure for your configuration file:

```json
{
    "log_dir": "logs",
    "experiment_name": "Your_Experiment_Name",
    "batch": "Your_Batch_Size",
    "num_workers": 8,  // Enhances tokenization speed
    "data": "Path_to_Your_Data_File",
    "model": "./Charangan/MedBERT",  // Refer to the Installation section for model setup
    "mode": "multimodal",
    "gpu": "Your_GPU_ID",
    "task": "Your_Task",  // 'eddispo' or 'multitask'
    "epoch": "Your_Num_Epochs",  // Early stopping is implemented
    "dataset": "MIMIC"
}
```

To start the training, initiate a screen session and execute the following command:

```
python3 trainer.py -c train_config.json
```

Ensure you replace placeholder values with actual data specific to your setup.

# Inference Configuration and Execution

For running inference, you will need to set up a `.json` configuration file similar to the training setup but with additional fields specific to inference. Here's a sample structure:

```json
{
    "log_dir": "logs",
    "experiment_name": "Your_Experiment_Name",
    "batch": "Your_Batch_Size",
    "num_workers": 8,
    "data": "Path_to_Your_Data_File",
    "model": "./Charangan/MedBERT",
    "weights": "Path_to_Your_Model_Weights",
    "mode": "multimodal",
    "gpu": "Your_GPU_ID",
    "task": "Your_Task",
    "dataset": "MIMIC",
    "validation": "Validation_Mode"  // 'within' or 'across'
}
```

To initiate the inference process, start a screen session and run the following command:

```
python3 inference.py -c inference_config.json
```

Make sure to replace placeholders with your actual data.

# Citing
```
@misc{lee2024multimodal,
      title={Multimodal Clinical Pseudo-notes for Emergency Department Prediction Tasks using Multiple Embedding Model for EHR (MEME)}, 
      author={Simon A. Lee and Sujay Jain and Alex Chen and Arabdha Biswas and Jennifer Fang and Akos Rudas and Jeffrey N. Chiang},
      year={2024},
      eprint={2402.00160},
      archivePrefix={arXiv},
      primaryClass={cs.CL}
}
```
