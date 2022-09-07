# Stable-Diffusion-Guide
This is a guide for installing and using **Stable Diffusion WebUI** on Windows with an NVIDIA gpu

## 0. Pre-reqs
- A NVIDIA GPU with at least 4gb of VRAM  

## 1. Download 
**For git users**, use `git clone`  
**For others**, click (Code > Download ZIP) on Github & unzip  
* [Miniconda 3 x64](https://docs.conda.io/en/latest/miniconda.html)  
* [StableDiffusion Model 1.4](https://drive.yerf.org/wl/?id=EBfTrmcCCUAGaQBXVIj5lJmEhjoP1tgl)  
* [StableDiffusion WebUI](https://github.com/sd-webui/stable-diffusion-webui/)  

## 2. Install
* Run the Miniconda installer, default location, install for **all users**  
* Rename `sd-v1-4.ckpt` to `model.ckpt` and put it in `stable-diffusion-webui/models/ldm/stable-diffusion-v1`  
* If you only have 4gb of VRAM, see [Running on 4GB](#troubleshooting-running-on-4gb)

## 3. Start
* Run `webui.cmd` in `stable-diffusion-webui`  
* Wait patiently while it installs dependencies and does a first time run. It may seem "stuck" but it isn't. It may take up to 10-15 minutes  
* The command line will show you image generation progress  
* After it finishes setting up, go to [http://localhost:7860/](http://localhost:7860/)  

## 4. Usage
### Basics
There are a bunch of settings, but they are simpler than they look. I will cover the important ones on the **Text-to-Image** tab  
- `Text Input` - this is where you put your prompts. Generally more detail is good. Words are roughly weighted by order. See [this post](https://www.reddit.com/r/StableDiffusion/comments/x41n87/how_to_get_images_that_dont_suck_a/) for tips
  
  
- `Width` & `Height` - dimensions of output image. Don't go above **512**, if you get memory errors, lower them until it works  
- `Classifer Free Guidance Scale` - How strongly the image follows the prompt  
   (**0 - 4**) AI has a lot of control, high variety  
   (**5 - 9**) Mixed, medium variety  
   (**10 - 14**) AI tries to follow very closely, lower variety (don't go above 14)  
- `Seed` Seed of output image. Usually you want to leave **blank**  
- `Sampling Steps` - How many iterations the AI refines the image. Higher values = slower. Ideal value depends on **Sampling Method**. Here is an [example](https://i.redd.it/ud12agb7goj91.jpg) of **sampling steps** & **sampling methods**  
- `Sampling Method` - Scroll down on [this post](https://www.reddit.com/r/StableDiffusion/comments/x41n87/how_to_get_images_that_dont_suck_a/) for some comparisons  

### Starting Recommendations
- `Classifer Free Guidance Scale` - 7.5   
- `Sampling Steps` - 10  
- `Sampling Method` - DDIM  
Test prompts with **10** steps, then once you starting getting the images you want, increase it to 25-50  

### Output
- `stable-diffusion-webui/outputs/txt2img-samples` - prompt grids (basically previews)  
- `stable-diffusion-webui/outputs/txt2img-samples/samples` - contains directories named after your prompts  
- `stable-diffusion-webui/outputs/txt2img-samples/samples/{example prompt}` - contains full size output images and yaml details file  
If generating multiple images, output can be viewed in these folders as they generate before the whole batch completes and the UI displays them  

### Example
![space-camping](docs/space-camping.jpg)  
> A Coleman Sundome Camping Tent lit from inside, on the surface of the moon, cinematic lighting, highly detailed, sharp focus, digital painting, art by junji ito and WLOP, professional photoshoot  

After entering your prompts, hit **Generate**. When it's done, you'll see **Done** in the command line and the image(s) will be output  

## 5. Up-scaling
If you want bigger images than **512** x **512**, then you need to use upscaling, which is provided on the **Image Lab** tab  

### Setup
1. Download the following
   * [LatentDiffusion](https://github.com/devilismyfriend/latent-diffusion)  
   unzip and put `latent-diffusion` in `stable-diffusion-webui/src`  
   * [GFPGANv1.3.pth](https://github.com/TencentARC/GFPGAN/releases/download/v1.3.0/GFPGANv1.3.pth)  
   put in `stable-diffusion-webui/src/gfpgan/experiments/pretrained_models/`  
   * [RealESRGAN_x4plus.pth](https://github.com/xinntao/Real-ESRGAN/releases/download/v0.1.0/RealESRGAN_x4plus.pth) & [RealESRGAN_x4plus_anime_6B.pth](https://github.com/xinntao/Real-ESRGAN/releases/download/v0.2.2.4/RealESRGAN_x4plus_anime_6B.pth)  
   put in `stable-diffusion-webui/src/realesgran/experiments/pretrained_models/`  
2. Restart **webui.cmd**  

### Usage
1. Open the **Image Lab** tab in the UI  
2. Select your photo  
3. Choose your processors (1 or both)  
4. Processor settings  

### Processor Settings
#### FaceFix
`Effect strength` - leave at **100**  
#### Upscaling
`Upscaler Selection` - descriptions can be found on the left of the page under **Process**  
`RealESRGAN model` - leave as `x4plus` unless you are upscaling "anime", in which case select `x4plus_anime`  
#### Upscaling (GoBig & GoLatent)
These settings are the same as **Text-to-Image**. A prompt can be added to influence changes  

### Output
- `stable-diffusion-webui/outputs/imglab-samples` - output parent directory  
- `stable-diffusion-webui/outputs/imglab-samples/{upscale type}` - full size upscaled images  

## Useful Resources
* [StableDiffusion wiki (prompt tips)](https://wiki.installgentoo.com/wiki/Stable_Diffusion)
* [Prompt builder](https://promptomania.com/stable-diffusion-prompt-builder/)

## Troubleshooting: RUNNING ON 4GB
It is possible to run the Stable Diffusion webui on 4gb Vram with some modifications:

1. Edit `/scripts/relauncher.py` in your preferred text editor  
2. Change line 8 in `relauncher.py` FROM "`python scripts/webui.py`" to the following:  
"`python scripts/webui.py --optimized`" and save  
(This tells it to optimize VRAM by generating incrementally, it should be noticed that this sacrifices generation speed)  
3. Launch webui.cmd like normal  

**If you are still getting an 'Out of Memory' error:**  
Delete or rename the ESRGAN and GFPGAN models (so they don't load into memory) and relaunch  
(You can use them both as external programs anyway)  

**If your output is a solid green square (known problem on GTX 16xx):**  
Add "`--precision full --no-half`" to the launch parameters above, it should look like this:  
"`python scripts/webui.py --precision full --no-half --optimized`"  
Unfortunately, the full precision fix raises ram use drastically, so you may have to moderately reduce your output to **448** x **448** if on 4gb  