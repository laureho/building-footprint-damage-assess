# Building Footprint Detection and Damage Assessment from Satellite Images

Quick attempt at generating building footprints of a selection of .jpeg images under `data/` according to instructions in `Task.md`

## Provided data

- .jp(e)g format, RGB, contains no CRS/GPS coordinates
- Different view angles observed in `image_1`, `image_2`, `image_4`, `image_6`
- High prevalence of grasslands/fields in `image_3`
- Higher building density in `image_2` and `image_5`

## Github repos considered

**oriented object detection**
- https://github.com/avijay24/AerialObjectDetection_FasterRCNN_YOLOv5_on_DOTA
- https://github.com/csuhan/s2anet
- https://github.com/Jittor/JDet
- https://github.com/sentinel-hub/hiector: Sentinel-2/Airbus Pleiades satellite

**building segmentation**
- https://github.com/SpaceNetChallenge/BuildingDetectors_Round2/tree/master/1-XD_XD
- https://github.com/Rim-chan/SpaceNet7-Buildings-Detection
- https://github.com/fuzailpalnak/building-footprint-segmentation/tree/main

## Task requirements

- Imported the .jp(e)g images from `data/`
- Because image quality is low (overall blur), used edge filter to sharpen, exported the processed images for later reuse

1. Object Detection
Notebook: obj_detection.ipynb - Run in Colab on T4 GPU
- Tested 2 pre-trained models from the [MMRotate open-source toolbox](https://github.com/open-mmlab/mmrotate) for rotated object detection with mixed results; [the Rotation-equivariant Detector (ReDet)](https://github.com/open-mmlab/mmrotate/blob/main/configs/redet/README.md) showed marginally better object detection
- `image_6`: large planes easily identified, but very small planes are not detected; the majority of cars also remains undetected
    - suggestion: crop image into two halves, then divide the right half into smaller patches for detection


2. Building Footprint Segmentation
Notebook: sem_seg.ipynb - Run from within a `conda` env installed using these instructions:
```
git clone https://github.com/laureho/building-footprint-damage-assess.git
cd building-footprint-damage-assess/
conda env create -f environment.yml
```

- Datased used: Inria Aerial Image Labeling (https://project.inria.fr/aerialimagelabeling/)
- Model architecture: U-Net with Resnet50 backbone pre-trained on [ImageNetv2](https://pytorch.org/vision/stable/models.html)
- Finetuned U-Net on Inria Aerial Image Labeling dataset for 100 epochs
    pink = 1st session, yellow = 2nd session, resumed training of model from checkpoint of 1st
    ![training jaccard index curve](/images/unet_train_jaccard-plot.png)
    ![validation jaccard index curve](/images/unet_val_jaccard-plot)
- Precision of building footprint prediction was notably better for `image_2` and `image_5` where there's a high prevalence of buildings with red/white roofs, but over-segmentation of roads and ground terrain was common
- Prediction on original vs. sharpened images:
    - Severe problem of under-segmentation was observed when using the sharpened versions of `image_1`, `image_3`, `image_4`, when using the original versions certain building footprints were at least correctly segmented (in `image_1` & `image_3`) but over-segmentation of roads and barren fields was also prominent

- Suggestions for improvement:
    - Introduce data augmentations during training
    - Train for higher number of epochs, e.g. 300, to assess when validation metric actually gets worse
    - Try different loss: e.g. Focal loss - deal with class-imbalance for images with fewer buildings
    - Try different model architecture: e.g. DeepV3
    - Improve training dataset by incorporating imagery with different view angles
    - Train network on matching SAR data to deal with the issue of clouds/smoke and building shadows in .jpg data

- Additional future steps:
    - Clean up segmentation mask by removing small regions below a certain area threshold & very large regions above a certain area threshold
    - Simplify shapes of building footprints using [`shapely`](https://shapely.readthedocs.io/en/stable/manual.html#object.simplify)
    - Extract bounding box coordinates from each building mask
    - Crop out individual buildings
    - Generate same crops from pre-disaster images matching the provided .jp(e)g images

3. Building Damage Assessment Plan
- Download xBD dataset (https://xview2.org/dataset) & pre-trained baseline model provided by xView2 Challenge
- Download additional Maxar Open Data from Turkey and Syria Earthquake 2023 (https://www.maxar.com/open-data/turkey-earthquake-2023) + use annotations from [this repo](https://github.com/blackshark-ai/Turkey-Earthquake-2023-Building-Change-Detection/tree/main) as starting point to generate new ground truth data
- Adjust Joint Damage Scale (4 classes) introduced in the xBD dataset to the 5-class assessment scale required by incorporating on-the-ground data that is annotated according to e.g. the EMS-98 classes
- Train a damage classification network on pairs of pre vs. post-disaster building crops, then use model in inference mode on pairs of crops obtained from subtask 2\.
