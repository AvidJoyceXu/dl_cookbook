# Learning Huggingface

## password
3?6, ? is for a special character inserted between two common used password slices
## General Concepts
### Model CPU offloading
- offload data from GPU to CPU and vice versa
- keep only the necessary data in GPU for back-propagation and so on
## Diffusers
- reference: https://huggingface.co/docs/diffusers/index
### Main Components
#### Pipelines, Schedulers and Models
- State-of-the-art diffusion pipelines for inference
- interchangeable noise schedulers
- pretrained models serving as building blocks
> pipeline is made up of **schedulers** and **models**, and should not be directly trained
![[Pasted image 20240409100618.png]]
- the Stable Diffusion pipeline is composed of the [UNet2DConditionModel](https://huggingface.co/docs/diffusers/v0.27.2/en/api/models/unet2d-cond#diffusers.UNet2DConditionModel) and [PNDMScheduler](https://huggingface.co/docs/diffusers/v0.27.2/en/api/schedulers/pndm#diffusers.PNDMScheduler) among other things:
> but how come that input and output between modules are smoothly in line wth each other?
> oh I understand. the `from_pretrained()` function actually did all these works, and `pipeline` reports the following human-understandable but limited information.
```Shell
>>> pipeline
StableDiffusionPipeline {
  "_class_name": "StableDiffusionPipeline",
  "_diffusers_version": "0.21.4",
  ...,
  "scheduler": [
    "diffusers",
    "PNDMScheduler"
  ],
  ...,
  "unet": [
    "diffusers",
    "UNet2DConditionModel"
  ],
  "vae": [
    "diffusers",
    "AutoencoderKL"
  ]
}
```
#### Switching between schedulers
```Python
from diffusers import EulerDiscreteScheduler

pipeline = DiffusionPipeline.from_pretrained("runwayml/stable-diffusion-v1-5", use_safetensors=True)
pipeline.scheduler = EulerDiscreteScheduler.from_config(pipeline.scheduler.config)
```
### Task Descriptions
- inpaint: fill the masked part of an image given the image, the mask and a text prompt
- img2img: adapt an image guided by a text prompt
![[Pasted image 20240409101115.png]]
### Retraining
https://huggingface.co/docs/diffusers/training/overview
#### Textual Inversion
> personalizing image generation models with just a few example images of what you want it to learn. This technique works by **learning and updating the text embeddings** (the new embeddings are tied to a **special word** you must use in the prompt) to match the example images you provide.

##### Install the `diffusers` library from source
```Shell
git clone https://github.com/huggingface/diffusers
cd diffusers
pip install .
```
##### Create an image dataset for training
https://huggingface.co/docs/diffusers/training/create_dataset

##### Realize train loop
- sample
- evaluate
### Installation
#### Install the `diffusers` library from source
```Shell
pip install accelerate
pip install git+https://github.com/huggingface/diffusers
```
#### Editable install
```Shell
git clone https://github.com/huggingface/diffusers.git
cd diffusers
pip install -e ".[torch]"
```
## Cache
- maybe change the parameters of this program might work: `/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/site-packages/huggingface_hub/file_download.py`
- simply downloading the model parameters to another cache_directory!

> Model weights and files are downloaded from the Hub to a cache which is usually your home directory. You can change the cache location by specifying the `HF_HOME` or `HUGGINFACE_HUB_CACHE` environment variables or configuring the `cache_dir` parameter in methods like [from_pretrained()](https://huggingface.co/docs/diffusers/v0.27.2/en/api/pipelines/overview#diffusers.DiffusionPipeline.from_pretrained).

### Manage `huggingface-hub` cache-system
- reference: https://huggingface.co/docs/huggingface_hub/guides/manage-cache
#### Scan cache from terminal
```Shell
huggingface-cli scan-cache
```
#### Clean cache
- reference: https://huggingface.co/docs/huggingface_hub/guides/manage-cache#clean-your-cache
##### TUI
```Shell
pip install huggingface_hub["cli"]
huggingface-cli delete-cache
```
##### Without TUI
```Shell
huggingface-cli delete-cache --disable-tui
```
- manually comment out the target cache to delete
##### Clean cache from Python
```Python
from huggingface_hub import scan_cache_dir

delete_strategy = scan_cache_dir().delete_revisions(
    "81fd1d6e7847c99f5862c9fb81387956d99ec7aa"
    "e2983b237dccf3ab4937c97fa717319a9ca1a96d",
    "6c0e6080953db56375760c0471a8c5f2929baf11",
)
print("Will free " + delete_strategy.expected_freed_size_str)
# will free 8.6G
delete_strategy.execute()
# Cache deletion done. Saved 8.6G.
```
### Error message
```
FORCE_MEM_EFFICIENT_ATTN= 1 @UNET:QKVATTENTION
/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/site-packages/huggingface_hub/file_download.py:1169: FutureWarning: The `force_filename` parameter is deprecated as a new caching system, which keeps the filenames as they are on the Hub, is now in place.
  warnings.warn(
pytorch_model.bin:   6%|████▍                                                                     | 514M/8.61G [01:12<18:50, 7.16MB/s]Traceback (most recent call last):
  File "inpainting.py", line 25, in <module>
    if_I = IFStageI('IF-I-XL-v1.0', device=device)
  File "/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/site-packages/deepfloyd_if/modules/stage_I.py", line 24, in __init__
    self.model = self.load_checkpoint(self.model, self.dir_or_name)
  File "/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/site-packages/deepfloyd_if/modules/base.py", line 237, in load_checkpoint
    path = self._get_path_or_download_file_from_hf(dir_or_name, filename)
  File "/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/site-packages/deepfloyd_if/modules/base.py", line 250, in _get_path_or_download_file_from_hf
    hf_hub_download(repo_id=f'DeepFloyd/{dir_or_name}', filename=filename, cache_dir=cache_dir,
  File "/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/site-packages/huggingface_hub/utils/_validators.py", line 119, in _inner_fn
    return fn(*args, **kwargs)
  File "/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/site-packages/huggingface_hub/file_download.py", line 1186, in hf_hub_download
    return cached_download(
  File "/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/site-packages/huggingface_hub/utils/_validators.py", line 119, in _inner_fn
    return fn(*args, **kwargs)
  File "/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/site-packages/huggingface_hub/file_download.py", line 818, in cached_download
    http_get(
  File "/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/site-packages/huggingface_hub/file_download.py", line 538, in http_get
    temp_file.write(chunk)
  File "/vepfs-cnsh4137610c2f4c/algo/user8/anaconda3/envs/mimicplay/lib/python3.8/tempfile.py", line 473, in func_wrapper
    return func(*args, **kwargs)
OSError: [Errno 28] No space left on device
```