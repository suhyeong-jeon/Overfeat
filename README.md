# Overfeat   

### Overfeat 모델의 detection 시, 동작 순서
##### 1. 6-scale 이미지를 입력받는다.
##### 2. Classification task 목적으로 학습된 Feature extractor에 이미지를 입력하여 feature map을 얻는다.
##### 3. Feature map을 Classifier와 Bounding box regressor에 입력하여 spatial map을 출력한다.
##### 4. 예측 bounding box에 Greeedy Merge Strategy 알고리즘을 적용하여 예측 bounding box를 출력한다.   

### Overfeat 모델
##### * Fast Mode
<p align="center"><img src="https://github.com/suhyeong-jeon/Overfeat/assets/70623959/230a81a5-800a-4761-93ea-fef5cc2f1e26"></p>   
##### * Accurate Mode
<p align="center"><img src="https://github.com/suhyeong-jeon/Overfeat/assets/70623959/f486ccf7-6295-45a7-a4a3-6eb8ab500c8d"></p>
##### Overfeat모델은 위 사진과 같다.
