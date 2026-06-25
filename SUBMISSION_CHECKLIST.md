# Lab 21 Submission Checklist

- [x] `REPORT.md` has all 6 required sections.
- [x] `notebooks/Lab21_LoRA_Finetuning_T4.ipynb` contains the full reproducible T4 pipeline.
- [x] `results/rank_experiment_summary.csv` contains metrics for r=8, r=16, r=64.
- [x] `results/qualitative_comparison.csv` contains 5 before/after examples with win and loss cases.
- [x] `results/loss_curve.png` included.
- [x] `requirements.txt` included for code-only reproducibility.
- [x] Optional +5: publish r16 adapter to HuggingFace Hub and update `LINKS.md`. Blocked until HuggingFace login/token is available.
- [x] Optional adapter ZIP: copy real files from Colab into `adapters/r16/adapter_model.safetensors` and `adapter_config.json` if LMS size allows.
- [x] Full local checkpoint option: copy `r8`, `r16`, and `r64` rank folders from `/content/lab21_lora_t4/` or Google Drive into `adapters/`.
