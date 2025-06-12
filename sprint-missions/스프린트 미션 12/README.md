# 🚲 Bike Sharing Demand - 수요 예측 프로젝트

## 📌 프로젝트 개요

이번 프로젝트는 **자전거 대여 서비스의 수요 예측 모델**을 개발하여 **운영 효율성과 사용자 만족도 향상**을 목표로 진행되었습니다.

자전거 대여량은 시간, 요일, 계절, 기상 조건 등 다양한 요인에 영향을 받으며 변화하기 때문에, 이러한 복합적인 패턴을 정확히 예측하는 것이 매우 중요합니다.

본 프로젝트에서는 Kaggle의 [Bike Sharing Demand Competition](https://www.kaggle.com/competitions/bike-sharing-demand) 데이터를 활용하였습니다.

---

## 📊 데이터 구성

- **train.csv** : 자전거 대여 수량(count) + 설명 변수 포함
- **test.csv** : 설명 변수만 포함 (count 없음 → 예측 대상)

### 주요 Feature

- **시간 관련** : `year`, `month`, `day`, `hour`, `weekday`
- **카테고리형** : `season`, `holiday`, `workingday`, `weather`
- **수치형** : `temp`, `humidity`, `windspeed`
- **추가 Feature Engineering** : `humidity_mapped`, `windspeed_mapped`

👉 Target 변수는 **count → log1p(count) 변환 후 학습**, 예측 시 **역변환(expm1)** 적용.

---

## 🔍 EDA & Feature Engineering 주요 내용

- `datetime` 컬럼 → **year, month, day, hour, weekday 파생 변수** 생성
- `humidity`, `windspeed` 변수는 **구간화 적용** → 모델 학습 안정성 향상
- `count` 변수는 **log1p 변환 후 학습** → 오른쪽 치우친(skewed) 분포 보완
- 이상치 제거는 실험 결과 성능 저하 → **최종적으로 적용 X**
- Feature Importance 분석 결과 → `hour`, `workingday`, `year`, `season`, `temp` 등이 주요 Feature로 확인됨

---

## 🧪 모델 실험 결과

### 전체 모델 성능 요약

| 모델                              | RMSLE (평가 기준)              | 비고                             |
|-----------------------------------|-------------------------------|---------------------------------|
| Ridge Regression                  | 1.01444 (train)               | baseline 선형 모델               |
| Lasso Regression                  | 1.01445 (train)               | baseline 선형 모델               |
| RandomForest Regressor (기본)     | 0.10943 (train → 과적합 가능)  | validation set 없이 학습          |
| GradientBoostingRegressor         | 0.38643 (train)               | validation set 없이 학습          |
| XGBoost (기본)                    | 0.18513 (train → 과적합 가능)  | validation set 없이 학습          |
| LightGBM                          | 0.25013 (train → 과적합 가능)  | validation set 없이 학습          |
| RandomForest 튜닝 버전            | 0.35170 (validation)          | train/validation 80:20 split      |
| XGBoost 개선 튜닝 버전 (3000트리) | 0.26382 (validation)          | train/validation 80:20 split      |
| XGBoost GridSearch 튜닝 버전 (1차) | 값 입력 후 업데이트 예정        | train/validation 80:20 split      |
| RandomForest (baseline 비교)      | 0.35170 (validation)          | 동일 Feature 구성 baseline 비교   |
| XGBoost (baseline 비교)           | 0.28268 (validation)          | 동일 Feature 구성 baseline 비교   |
| LightGBM (baseline 비교)          | 0.28439 (validation)          | 동일 Feature 구성 baseline 비교   |
| **최종 Kaggle 제출 결과**        | **0.41567 (public score)**     | `final_result_xgb_submission.csv` |

---

## ✅ 최종 모델 선정 이유

- **XGBoost 개선 튜닝 버전 (3000트리)** 가 **Validation RMSLE 0.26382** 로 가장 안정적인 성능 기록
- Feature Importance 분석 결과 → 주요 Feature가 모델에 잘 반영됨 확인
- GridSearch 기반 추가 튜닝 중 → 추가 성능 개선 가능성 존재
- **Kaggle 제출 결과 (Public Score 0.41567)** 에서도 안정적인 예측 성능 확인됨

👉 현재까지 실험 결과 기준으로는 **XGBoost 개선 튜닝 버전 모델**을 최종 모델로 선정

---

## 🚀 향후 개선 방향

- **시간대별 파생 Feature 추가** → 출퇴근 피크 여부 등 반영
- **카테고리형 Feature 처리 개선** → Category boost / Target encoding 실험
- **앙상블 모델 실험** → XGBoost + LightGBM 앙상블 구성 실험
- **GridSearch 기반 XGBoost 최종 튜닝 결과 반영 예정**

---

## 🛠 사용 라이브러리

- Python 3.x
- pandas, numpy
- scikit-learn
- xgboost
- lightgbm
- seaborn, matplotlib

---

## 📌 프로젝트 진행 흐름

1️⃣ **EDA 및 데이터 전처리**  
2️⃣ **Feature Engineering 적용**  
3️⃣ **Baseline 모델 학습 및 성능 확인**  
4️⃣ **트리 기반 모델 학습 (RandomForest, XGBoost, LightGBM)**  
5️⃣ **하이퍼파라미터 튜닝 (XGBoost 개선 버전 / GridSearch 적용)**  
6️⃣ **Feature Importance 분석**  
7️⃣ **최종 Kaggle 제출 결과 확인**

---

👉 본 프로젝트는 **Feature Engineering과 하이퍼파라미터 튜닝이 예측 성능 개선에 미치는 영향**을 확인할 수 있었던 실습 사례로 정리됨.  
👉 향후 추가 개선 실험을 지속 진행할 예정입니다.

---

