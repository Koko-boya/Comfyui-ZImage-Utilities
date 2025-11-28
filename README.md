# ComfyUI-ZImage-Utilities

**A comprehensive utility suite designed to elevate Z-Image generation quality through superior input processing and intelligent prompt engineering.**

This project is not just a prompt enhancer—it is a collection of specialized nodes built to ensure your Z-Image workflows start with the highest quality data possible. By leveraging the "Logic Cage" methodology and the Qwen model via OpenRouter, these utilities transform vague ideas into precise, aesthetically optimized instructions that Z-Image models can understand perfectly.

## The Z-Image Philosophy

Quality in, quality out. Z-Image models thrive on specificity, visual clarity, and logical consistency. This utility suite provides the infrastructure to:
1.  **Standardize Inputs**: Ensure every prompt follows a strict, high-quality format.
2.  **Maximize Intelligence**: Use state-of-the-art LLMs to reason about and expand your concepts.
3.  **Ensure Reliability**: Robust error handling and rate limiting keep your workflows running smoothly.

## The Utility Nodes

### 1. Z-Image Prompt Enhancer ("The Logic Cage")
The core engine of the suite. This node doesn't just "rewrite" prompts; it forces them through a rigorous logical structure (the "Logic Cage") to ensure:
-   **Fidelity**: The core intent is locked and preserved.
-   **Detail**: Aesthetic and realistic details are injected based on visual reasoning.
-   **Clarity**: Ambiguities are removed, and text elements are handled with precision.

**Key Features:**
-   **Bilingual Support**: Native handling of Chinese and English.
-   **Smart Cleaning**: Automatically strips "thinking" tags and markdown artifacts.
-   **Detailed Logging**: Full visibility into the enhancement process.

### 2. Z-Image OpenRouter API Router
The connectivity backbone. This node manages the connection to the intelligence source (OpenRouter), allowing you to:
-   **Switch Models**: Use any model on OpenRouter (default: `qwen/qwen3-235b-a22b:free`).
-   **Manage Costs**: Use free models or premium ones as needed.
-   **Centralize Config**: Configure your API key once and use it across your workflow.

### 3. Z-Image Prompt Enhancer + CLIP
The integration layer. This node combines the enhancement engine with CLIP encoding, streamlining your workflow by:
-   **Direct Conditioning**: Outputting ready-to-use conditioning for KSamplers.
-   **Reducing Clutter**: Eliminating the need for separate CLIP Text Encode nodes.
-   **Ensuring Compatibility**: seamlessly bridging text enhancement with image generation.

## Features at a Glance

| Feature | Description |
| :--- | :--- |
| **Reliability** | Strict error handling, smart rate limiting (respects `Retry-After`), and auto-retries. |
| **Quality** | "Logic Cage" prompting ensures high-fidelity, aesthetically pleasing outputs. |
| **Flexibility** | User-definable models, temperature control, and manual retry settings. |
| **Transparency** | Detailed debug logs show exactly what the model is thinking and doing. |
| **Cost-Effective** | Default configuration uses 100% FREE models via OpenRouter. |

## Installation

1.  Clone this repository into your `ComfyUI/custom_nodes/` directory:
    ```bash
    cd ComfyUI/custom_nodes/
    git clone https://github.com/yourusername/ComfyUI-ZImage-Utilities.git
    ```
2.  Restart ComfyUI.

## Configuration

1.  Get a free API key from [OpenRouter](https://openrouter.ai/keys).
2.  Add the **Z-Image OpenRouter API Router** node to your workflow.
3.  Enter your API key.

## Usage Workflows

### Standard Enhancement
*Best for general usage where you want to see the text output.*
```
[Z-Image OpenRouter API Router] 
       |
       v
[Z-Image Prompt Enhancer] <-- [Primitive String (Your Prompt)]
       |
       v
[CLIP Text Encode] --> [KSampler]
```

### Integrated Pipeline
*Best for streamlined, high-efficiency workflows.*
```
[Checkpoint Loader] --> [Z-Image Prompt Enhancer + CLIP] --> [KSampler]
                                   ^
                                   |
                     [Z-Image OpenRouter API Router]
```

## Credits

-   **Prompt Template**: Adapted from the [Z-Image Turbo Space](https://huggingface.co/spaces/Tongyi-MAI/Z-Image-Turbo/blob/main/pe.py).

## License

MIT
