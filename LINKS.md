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
https://huggingface.co/bangbeo46/my-r16-adapter

Without a personal HF token this submission should be graded as Option C. With the public adapter URL filled in, it qualifies for Option B bonus.

## Publish r16 Adapter

Current status: local adapter files are ready in `adapters/r16/`, but this machine is not logged in to HuggingFace Hub yet.

After logging in:

```powershell
huggingface-cli login
huggingface-cli upload <your-hf-username>/qwen2.5-3b-vi-lab21-r16 adapters/r16 . --repo-type model
```

Then replace the placeholder below with the real public model URL:

`https://huggingface.co/bangbeo46/my-r16-adapter`
