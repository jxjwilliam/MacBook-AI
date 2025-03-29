### 🥃 [Textual Inversion](https://textual-inversion.github.io/)

- https://stablediffusionweb.com/WebUI#demo
- https://github.com/AUTOMATIC1111/stable-diffusion-webui

### 🥃 models

1. SDXL

- CounterfeitXL
- SDXL_Niji_Special Edition
- RongHua

2. general

- DreamShape (-> Midjourney)
- Deliberate (creative)

3. realistic

- Realistic
- epicRealism
- majicMiX realistic (Asian), portraits (512x768)

4. Anime

- GhostMix
- Counterfeit-V3.0
- MeinaMix (no Lora needed)
- Anything V5/Ink
- ToonYou

5. 2.5D

- ReV Animated
- RealCartoon3D
- Dark Sushi 2.5D
- XXMixUnreal
- Disney Pixar Cartoon Type A

### SD-webUI negative prompt

- easynegative, bad-hand5
- controlnet, DW-openpose

### AnimateDiff

```text
1girl,wizard,circlet,earrings,jewelry,purple hair,
0:standing,close eye,
16:sitting,full body,rain
32:...
```

- SD webui版
- comfyUI版
- codes版

### TODO

- easyphoto
1:58:59
2:17:18


IP Adapter FaceID v2 Lora

### 🥃 LoRA (Low-Rank Adpation)

### 🥃 Quantization

- Convert Huggingface Models to GGUF (Quantization)
- llama.cpp:  `docker run -rm ./model:/chromadb quantization/... /chromada`

### 🥃 Hypernetworks (超网络)


## ControlNet

| Category         | Details                                                                                       |
|------------------|-----------------------------------------------------------------------------------------------|
| **1. 人物/肖像** | **canny** (棱角分明), **lineart** (线条分明, 处理细致), **scribble** (自由发挥度高), **softedge** (保留更多细节) |
| **2. 动作**      | **openpose**                                                                                 |
| **3. 动漫**      | **lineart-anime** (处理细致)                                                                 |
| **4. 建筑**      | **depth** (深度信息多的图), **mlsd** (可出室内效果图, 也可游戏建模), **normal** (表面法线图)         |
| **5. 修图**      | **inpaint** (主要用于肖像)                                                                    |
| **6. 复杂的图**  | **seg** (处理很优秀)                                                                         |


## SD + ControlNet + realistic

|功能|操作特点|重绘范围|常用例子或使用例子|
| ---- | ---- | ---- | ---- |
|图生图|以一张已有图片为基础，结合文本提示词，生成与原图风格、内容相关但有所不同的新图片|全图|将一张风景照转换为动漫风格的风景图|
|涂鸦|无严格蒙版概念，通过笔刷颜色决定出图对应区域颜色，可自由创作|通常全图重绘|给人物图片添加简单彩色装饰，如为人物画上彩色翅膀|
|局部重绘|基于蒙版操作，笔刷涂抹确定重绘区域，不涉及颜色对内容影响|可选择蒙版或非蒙版区域重绘|修复图片中人物脸上的瑕疵、替换人物身上的衣服|
|涂鸦重绘|结合涂鸦和局部重绘特点，支持蒙版且笔刷颜色影响绘制元素颜色|根据蒙版设置对特定区域重绘|给人物图片中的头发换不同颜色，同时不改变整体画面结构|
|上传重绘蒙版|需在外部软件制作精细蒙版并上传，本质类似局部重绘|根据上传蒙版确定重绘区域|处理复杂人物或场景时，精准替换人物面部表情|
|批量处理|同时对多张图片进行相同图生图操作，需设置输入、输出目录等路径|可对批量图片指定区域按设置重绘|批量将一组人物照片的背景替换为统一的梦幻星空背景| 

## prompt

```text
[masterpiece:1.2], best quality, highres,extremely detailed CG,perfect lighting,8k wallpaper,
```

- real: photograph,photorealistic,
- comic: illustration,painting,paintbrush,
- anime: anime,comic,game CG,
- 3D: 3D,C4D render,unreal engine,octane render,

```text
# Prompt Guide and Tools

## 主体描述 (Subject Description)
### Examples:
- **人物**: Characters  
- **年龄**: Age  
- **发型**: Hairstyle  
- **头发颜色**: Hair Color  
- **情绪表情**: Emotions  
- **衣服装束**: Clothing  
- **正在干什么**: Activities  

### 场景描述 (Scene Description):
- **环境**: Environment  
- **场景**: Scene  
- **灯光**: Lighting  
- **构图**: Composition  

---

## 负面词 (Negative Prompts)
### General Quality:
- `(worst quality:2)`
- `(low quality:2)`
- `(normal quality:2)`
- `lowres`
- `normal quality`
- `(monochrome)`
- `(grayscale)`

### Skin Issues:
- `skin spots`
- `acnes`
- `skin blemishes`
- `age spot`
- `(ugly:1.331)`
- `(duplicate:1.331)`
- `(morbid:1.21)`
- `(mutilated:1.21)`
- `(translucent:1.331)`

### Hand Issues:
- `mutated hands`
- `poorly drawn hands:1.5`
- `blurry`
- `bad anatomy:1.2`
- `bad proportions:1.331`
- `extra limbs:1.331`
- `disfigured:1.331`

### Missing or Extra Parts:
- `missing arms:1.331`
- `extra legs:1.331`
- `fused fingers:1.5`
- `too many fingers:1.5`
- `unclear eyes:1.331`
- `lowers`
- `bad hands`
- `missing fingers`
- `extra digits`
- `(((extra arms and legs)))`

---

## Tools and Plugins

### 必备提示词插件 (Essential Prompt Plugins):
1. **One Button Prompt**: Simplifies the prompt generation process.  
2. **sd-dynamic-prompts**: Adds dynamic and versatile prompt capabilities for better customization.  
3. **prompt-all-in-one**
```