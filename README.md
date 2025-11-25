# RLHF Tuning & Evaluation on Google Cloud Vertex AI

This project implements a complete workflow for fine-tuning a Large Language Model (specifically Llama-2-7b) using Reinforcement Learning from Human Feedback (RLHF). The workflow is designed to run on Google Cloud Vertex AI Pipelines. It compiles a training pipeline, calculates optimal training steps based on dataset size, submits the job to the cloud, and provides tools to evaluate the performance of the tuned model against the base model.

## 🚀 Features

- **Pipeline Compilation**: Compiles a KubeFlow Pipeline (KFP) for RLHF.
- **Dynamic Step Calculation**: Automatically calculates training steps for both the Reward Model and the Reinforcement Learning loop based on dataset size and batch size.
- **Vertex AI Integration**: Submits and manages training jobs directly on Google Cloud infrastructure.
- **Evaluation Tools**:
  - Integrated TensorBoard support for monitoring training logs (Reward & Reinforcement).
  - Pandas integration to compare "Tuned" vs "Untuned" model outputs side-by-side.

## 🛠️ Prerequisites

To run this project successfully, you need:

### Google Cloud Platform (GCP) Project
- Vertex AI API enabled.
- A Google Cloud Storage (GCS) bucket for staging artifacts.

### Python Environment
Install the following libraries:
- `google-cloud-pipeline-components`
- `google-cloud-aiplatform`
- `kfp` (KubeFlow Pipelines)
- `pandas`
- `tensorboard`

### Installation

```bash
pip install google-cloud-pipeline-components kfp google-cloud-aiplatform tensorboard pandas
```

## 📂 Project Structure

The project relies on a single consolidated Jupyter Notebook:
- `RLHF_Tune_and_Evaluate.ipynb`: The main executable file containing the logic for compilation, training configuration, and evaluation.

## ⚙️ Configuration & Hyperparameters

The pipeline is configured to perform a Summarization Task. Key parameters include:

| Parameter | Value | Description |
|-----------|-------|-------------|
| Model | `llama-2-7b` | The foundation model being tuned. |
| Instruction | "Summarize in less than 50 words" | The specific task constraint. |
| KL Coefficient | `0.1` | Penalty to prevent the model from diverging too far from the base model (prevents reward hacking). |
| Batch Size | `64` | Fixed batch size used for step calculations. |
| Preference Dataset Size | `3000` | Number of preference pairs for reward model training. |
| Prompt Dataset Size | `2000` | Number of prompts for reinforcement learning training. |

## 📖 Usage Guide

### 1. Compile the Pipeline
The notebook first compiles the RLHF pipeline into a YAML file (`rlhf_pipeline.yaml`).

### 2. Calculate Steps
Based on the defined `PREF_DATASET_SIZE` (3000) and `PROMPT_DATASET_SIZE` (2000), the notebook calculates the exact number of steps required for:
- Reward Model Training
- Reinforcement Learning Training

### 3. Submit Job
The pipeline is submitted to Vertex AI using `aiplatform.PipelineJob`.

**Note**: Actual training on Vertex AI takes significant time and compute resources (TPUs/GPUs).

### 4. Evaluate Results
- **TensorBoard**: Load logs from `reward-logs` and `reinforcer-logs` to visualize training progress.
- **Comparison**: The notebook loads pre-computed JSONL results (`eval_results_tuned.jsonl` and `eval_results_untuned.jsonl`) into a Pandas DataFrame to qualitatively compare how the tuned model performs against the base model.

## 📊 Sample Data

The project uses sample datasets stored in public Google Cloud Storage buckets:
- **Preference Dataset**: `summarize_from_feedback_tfds`
- **Prompt/Eval Dataset**: `reddit_tfds`

## ⚠️ Important Notes

- This notebook is configured to run on Google Cloud Vertex AI. Running the `job.run()` command will incur costs on your Google Cloud project.
- Ensure you have appropriate quotas and billing enabled before submitting training jobs.
- Training typically requires several hours depending on dataset size and compute resources allocated.

## 🔍 Monitoring & Troubleshooting

- Monitor pipeline execution through the Vertex AI Pipelines console in Google Cloud.
- Use TensorBoard integration to track training metrics in real-time.
- Check GCS bucket for generated artifacts and logs.

## 📝 License

Please refer to your organization's license for usage terms.

## 🤝 Contributing

For contributions or issues, please follow your organization's contribution guidelines.
