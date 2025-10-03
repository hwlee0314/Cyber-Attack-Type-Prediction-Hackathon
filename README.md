<img width="1054" height="816" alt="Image" src="https://github.com/user-attachments/assets/68e924ae-1f03-489e-8a64-d3331bb117f6" />  

# 사이버 공격 유형 예측 해커톤: 트래픽 속 위협을 식별하라!  

### **2025-06-02 ~ 2025.07.31**
### [Competition & Data Link](https://dacon.io/competitions/open/236502/overview/description)
- 사이버 위협 탐지 종류 예측 AI 알고리즘 개발
- 네트워크 트래픽 데이터를 기반으로 각기 다른 사이버 공격 유형을 예측


- 평가 산식: macro f1-score  

- 3rd out of 213 teams 🏆

- 주최: 데이콘  
---

### **주요 특징**

- **5-Fold Stratified Cross Validation**: **LightGBM** 모델을 5-fold 교차 검증으로 학습하여 클래스 불균형 문제를 해결하고 안정적인 예측 성능을 확보했습니다.
- **네트워크 피처 엔지니어링**: IP 주소에서 서브넷 정보를 추출하고, 네트워크 트래픽의 송신/수신 비율 및 차이를 계산하여 공격 패턴을 식별했습니다.
- **클래스 불균형 대응**: 클래스별 가중치를 자동 계산하여 불균형한 공격 유형 데이터셋에서도 효과적으로 학습할 수 있도록 했습니다.
- **도메인 특화 피처**: 페이로드 크기, 패킷 전송률, TCP 윈도우 크기 등 네트워크 보안 도메인 지식을 활용한 파생변수를 생성했습니다.
- **조기 종료 및 정규화**: Early Stopping과 L1/L2 정규화를 통해 과적합을 방지하고 일반화 성능을 향상시켰습니다.

---

### **개발 환경**

- **운영체제**: macos 26.0
- **언어**: Python 3.11

---

### **프로젝트 구조**

```
Dacon_Cyber_Attack_Type_Prediction_Hackathon/
├── train.ipynb          
├── outputs/
│   └── lgbm_kfold_submission_stop.csv
├── README.md           
└── requirements.txt    
```

---

### **모델링 접근법**

네트워크 트래픽 데이터를 바탕으로 사이버 공격 유형을 분류하기 위해 LightGBM 기반의 다중 분류 모델을 구축했습니다.

***

### 1. 데이터 전처리 파이프라인
- **IP 주소 전처리**: IP 주소의 앞 2블록만 추출하여 서브넷 단위로 변환
- **범주형 데이터 인코딩**: IP 주소와 프로토콜을 Label Encoding으로 처리
- **결측치 처리**: 수치형 변수는 평균값으로, IP 주소는 'Missing'으로 대체
- **파생변수 생성**: 페이로드 차이/합계, 패킷 비율, 바이트/패킷 전송률 비율 등

### 2. 모델 학습 전략
- **LightGBM Classifier** 사용 (다중 분류)
- **5-Fold Stratified Cross Validation**으로 클래스 불균형 문제 해결
- **클래스 가중치** 적용으로 불균형 데이터셋 대응
- **Early Stopping**으로 과적합 방지

### 3. 주요 하이퍼파라미터
```python
best_params = {
    'n_estimators': 1685,
    'learning_rate': 0.06555405498060013,
    'num_leaves': 65,
    'max_depth': 10,
    'min_child_samples': 24,
    'subsample': 0.8484011539017597,
    'colsample_bytree': 0.7337216490210687,
    'reg_alpha': 0.43019239671249737,
    'reg_lambda': 0.9937168583657578
}
```

---

### **파일 설명**

#### `train.ipynb`
- **데이터 로딩**: 학습/테스트 데이터 및 제출 파일 템플릿 불러오기
- **데이터 전처리**:
  - IP 주소 서브넷 추출 (`extract_subnet` 함수)
  - 범주형 변수 Label Encoding (IP 주소, 프로토콜)
  - 수치형 변수 결측치 평균 대체
- **파생변수 생성**:
  - `payload_diff`: 송신/수신 페이로드 평균 차이
  - `payload_sum`: 송신/수신 페이로드 평균 합계
  - `pkt_ratio`: 송신/수신 패킷 수 비율
  - `rate_byte_ratio`: 송신/수신 바이트 전송률 비율
  - `rate_pkt_ratio`: 송신/수신 패킷 전송률 비율
  - `tcp_win_diff`: TCP 윈도우 크기 차이
- **LightGBM 5-Fold CV 학습**: 
  - Stratified K-Fold로 클래스 균형 유지
  - 클래스 가중치 적용으로 불균형 문제 해결
  - Early Stopping으로 과적합 방지
- **모델 평가**: OOF 예측으로 Macro F1 Score 계산
- **제출 파일 생성**: `lgbm_kfold_submission_stop.csv` 저장

#### `data/`
- `train.csv`: 네트워크 트래픽 학습 데이터 (ID, 네트워크 피처들, attack_type)
- `test.csv`: 네트워크 트래픽 테스트 데이터 (ID, 네트워크 피처들)
- `sample_submission.csv`: 제출 파일 템플릿 (ID, attack_type)

#### `outputs/`
- `lgbm_kfold_submission_stop.csv`: LightGBM 5-Fold CV 최종 예측 결과


