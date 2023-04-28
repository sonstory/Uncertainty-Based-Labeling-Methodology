# Uncertainty-Based-Labeling-Methodology
Cost-Effective Labeling Methodology Based on Uncertainty &amp; Active Learning

# Data
- Kaggle에서 제공하는 실제 제조 현장 46,393개의 lots에서 수집된 811,457개의 반도체 웨이퍼 맵 이미지 데이터셋(WM-811K) 활용

![image](https://user-images.githubusercontent.com/79157951/234300440-a103ca5b-8679-467f-89f0-09f5d762717a.png)

# Data preprocessing
### Dataset
- 각 웨이퍼의 크기가 224x224로 통일되도록 스케일링
- 0:Wafer가 없는 영역/ 1: 양호 Chip / 2 : 불량 chip으로 나눠지도록 [224x224x3] 차원의 이미지 형태로 변경

### Data Segmentation
- 172,950장 중 고장 패턴 비율을 고려하여 10%를 추출하고 Train dataset으로 정의
- 나머지 155,655장의 data는 라벨을 가려 Test data로 사용
- Test data를 Random하게 9개의 set으로 분할하고 순차적으로 라벨링 진행

![image](https://user-images.githubusercontent.com/79157951/234301405-23194ae6-dfcc-49e1-b3bd-74eab5e12658.png)

# Experiment

![image](https://user-images.githubusercontent.com/79157951/234301523-21c34e0c-94bf-4159-8599-686ae665e05d.png)

1. Train Dataset을 통한 모델 학습(ResNet50)
2. 학습한 모델을 통해 Set 1 데이터 Predict 및 Softmax 값 계산
3. Softmax 값에 따라 데이터별 Uncertainty 계산
4. Threshold를 기준으로 Auto Labeling or Engineer Labeling 진행
5. 라벨링이 완료된 Set 데이터를 Train Dataset에 추가
6. Set 데이터별로 1~5번 과정을 반복하여 Unlabeled Data 전체에 대한 라벨링 진행

# Result
![image](https://user-images.githubusercontent.com/79157951/235137886-a66c202d-6805-4118-b606-6e219dedbf18.png)

Reject Option(1,2,3), Active Learning, Auto Labeling와 비교하였을 때 155,655개의 Unlabeled Data에 대하여
- 실제 레이블과 큰 차이가 없는 높은 Accuracy
- 엔지니어가 직접 라벨링을 해줘야하는 Engineer Cost 또한 비교 대상에 비해 현저히 적음

# Conclusion
- Uncertainty 기반 라벨링 방법론을 통해 반자동화 라벨링 모델 구축
- 세가지 불확실성 지표에 따라 라벨링 정확도는 각 99.27%, 99.34%, 99.57%를 보임
- 전체 Unlabeled Data 중 엔지니어 라벨링 비율은 각 11.25%, 11.68%, 13.74%를 보임
