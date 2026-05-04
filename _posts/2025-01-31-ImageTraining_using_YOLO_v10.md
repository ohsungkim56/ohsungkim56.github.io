---
title: "YOLOY v10을 이용한 이미지 학습 (Diablo2)"
author: ohsungkim56

categories:
  [Image Processing, Object Detection]
tags:
  [YOLO, Diablo2, ObjectDetection]

toc: true

date: 2025-01-31
last_modified_at: 2026-05-05
---

## 0. 이전 포스트
[Darknet + YOLO v4 tiny 를 이용한 이미지 학습-1](/posts/Darknet-+-YOLO-v4-tiny-%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9D%B4%EB%AF%B8%EC%A7%80-%ED%95%99%EC%8A%B5-1/)

[Darknet + YOLO v4 tiny 를 이용한 이미지 학습-2](/posts/Darknet-+-YOLO-v4-tiny-%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9D%B4%EB%AF%B8%EC%A7%80-%ED%95%99%EC%8A%B5-2/)


## 1. 작업 내용

- 이전 포스트에서 사용한 학습 데이터를 사용
- YOLO v10 을 이용하여 학습
- Pre-tratined weight `yolov10n.pt` 를 사용 
- supervision을 사용하여 시각화

## 2. 코드 및 데이터

- Train images
[https://github.com/ohsungkim56/diablo2_object_dection_yolo_v10/tree/main/data/images](https://github.com/ohsungkim56/diablo2_object_dection_yolo_v10/tree/main/data/images)
<br/>
- Train labels
[https://github.com/ohsungkim56/diablo2_object_dection_yolo_v10/tree/main/data/labels](https://github.com/ohsungkim56/diablo2_object_dection_yolo_v10/tree/main/data/labels)
<br/>
- Train, validation codes (notebook)
[https://github.com/ohsungkim56/diablo2_object_dection_yolo_v10/blob/main/diablo2_object_detection.ipynb](https://github.com/ohsungkim56/diablo2_object_dection_yolo_v10/blob/main/diablo2_object_detection.ipynb)

{% include embed/notebook.html src="/assets/html/2025-01-31/diablo2_object_detection.html" %}

## 3. 학습 로그

```shell
      Epoch    GPU_mem   box_loss   cls_loss   dfl_loss  Instances       Size
  1000/1000      6.41G      1.233     0.7209      1.542        543        640: 100%|██████████| 3/3 [00:00<00:00,  3.05it/s]
                 Class     Images  Instances      Box(P          R      mAP50  mAP50-95): 100%|██████████| 1/1 [00:00<00:00,  5.68it/s]
                   all          8        564       0.98       0.96      0.985      0.906

1000 epochs completed in 1.954 hours.
Optimizer stripped from runs/detect/train16/weights/last.pt, 5.9MB
Optimizer stripped from runs/detect/train16/weights/best.pt, 5.9MB

Validating runs/detect/train16/weights/best.pt...
Ultralytics 8.3.63 🚀 Python-3.10.6 torch-2.5.1+cu124 CUDA:0 (NVIDIA GeForce RTX 3060 Ti, 8191MiB)
YOLOv10n summary (fused): 285 layers, 2,699,876 parameters, 0 gradients, 8.3 GFLOPs
                 Class     Images  Instances      Box(P          R      mAP50  mAP50-95): 100%|██████████| 1/1 [00:00<00:00,  3.42it/s]
                   all          8        564      0.983      0.955      0.986      0.916
    _flawless_amethyst          8         31          1      0.998      0.995      0.937
     _flawless_diamond          7         34      0.969      0.926      0.986      0.847
     _flawless_emerald          8         45      0.953          1      0.995      0.958
        _flawless_ruby          8         54      0.995      0.963      0.989      0.896
     _flawless_saphire          6         33      0.979       0.97      0.992       0.95
       _flawless_skull          7         43          1      0.948      0.977      0.892
       _flawless_topaz          7         41          1      0.987      0.995      0.946
     _perfect_amethyst          8         48      0.979      0.978      0.994      0.954
      _perfect_diamond          7         50      0.977      0.856      0.978      0.871
      _perfect_emerald          8         37       0.97      0.885      0.951      0.869
         _perfect_ruby          6         24          1      0.933      0.987        0.9
      _perfect_saphire          6         33          1      0.995      0.995      0.945
        _perfect_skull          8         50      0.941      0.958      0.974      0.897
        _perfect_topaz          7         41          1      0.973      0.993      0.956
Speed: 0.4ms preprocess, 2.7ms inference, 0.0ms loss, 0.2ms postprocess per image
Results saved to runs/detect/train16
💡 Learn more at https://docs.ultralytics.com/modes/train
```
## 4. 학습 결과
![confusion_matrix_normalized](/assets/img/2025-01-31/train_confusion_matrix_normalized.png)

![result](/assets/img/2025-01-31/train_result.png)

## 5. 검증 데이터 결과

![검증 데이터 1](/assets/img/2025-01-31/yolo_v10_validation_1.png)
![검증 데이터 2](/assets/img/2025-01-31/yolo_v10_validation_2.png)
![검증 데이터 3](/assets/img/2025-01-31/yolo_v10_validation_3.png)
![검증 데이터 4](/assets/img/2025-01-31/yolo_v10_validation_4.png)

## 6. 평가
![검증 데이터 1](/assets/img/2025-01-31/yolo_v10_validation_5.png)
- 반지에 있는 보석이 상급 사파이어로 검출
- 주얼이 최상급 또는 상급 보석으로 검출 (이미지가 유사)
- 비슷한 위치에 있던 흰색 주얼 중 한개는 검출되었으나, 한개는 검출되지 않음

![검증 데이터 2](/assets/img/2025-01-31/yolo_v10_validation_6.png)
- 중/하급 스컬이 상급 스컬로 검출 (중/하급 스컬은 상급 스컬에 크랙이 있는 유사한 이미지이고, 최상급 스컬은 스컬 가운데에 보석이 박혀있어 구분되는 이미지) 
- 최하/하/중급 보석과, 검출하고자 했던 상/최상급 보석을 섞은 이미지인데, 최하/하/중급 보석은 검출되지 않았지만 상/최상급 보석 일부가 검출되지 않았음

![검증 데이터 3](/assets/img/2025-01-31/yolo_v10_validation_7.png)
- 최상급 자수정, 최상급 스컬을 모아둔 이미지였는데, 사이에 있던 일부 최상급 자수정이 검출되지 않았음. 

![검증 데이터 4](/assets/img/2025-01-31/yolo_v10_validation_8.png)
- 마찬가지로 최상급 루비를 모아둔 이미지였는데, 사이에 있던 일부 최상급 루비가 검출되지 않았음음

## 7. reference

[How to Train a YOLOv10 Model on a Custom Dataset](https://blog.roboflow.com/yolov10-how-to-train)

[[Colab] train-yolov10-object-detection-on-custom-dataset.ipynb](https://colab.research.google.com/github/roboflow/notebooks/blob/main/notebooks/train-yolov10-object-detection-on-custom-dataset.ipynb?ref=blog.roboflow.com)
