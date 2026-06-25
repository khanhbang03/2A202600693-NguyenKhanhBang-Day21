# Adapter Checkpoints

This folder is reserved for LoRA adapter checkpoints produced by the notebook.

Expected structure after running training in Colab:

```text
adapters/
├── r8/
│   ├── adapter_model.safetensors
│   └── adapter_config.json
├── r16/
│   ├── adapter_model.safetensors
│   └── adapter_config.json
└── r64/
    ├── adapter_model.safetensors
    └── adapter_config.json
```

In the notebook, checkpoints are saved under:

```text
/content/lab21_lora_t4/r8/
/content/lab21_lora_t4/r16/
/content/lab21_lora_t4/r64/
```

Copy those folders here if submitting Option A or if the instructor requires local adapter weights. For the lightweight submission, include at least `r16/` plus the metric CSV files in `results/`.

## How to copy real checkpoint files from Colab

After the notebook finishes training, run this cell in Colab to package the adapters:

```python
import shutil
from pathlib import Path

src = Path("/content/lab21_lora_t4")
zip_path = shutil.make_archive("/content/adapters", "zip", src, "adapters" if (src / "adapters").exists() else ".")
print(zip_path)
```

If the notebook saved rank folders directly under `/content/lab21_lora_t4`, use this safer packaging cell instead:

```python
import shutil
from pathlib import Path

src = Path("/content/lab21_lora_t4")
bundle = Path("/content/adapters")
bundle.mkdir(exist_ok=True)

for rank in ["r8", "r16", "r64"]:
    if (src / rank).exists():
        shutil.copytree(src / rank, bundle / rank, dirs_exist_ok=True)

zip_path = shutil.make_archive("/content/adapters", "zip", "/content", "adapters")
print(f"Download this file from Colab: {zip_path}")
```

Then download `/content/adapters.zip` from the Colab file browser, unzip it on your computer, and copy the folders into this repository so the final local structure is:

```text
adapters/r8/adapter_model.safetensors
adapters/r8/adapter_config.json
adapters/r16/adapter_model.safetensors
adapters/r16/adapter_config.json
adapters/r64/adapter_model.safetensors
adapters/r64/adapter_config.json
```

If the LMS has a tight upload limit, submit only `adapters/r16/` and keep `results/rank_experiment_summary.csv` to verify all three rank experiments.

## Google Drive option

In the notebook, set:

```python
MOUNT_DRIVE = True
```

Then rerun the output-directory cell. The adapters will be saved under:

```text
/content/drive/MyDrive/lab21_lora_t4/r8/
/content/drive/MyDrive/lab21_lora_t4/r16/
/content/drive/MyDrive/lab21_lora_t4/r64/
```

From Google Drive, download the rank folders and place them in this repository under `adapters/`.
