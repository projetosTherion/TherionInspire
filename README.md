# ComfyUI-Inspire-Pack
This repository offers various extension nodes for ComfyUI. Nodes here have different characteristics compared to those in the ComfyUI Impact Pack. The Impact Pack has become too large now...

## Notice:
* V0.69 incompatible with the outdated **ComfyUI IPAdapter Plus**. (A version dated March 24th or later is required.)
* V0.64 add sigma_factor to RegionalPrompt... nodes required Impact Pack V4.76 or later.
* V0.62 support faceid in Regional IPAdapter
* V0.48 optimized wildcard node. This update requires Impact Pack V4.39.2 or later.
* V0.13.2 isn't compatible with old ControlNet Auxiliary Preprocessor. If you will use `MediaPipeFaceMeshDetectorProvider` update to latest version(Sep. 17th).
* WARN: If you use version **0.12 to 0.12.2** without a GlobalSeed node, your workflow's seed may have been erased. Please update immediately.

## Nodes
* Lora Block Weight - This is a node that provides functionality related to Lora block weight.
    * This provides similar functionality to [sd-webui-lora-block-weight](https://github.com/hako-mikan/sd-webui-lora-block-weight)
    * `Lora Loader (Block Weight)`: When loading Lora, the block weight vector is applied.
        * In the block vector, you can use numbers, R, A, a, B, and b.
        * R is determined sequentially based on a random seed, while A and B represent the values of the A and B parameters, respectively. a and b are half of the values of A and B, respectively.
    * `XY Input: Lora Block Weight`: This is a node in the [Efficiency Nodes](https://github.com/LucianoCirino/efficiency-nodes-comfyui)' XY Plot that allows you to use Lora block weight.
        * You must ensure that X and Y connections are made, and dependencies should be connected to the XY Plot.
        * Note: To use this feature, update `Efficient Nodes` to a version released after September 3rd.

* SEGS Supports nodes - This is a node that supports ApplyControlNet (SEGS) from the Impact Pack.
    * `OpenPose Preprocessor Provider (SEGS)`: OpenPose preprocessor is applied for the purpose of using OpenPose ControlNet in SEGS.
        * You need to install [ControlNet Auxiliary Preprocessors](https://github.com/Fannovel16/comfyui_controlnet_aux) to use this.
    * `Canny Preprocessor Provider (SEGS)`: Canny preprocessor is applied for the purpose of using Canny ControlNet in SEGS.
    * `DW Preprocessor Provider (SEGS)`, `MiDaS Depth Map Preprocessor Provider (SEGS)`, `LeReS Depth Map Preprocessor Provider (SEGS)`, 
      `MediaPipe FaceMesh Preprocessor Provider (SEGS)`, `HED Preprocessor Provider (SEGS)`, `Fake Scribble Preprocessor (SEGS)`, 
      `AnimeLineArt Preprocessor Provider (SEGS)`, `Manga2Anime LineArt Preprocessor Provider (SEGS)`, `LineArt Preprocessor Provider (SEGS)`,
      `Color Preprocessor Provider (SEGS)`, `Inpaint Preprocessor Provider (SEGS)`, `Tile Preprocessor Provider (SEGS)`, `MeshGraphormer Depth Map Preprocessor Provider (SEGS)`  
    * `MediaPipeFaceMeshDetectorProvider`: This node provides `BBOX_DETECTOR` and `SEGM_DETECTOR` that can be used in Impact Pack's Detector using the `MediaPipe-FaceMesh Preprocessor` of ControlNet Auxiliary Preprocessors.

* A1111 Compatibility support - These nodes assists in replicating the creation of A1111 in ComfyUI exactly.
    * `KSampler (Inspire)`: ComfyUI uses the CPU for generating random noise, while A1111 uses the GPU. One of the three factors that significantly impact reproducing A1111's results in ComfyUI can be addressed using `KSampler (Inspire)`.
        * Other point #1 : Please make sure you haven't forgotten to include 'embedding:' in the embedding used in the prompt, like 'embedding:easynegative.'
        * Other point #2 : ComfyUI and A1111 have different interpretations of weighting. To align them, you need to use [BlenderNeko/Advanced CLIP Text Encode](https://github.com/BlenderNeko/ComfyUI_ADV_CLIP_emb).
    * `KSamplerAdvanced (Inspire)`: Inspire Pack version of `KSampler (Advanced)`.
    * Common Parameters
      * `batch_seed_mode` determines how seeds are applied to batch latents:
        * `comfy`: This method applies the noise to batch latents all at once. This is advantageous to prevent duplicate images from being generated due to seed duplication when creating images.
        * `incremental`: Similar to the A1111 case, this method incrementally increases the seed and applies noise sequentially for each batch. This approach is beneficial for straightforward reproduction using only the seed.
        * `variation_strength`: In each batch, the variation strength starts from the set `variation_strength` and increases by `xxx`.
      * `variation_seed` and `variation_strength` - Initial noise generated by the seed is transformed to the shape of `variation_seed` by `variation_strength`. If `variation_strength` is 0, it only relies on the influence of the seed, and if `variation_strength` is 1.0, it is solely influenced by `variation_seed`.
        * These parameters are used when you want to maintain the composition of an image generated by the seed but wish to introduce slight changes.

* Prompt Support - These are nodes for supporting prompt processing.
  * `Load Prompts From Dir (Inspire)`: It sequentially reads prompts files from the specified directory. The output it returns is ZIPPED_PROMPT.
    * Specify the directories located under `ComfyUI-Inspire-Pack/prompts/`
    * One prompts file can have multiple prompts separated by `---`. 
    * e.g. `prompts/example`
  * `Load Prompts From File (Inspire)`: It sequentially reads prompts from the specified file. The output it returns is ZIPPED_PROMPT.
    * Specify the file located under `ComfyUI-Inspire-Pack/prompts/`
    * e.g. `prompts/example/prompt2.txt` 
  * `Unzip Prompt (Inspire)`: Separate ZIPPED_PROMPT into `positive`, `negative`, and name components. 
    * `positive` and `negative` represent text prompts, while `name` represents the name of the prompt. When loaded from a file using `Load Prompts From File (Inspire)`, the name corresponds to the file name.
  * `Zip Prompt (Inspire)`: Create ZIPPED_PROMPT from positive, negative, and name_opt.
    * If name_opt is omitted, it will be considered as an empty name.
  * `Prompt Extractor (Inspire)`: This node reads prompt information from the image's metadata. Since it retrieves all the text, you need to directly specify the prompts to be used for `positive` and `negative` as indicated in the info.
  * `Global Seed (Inspire)`: This is a node that controls the global seed without a separate connection line. It only controls when the widget's name is 'seed' or 'noise_seed'. Additionally, if 'control_before_generate' is checked, it controls the seed before executing the prompt.
    * Seeds that have been converted into inputs are excluded from the target. If you want to control the seed separately, convert it into an input and control it separately.
  * `Global Sampler (Inspire)`: This node is similar to GlobalSeed and can simultaneously set the sampler_name and scheduler for all nodes in the workflow.
    * It applies only to nodes that have both sampler_name and scheduler, and it won't be effective if `GlobalSampler` is muted.
    * If some of the `sampler_name` and `scheduler` have been converted to input and connected to Primitive node, it will not apply only to the converted widget. The widget that has not been converted to input will still be affected.
  * `Bind [ImageList, PromptList] (Inspire)`: Bind Image list and zipped prompt list to export `image`, `positive`, `negative`, and `prompt_label` in a list format. If there are more prompts than images, the excess prompts are ignored, and if there are not enough, the remainder is filled with default input based on the images.
  * `Wildcard Encode (Inspire)`: The combination node of [ImpactWildcardEncode](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/ImpactWildcard.md) and BlenderNeko's [CLIP Text Encode (Advanced)](https://github.com/BlenderNeko/ComfyUI_ADV_CLIP_emb).
    * To use this node, you need both the [Impact Pack](https://github.com/ltdrdata/ComfyUI-Impact-Pack) and the [Advanced CLIP Text Encode]((https://github.com/BlenderNeko/ComfyUI_ADV_CLIP_emb)) extensions.
    * This node is identical to `ImpactWildcardEncode`, but it encodes using `CLIP Text Encode (Advanced)` instead of the default CLIP Text Encode from ComfyUI for CLIP Text Encode.
    * Requirement: Impact Pack V4.18.6 or above
  * `Prompt Builder (Inspire)`: This node is a convenience node that allows you to easily assemble prompts by selecting categories and presets. To modify the presets, edit the `ComfyUI-InspirePack/resources/prompt-builder.yaml` file.
  * `Seed Explorer (Inspire)`: This node helps explore seeds by allowing you to adjust the variation seed gradually in a prompt-like form.
    * This feature is designed for utilizing a seed that you like, adding slight variations, and then further modifying from there when exploring.
    * In the `seed_prompt`, the first seed is considered the initial seed, and the reflection rate is omitted, always defaulting to 1.0.
    * Each prompt is separated by a comma, and from the second seed onwards, it should follow the format `seed:strength`.
    * Pressing the "Add to prompt" button will append `additional_seed:additional_strength` to the prompt.
  * `Random Generator for List (Inspire)`: When connecting the list output to the signal input, this node generates random values for all items in the list.
  * `Make Basic Pipe (Inspire)`: This is a node that creates a BASIC_PIPE using Wildcard Encode. The `Add select to` determines whether the selected item from the `Select to...` combo will be input as positive wildcard text or negative wildcard text.
  * `Remove ControlNet (Inspire)`, `Remove ControlNet [RegionalPrompts] (Inspire)`: Remove ControlNet from CONDITIONING or REGIONAL_PROMPTS.
    * `Remove ControlNet [RegionalPrompts] (Inspire)` requires Impact Pack V4.73.1 or above.

* Regional Nodes - These node simplifies the application of prompts by region.
  * Regional Sampler - These nodes assists in the easy utilization of the regional sampler in the `Impact Pack`.
    * `Regional Prompt Simple (Inspire)`: This node takes `mask` and `basic_pipe` as inputs and simplifies the creation of `REGIONAL_PROMPTS`.
    * `Regional Prompt By Color Mask (Inspire)`: Similar to `Regional Prompt Simple (Inspire)`, this function accepts a color mask image as input and defines the region using the color value that will be used as the mask, instead of directly receiving the mask.
      * The color value can only be in the form of a hex code like #FFFF00 or a decimal number.
  * Regional Conditioning - These nodes provides assistance for simplifying the use of `Conditioning (Set Mask)`.
    * `Regional Conditioning Simple (Inspire)`
    * `Regional Conditioning By Color Mask (Inspire)`
  * Regional IPAdapter - These nodes facilitates the convenient use of the attn_mask feature in `ComfyUI IPAdapter Plus` custom nodes.
    * To use this node, you need to install the [ComfyUI IPAdapter Plus](https://github.com/cubiq/ComfyUI_IPAdapter_plus) extension.
    * `Regional IPAdapter Mask (Inspire)`, `Regional IPAdapter By Color Mask (Inspire)`
    * `Regional IPAdapter Encoded Mask (Inspire)`, `Regional IPAdapter Encoded By Color Mask (Inspire)`: accept `embeds` instead of `image`
  * Regional Seed Explorer - These nodes restrict the variation through a seed prompt, applying it only to the masked areas.
    * `Regional Seed Explorer By Mask (Inspire)` 
    * `Regional Seed Explorer By Color Mask (Inspire)`

* Image Util
  * `Load Image Batch From Dir (Inspire)`: This is almost same as `LoadImagesFromDirectory` of [ComfyUI-Advanced-Controlnet](https://github.com/Kosinkadink/ComfyUI-Advanced-ControlNet). This is just a modified version. Just note that this node forcibly normalizes the size of the loaded image to match the size of the first image, even if they are not the same size, to create a batch image.  
  * `Load Image List From Dir (Inspire)`: This is almost same as `Load Image Batch From Dir (Inspire)`. However, note that this node loads data in a list format, not as a batch, so it returns images at their original size without normalizing the size.
  * `Load Image (Inspire)`: This node is similar to LoadImage, but the loaded image information is stored in the workflow. The image itself is stored in the workflow, making it easier to reproduce image generation on other computers.
  * `Change Image Batch Size (Inspire)`: Change Image Batch Size
    * `simple`: if the `batch_size` is larger than the batch size of the input image, the last frame will be duplicated. If it is smaller, it will be simply cropped.
  * `Change Latent Batch Size (Inspire)`: Change Latent Batch Size
  * `ImageBatchSplitter //Inspire`, `LatentBatchSplitter //Inspire`: The script divides a batch of images/latents into individual images/latents, each with a quantity equal to the specified `split_count`. An additional output slot is added for each `split_count`. If the number of images/latents exceeds the `split_count`, the remaining ones are returned as the "remained" output.
  * `Color Map To Masks (Inspire)`: From the color_map, it extracts the top max_count number of colors and creates masks. min_pixels represents the minimum number of pixels for each color.
  * `Select Nth Mask (Inspire)`: Extracts the nth mask from the mask batch.
  
* KSampler Progress - In KSampler, the sampling process generates latent batches. By using `Video Combine` node from [ComfyUI-VideoHelperSuite](https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite), you can create a video from the progress.

* Backend Cache - Nodes for storing arbitrary data from the backend in a cache and sharing it across multiple workflows.
  * `Cache Backend Data (Inspire)`: Stores any backend data in the cache using a string key. Tags are for quick reference.
  * `Retrieve Backend Data (Inspire)`: Retrieves cached backend data using a string key.
  * `Remove Backend Data (Inspire)`: Removes cached backend data. 
    * Deletion in this node only removes it from the cache managed by Inspire, and if it's still in use elsewhere, it won't be completely removed from memory.
    * `signal_opt` is used to control the order of execution for this node; it will still run without a `signal_opt` input.
    * When using '*' as the key, it clears all data.
  * `Show Cached Info (Inspire)`: Displays information about cached data.
    * Default tag cache size is 5. You can edit the default size of each tag in `cache_settings.json`.
    * Runtime tag cache size can be modified on the `Show Cached Info (Inspire)` node. For example: `ckpt: 10`.
  * `Cache Backend Data [NumberKey] (Inspire)`, `Retrieve Backend Data [NumberKey] (Inspire)`, `Remove Backend Data [NumberKey] (Inspire)`: These nodes are provided for convenience in the automation process, allowing the use of numbers as keys.
  * `Cache Backend Data List (Inspire)`, `Cache Backend Data List [NumberKey] (Inspire)`: This node allows list input for backend cache. Conversely, nodes like `Cache Backend Data [NumberKey] (Inspire)` that do not accept list input will attempt to cache redundantly and overwrite existing data if provided with a list input. Therefore, it is necessary to use a unique key for each element to prevent this. This node caches the combined list. When retrieving cached backend data through this node, the output is in the form of a list.
  * `Shared Checkpoint Loader (Inspire)`: When loading a checkpoint through this loader, it is automatically cached in the backend cache. Additionally, if it is already cached, it retrieves it from the cache instead of loading it anew.
    * When `key_opt` is empty, the `ckpt_name` is set as the cache key. The cache key output can be used for deletion purposes with Remove Back End.
    * This node resolves the issue of reloading checkpoints during workflow switching.
  * `Stable Cascade Checkpoint Loader (Inspire)`: This node provides a feature that allows you to load the `stage_b` and `stage_c` checkpoints of Stable Cascade at once, and it also provides a backend caching feature, optionally.

* Conditioning - Nodes for conditionings
  * `Concat Conditionings with Multiplier (Inspire)`: Concatenating an arbitrary number of Conditionings while applying a multiplier for each Conditioning. The multiplier depends on `comfy_PoP`, so [comfy_PoP](https://github.com/picturesonpictures/comfy_PoP) must be installed.

* Models - Nodes for models
  * `IPAdapter Model Helper (Inspire)`: This provides presets that allow for easy loading of the IPAdapter related models. However, it is essential for the model's name to be accurate.
    * You can download the appropriate model through ComfyUI-Manager.

* Util - Utilities
  * `Float Range (Inspire)`: Create a float list that increases the value by `step` from `start` to `stop`. A list as large as the maximum limit is created, and when `ensure_end` is enabled, the last value of the list becomes the stop value.
  * `ToIPAdapterPipe (Inspire)`, `FromIPAdapterPipe (Inspire)`: These nodes assists in conveniently using the bundled ipadapter_model, clip_vision, and model required for applying IPAdapter.
  * `List Counter (Inspire)`: When each item in the list traverses through this node, it increments a counter by one, generating an integer value.
  
   
## Credits

ComfyUI/[ComfyUI](https://github.com/comfyanonymous/ComfyUI) - A powerful and modular stable diffusion GUI.

ComfyUI/[sd-webui-lora-block-weight](https://github.com/hako-mikan/sd-webui-lora-block-weight) - The original idea for LoraBlockWeight came from here, and it is based on the syntax of this extension.

jags111/[efficiency-nodes-comfyui](https://github.com/jags111/ComfyUI-Jags-workflows) - The `XY Input` provided by the Inspire Pack supports the `XY Plot` of this node.

Fannovel16/[comfyui_controlnet_aux](https://github.com/Fannovel16/comfyui_controlnet_aux) - The wrapper for the controlnet preprocessor in the Inspire Pack depends on these nodes.

Kosinkadink/[ComfyUI-Advanced-Controlnet](https://github.com/Kosinkadink/ComfyUI-Advanced-ControlNet) - `Load Images From Dir (Inspire)` code is came from here. 

Trung0246/[ComfyUI-0246](https://github.com/Trung0246/ComfyUI-0246) - Nice bypass hack!

cubiq/[ComfyUI_IPAdapter_plus](https://github.com/cubiq/ComfyUI_IPAdapter_plus) - IPAdapter related nodes depend on this extension.