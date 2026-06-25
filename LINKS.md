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

`https://huggingface.co/<your-hf-username>/qwen2.5-3b-vi-lab21-r16`

Without a personal HF token this submission should be graded as Option C. With the public adapter URL filled in, it qualifies for Option B bonus.
