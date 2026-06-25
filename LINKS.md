# Lab 21 Links

**Student**: Nguyen Khanh Bang - 2A202600693

## GitHub

Repository: this submitted repository folder.

## HuggingFace Hub

HF adapter link is prepared in the notebook optional cell:

```python
PUSH_TO_HUB = True
HUB_REPO_ID = "<your-hf-username>/qwen2.5-3b-vi-lab21-r16"
```

After logging in with `huggingface_hub.login()`, run the final cell to publish the `r16` adapter and replace this line with the public URL:

## LoRA Adapter

r16 adapter:
https://huggingface.co/bangbeo46/my-r16-adapter/
lora adapter:
https://huggingface.co/bangbeo46/my-lora-adapter/

Without a personal HF token this submission should be graded as Option C. With the public adapter URL filled in, it qualifies for Option B bonus.

## Publish r16 Adapter

Current status: local adapter files and the filled model card README are ready in `adapters/r16/`, but this machine is not logged in to HuggingFace Hub yet.

After logging in:

```powershell
huggingface-cli login
huggingface-cli upload bangbeo46/my-lora-adapter adapters/r16 . --repo-type model
```

To update only the HuggingFace model card at `https://huggingface.co/bangbeo46/my-lora-adapter/edit/main/README.md`, run:

```powershell
huggingface-cli upload bangbeo46/my-lora-adapter adapters/r16/README.md README.md --repo-type model
```

Public model URL:
`https://huggingface.co/bangbeo46/my-r16-adapter/`
`https://huggingface.co/bangbeo46/my-lora-adapter`
