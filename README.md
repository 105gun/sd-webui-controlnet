## Highres.fix disable
This repo add a 'Disable ControlNet in Highres.fix' checkbox and a 'Highres Weight' Slider, which allow you to control weight in Highres.fix pass, or just simply disable it.
### Modify file in WebUI
You have to modify ./modules/processing.py, like this:

**Import part**

`sys.path.append('./extensions/sd-webui-controlnet')`

`from scripts.controlnet import disable_controlnet`
![QQ截图20230215205227](https://user-images.githubusercontent.com/52597991/219041010-69aaf028-9656-483e-9dab-81b88fa33612.png)

**StableDiffusionProcessingTxt2Img/sample/save_intermediate()**
` disable_controlnet()`
![QQ截图20230215205255](https://user-images.githubusercontent.com/52597991/219041197-b3b6a0df-db43-47ef-bfe9-64de05262694.png)

### Example
This example uses canny model.
  Input |  Base | Disable
 ----------- | ----------- | ----------- 
  ![Line](https://user-images.githubusercontent.com/52597991/218993431-d6799160-0358-43e5-b455-405c90e2b705.jpg) |  ![02191-3719080654-1girl,Kuniform,masterpiece,simple background,](https://user-images.githubusercontent.com/52597991/219038208-5d2a41c4-20e5-4dc6-ab99-3000e0aa784d.png) | ![02190-3719080654-1girl,Kuniform,masterpiece,simple background,](https://user-images.githubusercontent.com/52597991/219038273-0362fe1d-4169-4307-9147-8bac3644b2d8.png)




## sd-webui-controlnet
(WIP) WebUI extension for ControlNet

This extension is for AUTOMATIC1111's [Stable Diffusion web UI](https://github.com/AUTOMATIC1111/stable-diffusion-webui), allows the Web UI to add [ControlNet](https://github.com/lllyasviel/ControlNet) to the original Stable Diffusion model to generate images. The addition is on-the-fly, the merging is not required.

ControlNet is a neural network structure to control diffusion models by adding extra conditions. 

Thanks & Inspired: kohya-ss/sd-webui-additional-networks

### Limits

* Dragging large file on the Web UI may freeze the entire page. It is better to use the upload file option instead.
* Just like WebUI's [hijack](https://github.com/AUTOMATIC1111/stable-diffusion-webui/blob/3715ece0adce7bf7c5e9c5ab3710b2fdc3848f39/modules/sd_hijack_unet.py#L27), we used some interpolate to accept arbitrary size configure (see `scripts/cldm.py`)

### Install

Some users may need to install the cv2 library before using it: `pip install opencv-python`

Upgrade gradio if any ui issues occured: `pip install gradio==3.16.2`

1. Open "Extensions" tab.
2. Open "Install from URL" tab in the tab.
3. Enter URL of this repo to "URL for extension's git repository".
4. Press "Install" button.
5. Reload/Restart Web UI.

### Usage

1. Put the ControlNet models (`.pt`, `.pth`, `.ckpt` or `.safetensors`) inside the `sd-webui-controlnet/models` folder.
2. Open "txt2img" or "img2img" tab, write your prompts.
3. Press "Refresh models" and select the model you want to use. (If nothing appears, try reload/restart the webui)
4. Upload your image and select preprocessor, done.

Currently it supports both full models and trimmed models. Use `extract_controlnet.py` to extract controlnet from original `.pth` file.

Pretrained Models: https://huggingface.co/lllyasviel/ControlNet/tree/main/models

### Extraction

Two methods can be used to reduce the model's filesize:

1. Directly extract controlnet from original .pth file using `extract_controlnet.py`.

2. Transfer control from original checkpoint by making difference using `extract_controlnet_diff.py`.

All type of models can be correctly recognized and loaded. The results of different extraction methods are discussed in https://github.com/lllyasviel/ControlNet/discussions/12 and https://github.com/Mikubill/sd-webui-controlnet/issues/73. 

Pre-extracted model: https://huggingface.co/webui/ControlNet-modules-safetensors

Pre-extracted difference model: https://huggingface.co/kohya-ss/ControlNet-diff-modules

### T2I-Adapter Support (Experimental)

Currently support both sketch Adapter and image Adapter. Note that the impl is experimental, result may differ from original repo. See `Adapter Examples` for reference.

To use these models:
1. Download files from https://huggingface.co/TencentARC/T2I-Adapter
2. Setup correct config in settings panel - `sketch_adapter_v14.yaml` for sketch model and `image_adapter_v14.yaml` for keypose and segmentation model.
3. It's better to use a slightly lower strength (t) when generating images with sketch model, such as 0.6-0.8. (ref: [ldm/models/diffusion/plms.py](https://github.com/TencentARC/T2I-Adapter/blob/5f41a0e38fc6eac90d04bc4cede85a2bc4570653/ldm/models/diffusion/plms.py#L158))

### Tips 

* Don't forget to add some negative prompt, default negative prompt in ControlNet repo is "longbody, lowres, bad anatomy, bad hands, missing fingers, extra digit, fewer digits, cropped, worst quality, low quality".
* Regarding canvas height/width: they are designed for canvas generation. If you want to upload images directly, you can safely ignore them.

### Examples

| Source | Input | Output |
|:-------------------------:|:-------------------------:|:-------------------------:|
| (no preprocessor) |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/bal-source.png?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/bal-gen.png?raw=true"> |
| (no preprocessor) |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/dog_rel.jpg?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/dog_rel.png?raw=true"> |
|<img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/mahiro_input.png?raw=true">  |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/mahiro_canny.png?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/mahiro-out.png?raw=true"> |
|<img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/evt_source.jpg?raw=true">  |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/evt_hed.png?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/evt_gen.png?raw=true"> |
|<img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/an-source.jpg?raw=true">  |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/an-pose.png?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/an-gen.png?raw=true"> |
|<img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/sk-b-src.png?raw=true">  |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/sk-b-dep.png?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/sk-b-out.png?raw=true"> |
|<img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/nm-src.png?raw=true">  |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/nm-gen.png?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/nm-out.png?raw=true"> |

### Adapter Examples

| Source | Input | Output |
|:-------------------------:|:-------------------------:|:-------------------------:|
| (no preprocessor) |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/dog_sk-2.png?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/dog_out-2.png?raw=true"> |
| (no preprocessor) |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/cat_sk-2.png?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/cat_out-2.png?raw=true"> |
| (no preprocessor) |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/kp_a-2.png?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/kp_o-2.png?raw=true"> |
| (no preprocessor) |  <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/kp_a2-2.png?raw=true"> | <img width="256" alt="" src="https://github.com/Mikubill/sd-webui-controlnet/blob/main/samples/kp_o2-2.png?raw=true"> |

### Minimum Requirements

* (Windows) (NVIDIA: Ampere) 4gb - with `--xformers` enabled, and `Low VRAM` mode ticked in the UI, goes up to 768x832


