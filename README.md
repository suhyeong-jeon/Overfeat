# Overfeat   
##### Overfeat은 fc를 conv layer로 바꾸어 여러 크기의 이미지를 입력받을 수 있도록한 구조를 가지고 있다.

### Overfeat 모델의 detection 시, 동작 순서
##### 1. 6-scale 이미지를 입력받는다.
##### 2. Classification task 목적으로 학습된 Feature extractor에 이미지를 입력하여 feature map을 얻는다.
##### 3. Feature map을 Classifier와 Bounding box regressor에 입력하여 spatial map을 출력한다.
##### 4. 예측 bounding box에 Greeedy Merge Strategy 알고리즘을 적용하여 예측 bounding box를 출력한다. 

### 1. Overfeat for Classification Task   
##### 먼저 classification을 위해 Overfeat 모델을 학습시켜야한다. 그 구조는 밑의 Overfeat 모델 사진과 같다.

### Overfeat 모델   

##### * Fast Mode
<p align="center"><img src="https://github.com/suhyeong-jeon/Overfeat/assets/70623959/230a81a5-800a-4761-93ea-fef5cc2f1e26"></p>   

##### * Accurate Mode
<p align="center"><img src="https://github.com/suhyeong-jeon/Overfeat/assets/70623959/f486ccf7-6295-45a7-a4a3-6eb8ab500c8d"></p>

##### Overfeat모델은 위 사진과 같다. AlexNet의 5 layers까지 활용했지만 contrast normalization을 사용하지 않았고 pooling시 겹치는 영역이 없도록 non-overlapping한점, 그리고 적용한 stride의 크기가 다르다.   

### 2. Overfeat for Localizatoin/Detection task   
##### Localization/Detection task시에는, Classification task를 위해 학습된 위 Overfeat 모델에서 layer5까지만 사용하고 나머지 layer는 fine tuning한다.


### Resolution Augmentation   
<p align="center"><img src="https://github.com/suhyeong-jeon/Overfeat/assets/70623959/f5a40378-5450-4e00-91ba-8623645ff478"></p> 

##### Overfast에서 핵심이 되는 개념이다. 논문의 저자는 spatial output에 한 요소가 원본 이미지의 지나치게 넓은 receptive field를 표현하면 객체를 제대로 포착하지 못한다고 이해했다. 이를 해결하기 위해 9번의 3*3 max pooling(non-overlapping)을 제시한다. 해당 max pooling을 사용하게 되면 자연스럽게 겹치는 구간의 연산을 공유하게된다. 쉽게 생각해서 spatial output은 conv layer를 통해 생성된 feature map의 부분적인 위치의 정보를 각각 담고있는것이라 이해했다.   

<p align="center"><img src="https://github.com/suhyeong-jeon/Overfeat/assets/70623959/93706ab4-b170-4131-9a6d-d4ae5252e26e"></p>

##### 위 사진은 input size에 대한 Overfeat 모델의 layer5에서 제시한 max pooling을 적용하기 전과 후, 그리고 layer6, 7, 8을 적용한 spatial dimension 표다. (accurate mode 기준)   
##### 만약 input size가 245*245라면 layer5의 3*3 conv가 적용된 후 spatial output은 17*17이다. 여기에 논문의 저자가 제시한 3*3 max pooling을 적용하면 (5*5)x(3*3)이 된다. 왜냐하면 3*3 max pooling을 하면 9개의 feature map이 나오게 되기 때문이다.    
##### 여기에 layer6가 적용이 되면 3 * 3 conv(stride=1, zero-padding), 일반적인 3 * 3 max pooling이니 (5*5)x(3*3)의 spatial output이 나온다.   
##### layer7에 3 * 3 conv(stride=1)을 설정하면 (3*3)x(3*3)의 spatial output이 나오고 layer8에 3 * 3 conv(stride=1)을 설정하면 (1*1)x(3*3)의 결과가 나온다.
##### 즉 Classifier를 통해 나온 feature map은 3*3*Class 개수가 된다. 예를들어 class 개수가 2(사자, 사과)라고 하면 3 * 3 크기의 feature map이 2개 있는것이고 첫 번째 feature map의 각 1 * 1크기의 이미지에는 사자가 receptive field내에 존재할 확률을 나타낸다. 그럼 두번째 feature map의 각각의 1*1은 각 receptive field에 사과가 존재할 확률을 가지고 있는게 될것이다.   

### 3. Training Bounding Box Regressor   
<p align="center"><img src="https://github.com/suhyeong-jeon/Overfeat/assets/70623959/a6b9209a-b9bf-4f94-a9c3-8138e0a2a3b3"></p>   

##### Bounding Box Regressor를 통한 localization은 위 과정과 비슷하다. 각 spatial map의 pixel값(1*1)은 각 class의 bounding box x1, y1, x2, y2 좌표를 나타낸다. 따라서 spatial ouptut의 channel 수는 4 * class가 된다고 한다.

### 4. Greedy Merge Strategy   
##### 여기서는 GMS를 사용해 최적의 bounding box를 찾았다는데 이 논문의 핵심내용은 아니라서 패스하겠다. 다음에 논문 발표나 정리를 해야할 기회가 있다면 다시 찾아서 정리하겠다.   

### 이슈 사항   
##### 참고한 블로그에서는 layer5 pre-pool의 out_channel을 256이라고 했는데 왜 256으로 잡았는지 아직도 이해가 안된다. 분명 Overfeat모델에서 layer5의 out_channel은 1024인데 말이다. Fine tuning도 layer6부터인데 말이다. 혹시 내가 잘못 이해한것이면 제발 나에게 연락해서 무지함을 깨우쳐주길 바란다. 이거 고민만 2시간 가까이함.   

### References   
##### <https://herbwood.tistory.com/7>
##### <https://medium.com/coinmonks/review-of-overfeat-winner-of-ilsvrc-2013-localization-task-object-detection-a6f8b9044754>
