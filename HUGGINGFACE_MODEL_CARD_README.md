---
base_model: unsloth/Qwen2.5-3B-bnb-4bit
library_name: peft
pipeline_tag: text-generation
tags:
- base_model:adapter:unsloth/Qwen2.5-3B-bnb-4bit
- lora
- qlora
- sft
- transformers
- trl
- peft
- unsloth
- vietnamese
- instruction-tuning
datasets:
- 5CD-AI/Vietnamese-alpaca-gpt4-gg-translated
language:
- vi
- en
license: mit
---

# my-lora-adapter

`my-lora-adapter` is a LoRA adapter fine-tuned from `unsloth/Qwen2.5-3B-bnb-4bit` for Vietnamese instruction-following experiments in Lab 21. It was trained with QLoRA-style 4-bit base model loading and supervised fine-tuning on 200 Vietnamese Alpaca-format examples.

This repository contains the r=16 adapter, which is the recommended rank from the lab report because it gives the best trade-off between quality, parameter count, and deployment cost.

## Model Details

### Model Description

- **Developed by:** Nguyen Khanh Bang - 2A202600693
- **Funded by:** Self-funded educational lab work
- **Shared by:** Nguyen Khanh Bang
- **Model type:** PEFT LoRA adapter for causal language modeling
- **Language(s):** Vietnamese primarily, with some English capability inherited from the base model
- **License:** MIT for this adapter repository, subject to the base model and dataset licenses
- **Finetuned from model:** `unsloth/Qwen2.5-3B-bnb-4bit`
- **Base architecture:** Qwen2.5 3B causal language model
- **Adapter method:** LoRA, rank 16, alpha 32

### Model Sources

- **Repository:** Lab 21 submission repository
- **Base model:** https://huggingface.co/unsloth/Qwen2.5-3B-bnb-4bit
- **Dataset:** https://huggingface.co/datasets/5CD-AI/Vietnamese-alpaca-gpt4-gg-translated
- **Paper references:** LoRA, QLoRA, and supervised fine-tuning references listed in the lab report

## Uses

### Direct Use

This adapter is intended for educational Vietnamese instruction-following demos. It can be loaded on top of `unsloth/Qwen2.5-3B-bnb-4bit` and used for short-form responses to general Vietnamese prompts.

### Downstream Use

The adapter can be used as a starting point for:

- Comparing LoRA ranks in a classroom fine-tuning lab
- Demonstrating PEFT adapter loading and saving
- Running small qualitative evaluations of Vietnamese instruction-following behavior
- Reproducing the Lab 21 r=16 baseline experiment

### Out-of-Scope Use

This adapter should not be used for:

- Medical, legal, financial, or other high-stakes advice
- Safety-critical decision making
- Production deployment without additional evaluation
- Claims of factual reliability
- Private or sensitive data processing without a separate privacy review

## Bias, Risks, and Limitations

The adapter was trained on only 200 samples, so it should be treated as a small lab artifact rather than a robust production model. It may hallucinate, make factual mistakes, produce awkward Vietnamese phrasing, or inherit biases from the base model and training data.

The qualitative evaluation found one clear failure case: the r=16 adapter incorrectly expanded "LoRA" while explaining LoRA vs QLoRA. This means factual definitions should be verified, especially for technical topics.

### Recommendations

Use this model only for experimentation and coursework. For real applications, run a larger evaluation set, add domain-specific tests, compare against the base model, and use retrieval or human review when factual accuracy matters.

## How to Get Started with the Model

```python
from peft import PeftModel
from transformers import AutoTokenizer
from unsloth import FastLanguageModel

base_model = "unsloth/Qwen2.5-3B-bnb-4bit"
adapter_id = "your-hf-username/qwen2.5-3b-vi-lab21-r16"  # or local path: "adapters/r16"

model, tokenizer = FastLanguageModel.from_pretrained(
    model_name=base_model,
    max_seq_length=1024,
    dtype=None,
    load_in_4bit=True,
)

model = PeftModel.from_pretrained(model, adapter_id)
FastLanguageModel.for_inference(model)

prompt = """### Instruction:
Giải thích sự khác nhau giữa prompt engineering, RAG và fine-tuning.

### Response:
"""

inputs = tokenizer(prompt, return_tensors="pt").to("cuda")
outputs = model.generate(
    **inputs,
    max_new_tokens=200,
    temperature=0.7,
    top_p=0.9,
    do_sample=True,
    pad_token_id=tokenizer.eos_token_id,
)

print(tokenizer.decode(outputs[0], skip_special_tokens=True))
```

## Training Details

### Training Data

- **Dataset:** `5CD-AI/Vietnamese-alpaca-gpt4-gg-translated`
- **Samples used:** 200
- **Train/eval split:** 180 train / 20 eval
- **Split seed:** 42
- **Fields used:** `instruction_vi`, `input_vi`, `output_vi`
- **Format:** Alpaca-style prompt text

### Training Procedure

The base model was loaded in 4-bit mode with Unsloth, then a LoRA adapter was trained using TRL `SFTTrainer`.

#### Preprocessing

- Empty input fields were handled with a no-input Alpaca template.
- Token lengths were measured before training.
- p50 token length: 227
- p95 token length: 562
- p99 token length: 704
- `max_seq_length`: 1024, selected by rounding up p95 and capping for T4 memory.

#### Training Hyperparameters

