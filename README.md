# LLM Fine-tuning for Text-to-SQL Generation

Fine-tuning CodeT5 and FLAN-T5 models to convert natural language questions into SQL queries with explanations.

## Quick Results

| Model | Zero-shot | Fine-tuned | Improvement |
|-------|-----------|------------|-------------|
| **CodeT5-Small** | 8.2% | **54.9%** | +44.7% |
| **FLAN-T5-Small** | 9.1% | 38.0% | +28.9% |

**Key Finding**: Code-specialized models (CodeT5) significantly outperform general language models for SQL generation tasks.

## Quick Start

```bash
pip install transformers datasets torch evaluate sqlparse
python llm_finetuning.py
```

## Project Overview

**Objective**: Fine-tune language models for natural language to SQL conversion using Gretel's synthetic dataset

### Why Text-to-SQL?
- **Democratizes data access** for non-technical users
- **Reduces development time** for analytics and reporting  
- **Improves accuracy** vs manual SQL writing

## Implementation

### Dataset
- **Source**: Gretel synthetic text-to-SQL (100k examples, 85+ domains)
- **Filtered**: Financial services, healthcare, finance, insurance (4,318 examples)
- **Split**: 70/10/20 train/validation/test

### Models Tested
- **CodeT5-Small**: Pre-trained on code, specialized for programming tasks
- **FLAN-T5-Small**: Instruction-tuned general language model

### Enhanced Prompt Strategy
```
Convert this natural language question to SQL using the given database schema.

Database Schema: [SCHEMA]
Question: [QUESTION]  
Please generate a SQL query with explanation:
```

## Evaluation Framework

**Multi-dimensional scoring system:**

| Metric | Weight | Purpose |
|--------|---------|---------|
| Syntax Correctness | 20% | Can SQL be parsed? |
| Structural Similarity | 40% | Do components match reference? |
| Exact Match | 10% | Perfect query match |
| Explanation BLEU | 30% | Quality of natural language explanation |

**Combined Score = 0.2×Syntax + 0.4×Structural + 0.1×Exact + 0.3×BLEU**

## Key Results

### Performance Comparison
- **CodeT5 Zero-shot**: 0.5% syntax, 20.3% structural, 8.2% combined
- **CodeT5 Fine-tuned**: 99.3% syntax, 63.8% structural, 54.9% combined
- **FLAN-T5 Zero-shot**: 4.9% syntax, 20.3% structural, 9.1% combined  
- **FLAN-T5 Fine-tuned**: 88.9% syntax, 34.9% structural, 38.0% combined

### Training Efficiency
- **Loss Reduction**: CodeT5 (58%), FLAN-T5 (95%)
- **Training Time**: ~7-8 minutes per model on T4 GPU
- **Convergence**: Both models showed consistent improvement

## Sample Predictions

### Question: "Find all employees in Engineering department"

| Model | Output Quality |
|-------|---------------|
| **CodeT5 Zero-shot** | Repetitive tokens (failed) |
| **CodeT5 Fine-tuned** | `SELECT name, salary FROM employees WHERE department = 'Engineering';` ✅ |
| **FLAN-T5 Zero-shot** | Echoes input question (failed) |
| **FLAN-T5 Fine-tuned** | `SELECT emp_id FROM employees WHERE department = 'Engineering';` (partial) |

## Technical Implementation

### Training Configuration
- **Epochs**: 3 
- **Batch Size**: 4 with gradient accumulation
- **Optimizer**: AdamW with warmup
- **Hardware**: T4 GPU with mixed precision (FP16)

### Features Implemented
- **Gradio Interface**: Interactive web demo for testing models
- **Custom Evaluation**: Multi-metric scoring beyond simple BLEU
- **Domain Filtering**: Focus on business-critical sectors
- **Structured Output**: SQL + explanation generation



## Key Insights

1. **Domain Specialization Matters**: CodeT5's code pre-training provides substantial advantage
2. **Fine-tuning Essential**: Zero-shot performance inadequate for production use
3. **Structural Understanding**: Code models better capture SQL query structure
4. **Scaling Strategy**: Start with smaller models, scale up based on results

## Interactive Demo

The project includes a Gradio web interface allowing users to:
- Input database schemas and natural language questions
- Select between fine-tuned CodeT5 and FLAN-T5 models
- Get SQL queries with explanations
- Test with pre-built examples

---

**Conclusion**: Code-specialized models with domain-focused fine-tuning achieve production-ready text-to-SQL performance, with CodeT5 showing 44.7% improvement over baseline.
