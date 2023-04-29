# Uncertainty-Based-Labeling-Methodology
### Related Work 1 : Active Learning
- Unlabeled Data를 순차적으로 Train Dataset에 추가함으로써 모델의 성능을 개선
- 모든 Unlabeled Data를 엔지니어가 Labeling해주어야 한다는 한계점 존재 

### Related Work 2 : Reject Option
- Uncertainty에 기반하기 때문에 엔지니어의 Labeling Cost가 상대적으로 낮음
- 최초 Train Dataset을 통해서만 Model을 학습시키기 때문에 모델 정확도가 상대적으로 낮음

두 방법론의 한계점을 개선하는 새로운 Uncertainty Based Labeling Methodology를 제안

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
### 1. Uncertainty Based Labeling Methodology
![image](https://user-images.githubusercontent.com/79157951/234301523-21c34e0c-94bf-4159-8599-686ae665e05d.png)

1. Train Dataset을 통한 모델 학습(ResNet50)
- 초기 데이터셋은 17,295개(Epoch 5)
2. 학습한 모델을 통해 Set 1 데이터 Predict 및 Softmax 값 계산
3. Softmax 값에 따라 데이터별 Uncertainty 계산
  - `Least Confidence` : Softmax 값 중 최댓값
  - `Least Margin` : Softmax 값 중 최댓값과 두번째 최댓값 차이
  - `Entropy` : Softmax 값으로 계산한 엔트로피
4. Threshold를 기준으로 Auto Labeling or Engineer Labeling 진행
- Uncertainty를 계산하는 Least Confidence, Least Margin, Entropy 각각 0.99, 0.98, 0.05의 Threshold를 지정
5. 라벨링이 완료된 Set 데이터를 Train Dataset에 추가
6. Set 데이터별로 1~5번 과정을 반복하여 Unlabeled Data 전체에 대한 라벨링 진행

### 2. Auto Labeling
- 동일한 초기 데이터셋 17,295개에 대하여 ResNet50 모델을 학습(Epoch 5)
- unlabeled data 155,655개에 대하여 Auto Labeling 진행

### 3. Reject Option(1,2,3)
- 동일한 초기 데이터셋 17,295개에 대하여 ResNet50 모델을 학습(Epoch 5)
- 남은 unlabeled data에 대하여 softmax 값을 계산하여 Threshold를 넘는 데이터는 Auto Labeling, 넘지 못하는 데이터는 Engineer가 직접 라벨링 수행

### 4. Active Learning
- Uncertainty를 계산하는 과정은 제안하고자 하는 방법론과 동일하며, 이후 엔지니어가 직접 라벨링을 진행하므로 따로 실험을 진행하지 않음

# Result
![image](https://user-images.githubusercontent.com/79157951/235137886-a66c202d-6805-4118-b606-6e219dedbf18.png)

Reject Option(1,2,3), Active Learning, Auto Labeling와 비교하였을 때 155,655개의 Unlabeled Data에 대하여
- 실제 레이블과 큰 차이가 없는 높은 Accuracy
- 엔지니어가 직접 라벨링을 해줘야하는 Engineer Cost 또한 비교 대상에 비해 현저히 적음

# Conclusion
- Uncertainty 기반 라벨링 방법론을 통해 반자동화 라벨링 모델 구축
- 세가지 불확실성 지표에 따라 라벨링 정확도는 각 99.27%, 99.34%, 99.57%를 보임
- 전체 Unlabeled Data 중 엔지니어 라벨링 비율은 각 11.25%, 11.68%, 13.74%를 보임

# Comment
- WM-811k 데이터셋의 경우 레이블별 불균형이 심한 데이터이다. 이를 적절하게 반영하기 위해 매 phase마다 train 시 train/valid를 split하는 과정에서 `random split`을 수행하는 것이 올바르지 않은 방법일 수 있다. 각 Uncertainty별 mislabeling이 약 3-400여개 되는데, 이것이 개수가 적은 레이블이 대부분일 경우 이를 고려한 split을 통해 모델을 학습할 필요가 있다.
- csv 파일은 총 3가지로 구성된다.
  - data_172950 : WM-811k 데이터셋에서 라벨링이 주어져있는 모든 데이터가 포함된 데이터셋
  - data_17295 : 각 레이블별 비율을 고려해 10%를 추출한 초기 train 데이터셋
  - data_155655 : 전체 172,950개의 데이터에서 초기 데이터 17,295개를 제외한 unlabeled data