- **Training regime:** fp16 mixed precision on Tesla T4
- **Epochs:** 3
- **Learning rate:** 2e-4
- **LR schedule:** cosine
- **Warmup ratio:** 0.10
- **Per-device train batch size:** 1
- **Gradient accumulation steps:** 8
- **Effective batch size:** 8
- **Optimizer:** `adamw_8bit`
- **Weight decay:** 0.01
- **LoRA rank:** 16
- **LoRA alpha:** 32
- **LoRA dropout:** 0
- **Target modules:** `q_proj`, `v_proj`
- **Gradient checkpointing:** enabled with Unsloth
- **Packing:** disabled
- **Seed:** 42

#### Speeds, Sizes, Times

- **Trainable parameters:** 3,686,400
- **Adapter file size:** about 14.8 MB for `adapter_model.safetensors`
- **Training time:** 4.26 minutes for r=16
- **Peak VRAM:** 6.62 GB for r=16
- **Total rank experiment time:** about 12.2 minutes for r=8, r=16, and r=64
- **Estimated training cost:** about $0.07 at $0.35/hour

## Evaluation

### Testing Data, Factors & Metrics

#### Testing Data

The evaluation set is the 10 percent held-out split from the same 200-sample dataset, giving 20 examples.

#### Factors

The lab compared LoRA rank values while keeping the base model, dataset, optimizer, learning rate, epochs, batch size, and evaluation method fixed:

- r=8, alpha=16
- r=16, alpha=32
- r=64, alpha=128

#### Metrics

- **Eval loss:** computed on the held-out evaluation set.
- **Perplexity:** `exp(eval_loss)`.
- **Peak VRAM:** `torch.cuda.max_memory_allocated()`.
- **Trainable parameters:** number of parameters with `requires_grad=True`.
- **Qualitative examples:** five prompts comparing base model vs r=16 adapter.

### Results

| Rank | Alpha | Trainable Params | Train Time | Peak VRAM | Eval Loss | Perplexity |
|---:|---:|---:|---:|---:|---:|---:|
| 8 | 16 | 1,843,200 | 4.00 min | 7.22 GB | 1.5577 | 4.7479 |
| 16 | 32 | 3,686,400 | 4.26 min | 6.62 GB | 1.5161 | 4.5544 |
| 64 | 128 | 14,745,600 | 3.99 min | 8.00 GB | 1.4768 | 4.3790 |

#### Summary

r=64 achieved the lowest perplexity, but r=16 was selected as the best practical adapter because it captures most of the improvement with far fewer trainable parameters. The jump from r=8 to r=16 gives a meaningful perplexity improvement, while the jump from r=16 to r=64 shows diminishing returns.

## Model Examination

Qualitative examples showed that the r=16 adapter often gives clearer Vietnamese explanations and better alignment with the Day 21 fine-tuning lesson. However, it can still make factual errors, including an incorrect expansion of "LoRA" in one technical prompt.

## Environmental Impact

Carbon emissions were not directly measured.

- **Hardware Type:** NVIDIA Tesla T4
- **Hours used:** about 0.203 hours for the full rank experiment
- **Cloud Provider:** Google Colab-style GPU environment
- **Compute Region:** Unknown
- **Carbon Emitted:** Not measured

## Technical Specifications

### Model Architecture and Objective

The base model is a Qwen2.5 3B causal language model. This repository contains a LoRA adapter trained with supervised fine-tuning for next-token prediction over Alpaca-formatted instruction examples.

### Compute Infrastructure

#### Hardware

- NVIDIA Tesla T4
- 14.563 GB reported VRAM

#### Software

- Python 3.12 in Colab
- Unsloth 2026.5.2
- Transformers 5.5.0 in the recorded run
- TRL 0.15.2
- PEFT 0.19.1
- PyTorch 2.10.0+cu128
- CUDA Toolkit 12.8

## Citation

**BibTeX:**

```bibtex
@misc{hu2021lora,
  title={LoRA: Low-Rank Adaptation of Large Language Models},
  author={Hu, Edward J. and Shen, Yelong and Wallis, Phillip and Allen-Zhu, Zeyuan and Li, Yuanzhi and Wang, Shean and Wang, Lu and Chen, Weizhu},
  year={2021},
  eprint={2106.09685},
  archivePrefix={arXiv},
  primaryClass={cs.CL}
}

@misc{dettmers2023qlora,
  title={QLoRA: Efficient Finetuning of Quantized LLMs},
  author={Dettmers, Tim and Pagnoni, Artidoro and Holtzman, Ari and Zettlemoyer, Luke},
  year={2023},
  eprint={2305.14314},
  archivePrefix={arXiv},
  primaryClass={cs.LG}
}
```

**APA:**

Hu et al. (2021). LoRA: Low-Rank Adaptation of Large Language Models. arXiv:2106.09685.

Dettmers et al. (2023). QLoRA: Efficient Finetuning of Quantized LLMs. arXiv:2305.14314.

## Glossary

- **LoRA:** Low-Rank Adaptation, a parameter-efficient fine-tuning method.
- **QLoRA:** LoRA training with a quantized base model, commonly using 4-bit loading to reduce memory.
- **Perplexity:** Exponential of evaluation loss; lower is better for language modeling.
- **Adapter:** Small trainable weight update loaded on top of a frozen base model.

## More Information

See the Lab 21 report and result files in the submission repository:

- `REPORT.md`
- `results/rank_experiment_summary.csv`
- `results/qualitative_comparison.csv`

## Model Card Authors

Nguyen Khanh Bang - 2A202600693

## Model Card Contact

Nguyen Khanh Bang

### Framework versions

- PEFT 0.19.1
