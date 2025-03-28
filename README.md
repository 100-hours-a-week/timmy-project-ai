# timmy-project-ai
# CNN을 활용한 쌀알 품종 분류 프로젝트

**Rice Grain Classification using CNNs (VGG16, MobileNetV2, ResNet18)**  
by Timmy.park (박정민)

---

## 프로젝트 개요

이 프로젝트는 다섯 가지 쌀 품종(Arborio, Basmati, Ipsala, Jasmine, Karacadag)의 이미지를 기반으로 CNN을 활용하여 품종을 분류하는 이미지 분류 모델을 학습하고 평가한 것입니다.  
고품질 이미지 데이터와 다양한 모델을 실험하며, 노이즈 환경에서도 강인한 모델 구조를 탐색하고 일반화 성능을 향상시키는 것이 주요 목표입니다.

---

## 데이터셋 구성

- 총 75,000장의 RGB 이미지 (품종당 약 15,000장)
- 해상도: 250x250
- 배경은 모두 검정색으로 통일되어 있어 전처리 최소화 가능
- 클래스 간 균형 유지
- Kaggle에서 다운로드 후 8:2 비율로 학습/테스트 세트 분할

---

## 사용 모델 및 학습 설정

사전 학습된 3가지 CNN 구조를 실험하였습니다:

### VGG-16
- 깊고 단순한 구조
- 높은 정확도
- 다소 느린 학습 속도

### MobileNetV2
- 경량 구조
- 빠른 학습 속도
- 준수한 정확도

### ResNet18
- 잔차 연결로 학습 안정성 확보
- 속도와 성능의 균형

### 공통 학습 설정
- Optimizer: Adam  
- Loss Function: CrossEntropy  
- Learning rate: 0.0001  
- EarlyStopping 적용

---

## 실험 결과 (정제된 데이터 기준)

| 모델        | 정확도   | 손실    | 학습 시간   |
|-------------|----------|---------|-------------|
| VGG16       | 99.99%   | 0.0004  | 약 1.39시간 |
| MobileNetV2 | 99.79%   | 0.0021  | 약 30분     |
| ResNet18    | 99.81%   | 0.0018  | 약 40분     |

---

## 노이즈 환경 실험

- 정규분포(평균=0, 분산=0.01)의 Gaussian Noise 추가
- 노이즈 강도 0.1로 학습 진행

| 모델        | 정확도   | 손실    | 학습 시간   |
|-------------|----------|---------|-------------|
| VGG16       | 99.76%   | 0.0102  | 약 50분     |
| MobileNetV2 | 99.90%   | 0.0038  | 약 35분     |
| ResNet18    | 99.91%   | 0.0031  | 약 45분     |

---

## VGG-16 구조 개선 실험

### VGG16-a1
- 일부 레이어 단순화
- Dropout 0.3, 4 epoch 이후 fine-tuning
- 성능 향상 미미

### VGG16-a2 (최종 개선 모델)
- 채널 점진 축소 구조
- 활성화 함수: GELU
- 정규화: BatchNorm + Dropout
- 학습 전략: 2단계(freeze → unfreeze)
- 정확도 99.81%, 손실 0.0026으로 가장 우수

---

## Feature Map 시각화

- VGG16-a2 모델의 중간 convolution layer에서 feature map 추출
- 쌀알 윤곽을 명확히 감지하며 배경 노이즈 억제 확인

---

## 모델 관리 및 DB 연동

- SQLite 데이터베이스에 모델 메타정보 저장
- 저장 정보: 모델명, 정확도, 손실, 버전, 경로
- DB에서 모델 로딩 후 테스트 이미지 분류 가능

---

## 주요 결론

- 모델 간 성능 차이는 노이즈 환경에서 더욱 명확히 드러남
- 구조 개선은 단편적인 조정보다는 전체적인 학습 전략과 정규화 방식까지 고려할 필요가 있음
- 실제 서비스 목적의 모델은 정확도뿐 아니라 **노이즈 강건성, 학습 안정성**을 고려해야 함

---

## 기술 스택

- Python  
- PyTorch, Torchvision  
- SQLite  
- NumPy, Matplotlib, OpenCV  
- Kaggle API

---

## 실행 방법

```bash
# 데이터셋 전처리
python prepare_dataset.py

# 모델 학습
python train.py --model vgg16

# 모델 메타정보 DB 저장
python save_to_db.py

# 테스트 이미지 분류
python classify_image.py --image test.png
