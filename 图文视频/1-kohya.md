# kohya_ss

## Train Lora Sketch (素描绘画风格)

- Style: 400 images, 2 repeats
- Objects or Characters: >20 images, 10-20 repeats

- Regularization images are optional
- SDXL: 1024x1024, SD1.5: 768x768
- images counts x repeats
- prefix to WD14 caption: xyzsketchstyle
- SD1.5 和 SZDXL分别训练
- epoch: SD1.5:10, SDXL:5
- Model with the trained Lora: Photon

### 🥃 训练参数设置

`max trainign steps = (number of images x repeats x epoch) / batch size`

- **max train steps(4800) = number of images(32) x repeats(15) x epoch(10)**
- 15_ScalettJohansson

## Prepare Data

### 🥃 Search image

- google advanced image search： >1024x768
- unsplash
- pixabay
- colab

### photo booth

### disk size: ncdu

