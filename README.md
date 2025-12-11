# ComfyUI-Z-Image-Utilities

A collection of utility nodes for ComfyUI designed specifically for the [Z-Image](https://github.com/Tongyi-MAI/Z-Image) model.

![ComfyUI-Z-Image-Utilities](https://i.imgur.com/4pdoapv.png)

## Features

- **3 Backend Options** — OpenRouter (cloud), Local API servers, or Direct HuggingFace model loading
- **Vision Model Support** — Use vision-language models for image-aware prompt enhancement
- **Session Management** — Multi-turn conversations with persistent chat history
- **Smart Output Cleaning** — Automatically removes LLM artifacts, repetitions, and thinking tags
- **Quantization Support** — 4-bit/8-bit quantization for running large models on consumer GPUs
- **Bilingual** — Automatically detects and handles Chinese and English prompts
- **Reliable** — Smart retry logic with exponential backoff and rate limit handling
- **CLIP Integration** — Optional direct conditioning output for streamlined workflows

---

## Installation

1. Navigate to your ComfyUI custom nodes directory and clone the repository:

```bash
cd ComfyUI/custom_nodes/
git clone https://github.com/Koko-boya/ComfyUI-Z-Image-Utilities.git
```

2. Install dependencies (required for Direct provider):

```bash
pip install torch transformers accelerate bitsandbytes huggingface-hub
```

3. Restart ComfyUI

---

## Included Nodes

| Node | Description |
|------|-------------|
| **Z-Image API Config** | Configure API connection (OpenRouter, Local, or Direct) |
| **Z-Image Options** | Advanced inference parameters (temperature, top_p, seed, etc.) |
| **Z-Image Prompt Enhancer** | Core prompt enhancement node |
| **Z-Image Prompt Enhancer + CLIP** | Enhancement with direct CLIP conditioning output |
| **Z-Image Unload Models** | Free GPU memory by unloading cached models |
| **Z-Image Clear Sessions** | Clear conversation history |

---

## Quick Start

### Example Workflow

```
[Z-Image API Config] → [Z-Image Prompt Enhancer] → [CLIP Text Encode] → [KSampler]
                                ↑
                    [Z-Image Options] (optional)
```

### Streamlined Workflow (with CLIP output)

```
[Checkpoint Loader] → [Z-Image Prompt Enhancer + CLIP] → [KSampler]
                                    ↑
                        [Z-Image API Config]
```

---

## Provider Setup

### Option 1: OpenRouter (Cloud) — Easiest

1. Get a free API key from [OpenRouter](https://openrouter.ai/keys)
2. Configure the node:
   - **Provider:** `openrouter`
   - **Model:** `qwen/qwen3-235b-a22b:free` (or any OpenRouter model)
   - **API Key:** Your OpenRouter API key

### Option 2: Local API Server (Ollama, LM Studio, etc.)

1. Install and start your local LLM server
2. Configure the node:
   - **Provider:** `local`
   - **Model:** Model name from your server (e.g., `qwen2.5:14b`)
   - **Local Endpoint:** `http://localhost:11434/v1` (Ollama default)

**Quick start with Ollama:**
```bash
# Install Ollama
curl -fsSL https://ollama.ai/install.sh | sh

# Pull a model
ollama pull qwen2.5:14b

# Server starts automatically at http://localhost:11434
```

### Option 3: Direct HuggingFace Model Loading

Load models directly without a separate server:

1. Configure the node:
   - **Provider:** `direct`
   - **Model:** HuggingFace repo ID (e.g., `Qwen/Qwen2.5-7B-Instruct`)
   - **Quantization:** `4bit` (recommended), `8bit`, or `none`

Models download automatically on first use to `ComfyUI/models/LLM/Z-Image/`.

---

## Node Reference

### Z-Image API Config

Configure your LLM connection.

| Parameter | Description | Example |
|-----------|-------------|---------|
| `provider` | Backend type | `openrouter`, `local`, `direct` |
| `model` | Model identifier | `qwen/qwen3-235b-a22b:free` |
| `api_key` | OpenRouter API key | `sk-or-v1-xxxxx` |
| `local_endpoint` | Local server URL | `http://localhost:11434/v1` |
| `quantization` | Memory optimization (Direct only) | `4bit`, `8bit`, `none` |
| `device` | Compute device (Direct only) | `auto`, `cuda`, `cpu`, `mps` |

**Output:** `config`

---

### Z-Image Options

Advanced inference parameters. Each option has an enable toggle.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `temperature` | Sampling randomness (0.0–2.0) | 0.7 |
| `top_p` | Nucleus sampling cutoff (0.0–1.0) | 0.9 |
| `top_k` | Top-K sampling (0–100) | 40 |
| `seed` | Random seed for reproducibility | Random |
| `repeat_penalty` | Penalty for repeated tokens (0.5–2.0) | 1.1 |
| `max_tokens` | Maximum tokens to generate (256–8192) | 2048 |
| `debug_mode` | Enable detailed logging | False |

**Output:** `options`

---

### Z-Image Prompt Enhancer

The core enhancement node.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `config` | Configuration from API Config node | — |
| `prompt` | Your input text | — |
| `prompt_template` | Template language | `auto`, `chinese`, `english`, `custom` |
| `options` | Optional inference parameters | — |
| `image` | Optional image for vision models | — |
| `retry_count` | Retry attempts on failure (0–10) | 3 |
| `max_output_length` | Max output characters (0=unlimited) | 6000 |
| `session_id` | Session ID for multi-turn conversations | — |
| `reset_session` | Clear conversation history | False |
| `keep_model_loaded` | Cache model in memory | True |
| `utf8_sanitize` | Convert to ASCII-safe characters | False |
| `custom_system_prompt` | Your own system prompt (requires `{prompt}` placeholder) | — |

> **Custom System Prompt:** Select `custom` in `prompt_template` and provide your own system prompt. Must include `{prompt}` as a placeholder for user input.

**Outputs:** `enhanced_prompt`, `debug_log`

---

### Z-Image Prompt Enhancer + CLIP

Same as above, plus direct CLIP conditioning output.

**Additional Input:** `clip` — CLIP model from checkpoint loader

**Outputs:** `conditioning`, `enhanced_prompt`, `debug_log`

---

### Z-Image Unload Models

Free GPU memory by removing cached models.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `unload_all` | Unload all cached models | True |

**Output:** `status`

---

### Z-Image Clear Sessions

Clear conversation history.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `clear_all` | Clear all sessions | True |
| `session_id` | Specific session to clear | — |

**Output:** `status`

---

## Recommended Models

These models have been tested and work well for prompt enhancement:

| Model | Type | Description |
|-------|------|-------------|
| [Goekdeniz-Guelmez/Josiefied-Qwen3-8B-abliterated-v1](https://huggingface.co/Goekdeniz-Guelmez/Josiefied-Qwen3-8B-abliterated-v1) | Local/Direct | **Recommended** — Works for both SFW and NSFW content. Available in GGUF and standard formats. |
| [Qwen/Qwen3-8B](https://huggingface.co/Qwen/Qwen3-8B) | Local/Direct | Official Qwen model — SFW only |
| `qwen/qwen3-235b-a22b:free` | OpenRouter | Free cloud option, high quality |

**VRAM Requirements (approximate):**

| Model Size | 4-bit | 8-bit | Full Precision |
|------------|-------|-------|----------------|
| 8B | ~6GB | ~10GB | ~16GB |

---

## Example

**Input:**
```
a cat
```

**Output:**
```
A domestic shorthair cat with orange and white fur sits on a wooden floor. 
The cat has bright green eyes and is looking directly at the camera with 
an alert expression. Soft natural light from a nearby window illuminates 
the scene from the left, creating gentle shadows. The background shows a 
blurred living room interior with warm earth tones. The cat's fur texture 
is clearly visible with individual strands catching the light. Centered 
composition with shallow depth of field.
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| **Empty response** | Increase `retry_count`, verify API key/endpoint |
| **Rate limiting** | Node handles automatically; wait if persistent |
| **Connection errors** | Check server is running, verify endpoint URL |
| **Out of memory** | Use `4bit` quantization, smaller model, or unload other models |
| **Model not found** | Verify HuggingFace repo ID or run `ollama pull <model>` |
| **Thinking tags in output** | Update to latest version (automatic removal) |
| **Repetitive output** | Enable `repeat_penalty` in Options node |
| **Unexpected output** | Check `debug_log` output for details |

### Common Endpoint URLs

| Server | Default Endpoint |
|--------|------------------|
| Ollama | `http://localhost:11434/v1` |
| LM Studio | `http://localhost:1234/v1` |
| vLLM | `http://localhost:8000/v1` |
| text-generation-webui | `http://localhost:5000/v1` |

---

## Credits

- **System Prompt:** [Z-Image Turbo Space](https://huggingface.co/spaces/Tongyi-MAI/Z-Image-Turbo/blob/main/pe.py) by Tongyi-MAI
- **Author:** [Koko-boya](https://github.com/Koko-boya)

### References

- [comfyui-ollama](https://github.com/stavsap/comfyui-ollama)
- [ComfyUI-QwenVL](https://github.com/1038lab/ComfyUI-QwenVL)
- [ComfyUI-EBU-LMStudio](https://github.com/burnsbert/ComfyUI-EBU-LMStudio)

## License

Apache License 2.0 — see [LICENSE](LICENSE) for details.
