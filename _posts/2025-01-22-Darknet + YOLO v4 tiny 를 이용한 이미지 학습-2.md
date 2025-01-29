---
title: "Darknet + YOLO v4 tiny 를 이용한 이미지 학습-2"
excerpt: ""
author: ohsungkim56

categories:
  [Image Processing, Object Detection]
tags:
  [YOLO, Diablo2, ObjectDetection]

toc: true

date: 2025-01-22
last_modified_at: 2025-01-29
---

## 0. 확인하고 싶은 것

1. 실제 게임에서 얻은 이미지가 아니라, 만들어진 게임 이미지로도 학습이 되는지?

2. 해상도, 감마, 대비, 확대, 축소 등의 변화에도 오브젝트 인식이 가능한지?

## 1. 실제 케이스에서의 테스트 (학습/검증 외 데이터, 일반 영상)

- OpenCV를 이용해 동영상 캡쳐 -> bounding box draw -> 출력

- Thresold는 0.5로 설정.

- NMS는 사용하지 않음.

### 1-1. 사용한 코드

[https://github.com/ohsungkim56/diablo2_object_dection/blob/main/4_yolo_opencv_detector.ipynb](https://github.com/ohsungkim56/diablo2_object_dection/blob/main/4_yolo_opencv_detector.ipynb)

### 1-2. 테스트

- 게임 내에서 사용한 여러 종류의 보석(`Game assets` + other assets)을 포함하는 영상

{% include embed/youtube.html id='wsYWGh9KqJA' %}

### 1-3. 평가

- 창고(영상 좌측)에 있는 아이템에 `퍼펙트 루비` 여러개가 있는데 일부는 검출되지 않음. 

- 인벤토리(영상 우측)에 있는 아이템 중 일부는 바운딩 박스 두개가 합쳐진듯한 모습. (바운딩 박스 2개가 합쳐진 듯)

- 우측 중간에 있는 `주얼`의 경우, `퍼펙트 루비`와 매우 유사한데 검출되지 않음. (Thresold를 낮춰도 검출되지 않음)


## 2. 명도/대비/채도/색상 변화에 따른 검출

### 2-1. 사용한 코드

- 동일

### 2-2. 테스트

- 1과 동일한 영상, 명도/대비/채도/색상 변경하여 테스트

- 팟플레이어의 기능 이용하여 명도/대비/채도/색상 변경

{% include embed/youtube.html id='xzR2QghtM8s' %}

### 2-3. 평가

- 명도 증가 : 명도를 올리니, 영상이 전체적으로 밝아짐. 영상의 일부 색이 남아있는 부분에서만 검출됨. (노란색 `토파즈`, 초록색 `에메랄드` 위주로 검출)

- 명도 감소 : 명도를 내리니, 영상이 전체적으로 어두워져서 영상의 일부 색이 남아있는 부분에서만 검출됨. (빨간색 `루비` 위주로 검출)

- 대비 증가/감소 : 거의 검출되지 않음. 아이템 색상과 영상에서의 아이템 색상이 많이 달라서 그런듯. (그나마 검출된 것도 conf 값 낮음)

- 채도 증가 : 크게 영향 받지 않은 것으로 보임. (원래 가지고 있던 색상이 강조됨)

- 채도 감소 : 흑백 영상이 되어버려서, 원래 흰색 위주의 아이템이었던 `퍼펙트 다이아몬드` 만 검출됨. 
 
- 색상 증가 : 원본 아이템 이미지와 다른 색상이 되어서 다른 클래스로 검출됨.

- 색상 감소 : 원본 아이템 이미지와 다른 색상이 되어서 다른 클래스로 검출됨.

## 3. 확대/축소 변화에 따른 검출

### 3-1. 사용한 코드

- 동일

### 3-2. 테스트

- 1과 동일한 영상, 확대/축소/가로 늘리기/세로 늘리기 하여 테스트

- 팟플레이어의 기능 이용하여 확대/축소/늘리기

{% include embed/youtube.html id='kkjV8RCmdhc' %}

### 3-3. 평가

- 확대 : Conf가 잘 안보이지만 확대 될 수록 conf가 점차 낮아지는 듯. 오히려 검출 안되던 부분도 검출됨. 

- 축소 : 축소할수록 conf 가 점차 낮아지면서, thresold 밑의 conf를 가진 바운딩 박스는 검출 안됨.

- 가로/세로 늘리기 : conf는 낮아졌지만 여전히 검출 가능. (thresold 값에 따라 검출 안되었을 수도 있었을 것으로 보임)

## 4. 종합 평가

```1. 실제 게임에서 얻은 이미지가 아니라, 만들어진 게임 이미지로도 학습이 되는지?```

-> 학습 가능. 단, 실제 환경에서 존재할 수 없는 케이스가 만들어지지 않도록 조심해야 할 듯.

```2. 해상도, 감마, 대비, 확대, 축소 등의 변화에도 오브젝트 인식이 가능한지?```

-> 가능하지만 conf가 작아지므로, 적절한 thresold 설정 필요.


## 5. 후기

- 객관적인 비교 지표 설정 필요. (실제 오브젝트 갯수, 바운딩 박스 갯수 등)

- 영상보다 고정 이미지로 비교하는게 편할 듯..

## 6. reference

[[Youtube]Build an Object Detector for Any Game Using YOLO](https://www.youtube.com/watch?v=RSXgyDf2ALo)

[[Github] yolo-opencv-detector](https://github.com/moises-dias/yolo-opencv-detector/tree/main)

[[Github] pjreddie/darknet](https://github.com/pjreddie/darknet)

[[Github] AlexeyAB/darknet (forked from pjreddie/darknet)](https://github.com/AlexeyAB/darknet)
