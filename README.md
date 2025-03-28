# timmy-project-ai

CNN을 활용한 쌀알 품종 분류 프로젝트
Rice Grain Classification using CNNs (VGG16, MobileNetV2, ResNet18)
by Timmy.park (박정민)

프로젝트 개요
이 프로젝트는 다섯 가지 쌀 품종(Arborio, Basmati, Ipsala, Jasmine, Karacadag)의 이미지를 기반으로 CNN을 활용하여 품종을 분류하는 이미지 분류 모델을 학습하고 평가한 것이다.
고품질 이미지 데이터와 다양한 모델을 실험하며, 노이즈 환경에서도 강인한 모델 구조를 탐색하고 일반화 성능을 향상시키는 것이 주요 목표이다.

데이터셋 구성
총 75,000장의 RGB 이미지 (품종당 약 15,000장)

해상도: 250x250

배경은 모두 검정색으로 통일되어 전처리 최소화 가능

클래스 간 균형 유지

Kaggle에서 다운로드 후 8:2 비율로 학습/테스트 세트 분할

사용 모델 및 학습 설정
사전 학습된 3가지 CNN 구조를 활용:

VGG-16

깊고 단순한 구조

정확도는 높지만 학습 속도가 느림

MobileNetV2

경량 구조

적은 리소스로 준수한 정확도 및 빠른 학습 속도

ResNet18

잔차 연결로 학습 안정성 확보

속도와 성능의 균형이 우수

공통 학습 설정:

Optimizer: Adam

Loss Function: CrossEntropy

Learning rate: 0.0001

EarlyStopping 적용

실험 결과 요약 (정제된 데이터셋 기준)
모델	정확도	손실	학습 시간
VGG16	99.99%	0.0004	약 1.39시간
MobileNetV2	99.79%	0.0021	약 30분
ResNet18	99.81%	0.0018	약 40분
노이즈 환경 실험
정규분포(평균=0, 분산=0.01)의 Gaussian Noise 추가

노이즈 강도 0.1로 동일 조건 하에 학습

모델	정확도	손실	학습 시간
VGG16	99.76%	0.0102	약 50분
MobileNetV2	99.90%	0.0038	약 35분
ResNet18	99.91%	0.0031	약 45분
VGG-16 모델 구조 개선 실험
VGG16-a1
일부 레이어 단순화

Dropout 0.3 적용, 특징 추출기 4 epoch 이후 fine-tuning

기존 VGG16과 성능 차이 거의 없음

VGG16-a2 (최종 개선 모델)
점진적인 채널 축소 구조 도입

GELU 활성화 함수 적용

BatchNorm + Dropout 정규화 병합

2단계 학습 전략(freeze → unfreeze) 적용

정확도 99.81%, 손실 0.0026으로 가장 안정적 성능

Feature Map 시각화
VGG16-a2 모델의 중간 convolution layer에서 feature map 시각화

쌀알의 경계를 정확히 추출하고, 배경 노이즈는 억제됨

모델 관리 및 DB 연동
모델 이름, 정확도, 손실, 버전, 경로 정보를 SQLite에 저장

모델 로딩 후 분류 테스트 가능

실험 재현성과 모델 버전 관리 목적

주요 결론
정제된 데이터셋에서는 모델 성능 차이가 작지만,
노이즈 환경에서는 구조에 따른 일반화 능력 차이가 명확히 드러남

단순한 구조 수정보다는 활성화 함수, 정규화 전략, 학습 방식 등 복합적 개선이 중요

실제 서비스 목적의 이미지 분류 모델 설계 시, 정확도뿐 아니라 견고성과 학습 안정성을 고려해야 함

주요 기술 스택
Python, PyTorch, Torchvision

SQLite

NumPy, Matplotlib, OpenCV

Kaggle API

