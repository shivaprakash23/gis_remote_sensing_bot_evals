# GIS & Remote Sensing Q&A Dataset

This folder contains a growing collection of **chat-style instruction / response pairs** intended for fine-tuning and evaluating large language models (LLMs) in the GIS and Remote-Sensing domain.

## File structure

| File | Description |
|------|-------------|
| `gis_remote_sensing.jsonl` | The dataset itself, one JSON object per line. |
| `README.md` | (this file) Dataset description, format specification and contribution guidelines. |

## JSONL format

Each line follows the chat schema used by Qwen, LLaMA-2-Chat and other open-source assistant models:

```jsonc
{"messages": [
  {"role": "system",   "content": "You are a GIS expert."},
  {"role": "user",     "content": "What is NDVI and why is it useful?"},
  {"role": "assistant","content": "NDVI (Normalized Difference Vegetation Index) …"}
]}
```

* **`system`** – Defines the assistant persona (keep it short; domain-specific instructions welcome).
* **`user`** – The question or instruction.
* **`assistant`** – The ground-truth answer you expect the model to produce.

Multi-turn dialogues are supported by simply appending additional `user` / `assistant` pairs inside the same `messages` array.

## Usage examples

### Fine-tuning (QLoRA example)
After preparing the dataset you can load it with 🤗 `datasets` and fine-tune Qwen-1.8B with LoRA:

```python
from datasets import load_dataset
from transformers import AutoTokenizer

ds = load_dataset("json", data_files="data/gis_remote_sensing.jsonl")

tok = AutoTokenizer.from_pretrained("Qwen/Qwen1.5-1.8B", trust_remote_code=True)
print(tok.apply_chat_template(ds["train"][0]["messages"], tokenize=False))
```

Full training recipe: see `../scripts/finetune_qwen_lora.py` (example provided in the chat).

### Evaluation
Use the same chat template during evaluation/inference to measure exact-match, Rouge, etc., between generated answers and the `assistant` field.

## Contributing
1. Add new Q&A lines to **`gis_remote_sensing.jsonl`** – **do not** pretty-print or span multiple lines.
2. Ensure factual accuracy and domain relevance.
3. Validate JSON (no trailing commas).
4. Open a pull request / share your file for review.

## License
Yet to be decided
