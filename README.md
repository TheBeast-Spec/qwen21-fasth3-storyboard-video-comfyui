# Qwen Image 2.1 + FastH3: Storyboard to Continuous Video (ComfyUI)

Two ComfyUI workflows from Noncoder's tutorial **Continuous AI Video With Qwen 2.1 + FastH3 (No Cuts, ComfyUI)**.

1. **Storyboard.** Qwen Image 2.1 draws a 9-panel storyboard from one prompt and an optional character reference, then splits it into nine sharp panels.
2. **Storyboard to long video.** FastH3 animates each pair of neighbouring panels. Motion Context carries every segment into the next, so the result is one continuous take with sound and no cuts.

## Start here

1. Update ComfyUI to the latest version. Workflow 2 uses the built-in Start Loop, End Loop and Concatenate Video nodes.
2. Download this repository with **Code > Download ZIP** and extract it.
3. Drag a workflow from `workflows/` onto the ComfyUI canvas.
4. Install the missing custom nodes with **Manager > Install Missing Custom Nodes**.
5. Download the models below into the folders shown.
6. Read the notes on the canvas before your first run.

## Workflows

| File | What it does |
|---|---|
| `workflows/1_qwen21_storyboard_9grid.json` | Prompt + character reference → 3×3 storyboard → nine panels |
| `workflows/2_storyboard_to_long_video_fasth3.json` | Storyboard image (option A) or panels folder (option B) + one prompt per transition → one continuous video |

The prompt boxes start empty: write your own storyboard prompt in workflow 1 and one prompt per transition in workflow 2. The character reference, the storyboard and the panels are not included; load your own.

## Models

| File | ComfyUI folder | Used in |
|---|---|---|
| [qwen_image_2.1_int8_convrot.safetensors](https://huggingface.co/Comfy-Org/Qwen-Image-2.1/resolve/main/diffusion_models/qwen_image_2.1_int8_convrot.safetensors) | `models/diffusion_models` | 1 |
| [qwen3vl_8b_int8_convrot.safetensors](https://huggingface.co/Comfy-Org/Qwen-Image-2.1/resolve/main/text_encoders/qwen3vl_8b_int8_convrot.safetensors) | `models/text_encoders` | 1 |
| [qwen_image_2.1_vae_bf16.safetensors](https://huggingface.co/Comfy-Org/Qwen-Image-2.1/resolve/main/vae/qwen_image_2.1_vae_bf16.safetensors) | `models/vae` | 1 |
| [fastvideo_fasth3_8step_v2_pruned_int8_convrot.safetensors](https://huggingface.co/FastVideo/FastVideo-FastH3-Comfy/resolve/main/diffusion_models/fastvideo_fasth3_8step_v2_pruned_int8_convrot.safetensors) | `models/diffusion_models` | 2 |
| [qwen3vl_32b_minimax_h3_nvfp4_awq.safetensors](https://huggingface.co/Comfy-Org/MiniMax-H3/resolve/main/text_encoders/qwen3vl_32b_minimax_h3_nvfp4_awq.safetensors) | `models/text_encoders` | 2 |
| [minimax_h3_video_vae_fp16.safetensors](https://huggingface.co/Comfy-Org/MiniMax-H3/resolve/main/vae/minimax_h3_video_vae_fp16.safetensors) | `models/vae` | 2 |
| [minimax_h3_audio_vae_fp32.safetensors](https://huggingface.co/Comfy-Org/MiniMax-H3/resolve/main/vae/minimax_h3_audio_vae_fp32.safetensors) | `models/vae` | 2 |

Model pages: [Comfy-Org/Qwen-Image-2.1](https://huggingface.co/Comfy-Org/Qwen-Image-2.1) · [FastVideo/FastVideo-FastH3-Comfy](https://huggingface.co/FastVideo/FastVideo-FastH3-Comfy) · [Comfy-Org/MiniMax-H3](https://huggingface.co/Comfy-Org/MiniMax-H3)

The H3 text encoder is NVFP4, built for RTX 50 cards. On an RTX 30 or 40 card, try `qwen3vl_32b_minimax_h3_int8_convrot` from the same Comfy-Org folder. That pairing hasn't been tested with this workflow.

## Custom nodes

- [ComfyUI-H3-Motion-Context](https://github.com/NikoDemon80/ComfyUI-H3-Motion-Context): carries each segment's motion and sound into the next
- [ComfyUI-Easy-Use](https://github.com/yolain/ComfyUI-Easy-Use): grid split, panel loop and switches
- [ComfyUI-VideoHelperSuite](https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite): loads the panels folder (option B)
- [ComfyUI-KJNodes](https://github.com/kijai/ComfyUI-KJNodes): the Set and Get tags that replace long wires

## Using workflow 2

- **Panels.** Option A: load the 3×3 storyboard and it's split for you. Option B: paste the full path of a folder of panels named in order (`panel_01.png`, `panel_02.png` …). Set **Panel source** to `0` for A or `1` for B.
- **Prompts.** One per transition: Transition 1 is panel 1 → 2. Nine panels need eight prompts. Say where every hand and prop ends up, and add a speed plan (real time or slow motion).
- **Length and seed.** 5 seconds per segment is a good default. A fixed seed makes reruns repeatable.
- **Fix one segment.** After one full run, set **First** to the broken segment and **Last** to 99, then run again. Only those segments render, continuing from the saved motion of the one before, so the joins stay smooth.
- **Check segments.** The **SEGMENTS** player shows every segment in order. **FULL_VIDEO** is the joined film with sound.

## Tested on

RTX 5070 Ti (16 GB) with 32 GB of system RAM: about 4 to 5 minutes per 5-second segment. An 8-segment run in one go can run out of system RAM on 32 GB. If it does, render segments 1 to 4, restart ComfyUI, then render 5 to 99.

## Known limits

- FastH3 sometimes invents a cut partway through a segment. It's random per seed: redo that segment with a new seed.
- Hands, fingers and small props are the weak spot. Fix them in the panel before rendering, because each panel is the end frame of a segment.

## Licence and privacy

Third-party nodes and model weights remain subject to their own licences. This repository contains no model weights, reference images, personal file paths or credentials.
