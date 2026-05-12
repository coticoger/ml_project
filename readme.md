# Drug-induced Gene Expression Change Prediction using Basic Machine Learning Models

## 1. 프로젝트 개요

본 프로젝트의 목표는 특정 disease 상태의 gene expression profile과 약물 정보를 기반으로 약물 처리 후 발생하는 gene expression 변화량을 예측하는 것이다.

최근 bioinformatics 분야에서는 deep learning 및 foundation model 기반 접근이 활발하게 연구되고 있으나, 본 프로젝트에서는 SOTA 모델이 아닌 기본적인 machine learning 모델을 사용하여 약물 처리에 따른 유전자 발현 변화 예측 가능성을 확인하고자 한다.

본 프로젝트는 다음 문제를 다룬다.

```text
Disease 상태의 gene expression profile에
특정 약물과 dose를 적용했을 때,
gene expression이 어떻게 변화하는가?
```

---

## 2. 프로젝트 목표

### 최종 목표

다음 정보를 입력으로 사용하여:

- diseased gene expression
- drug target gene position information
- drug dose

약물 처리 후의 gene expression 변화량(delta expression)을 예측하는 회귀 모델을 구축한다.

---

## 3. 데이터 구성

### 3.1 Gene Expression Data

LINCS L1000 데이터셋을 사용하여:

- diseased 상태의 gene expression
- 약물 처리 후(treated)의 gene expression
- control (`ctl_vehicle`) expression

정보를 사용한다.

---

### 3.2 Drug Target Information

ChEMBL 기반 drug-target 정보를 활용하여:

- 특정 약물이 어떤 gene/protein target을 가지는지
- target gene의 위치 정보

를 feature로 사용한다.

Drug target 정보는 gene dimension vector 형태로 변환한다.

예시:

```text
Gene A -> 1
Gene B -> 0
Gene C -> 1
...
```

---

### 3.3 Dose Information

약물 농도(dose)는 연속형 변수로 사용한다.

필요 시 다음과 같은 전처리를 수행한다.

$$
dose' = \log(1 + dose)
$$

---

## 4. 데이터 전처리

### 4.1 Control Baseline 생성

동일한 cell line에 여러 개의 `ctl_vehicle` signature가 존재하는 경우 gene별 평균값을 사용하여 baseline profile을 생성한다.

평균 baseline은 다음과 같이 계산한다.

$$
\bar{x}_g = \frac{1}{n}\sum_{i=1}^{n} x_{g,i}
$$

이를 통해 technical noise를 감소시키고 보다 안정적인 control profile을 생성한다.

---

### 4.2 Delta Expression 생성

모델은 treated expression 자체보다 변화량(delta)을 예측하도록 구성한다.

$$
\Delta x = x_{treated} - x_{diseased}
$$

최종 treated expression은 다음과 같이 계산한다.

$$
x_{predicted} = x_{diseased} + \Delta x_{predicted}
$$

---

## 5. 입력 및 출력 정의

### Input Features

```text
1. diseased gene expression vector
2. drug target gene vector
3. dose information
```

---

### Output

```text
drug 처리 후 gene expression 변화량(delta expression)
```

---

## 6. 사용 모델

본 프로젝트에서는 기본적인 machine learning 회귀 모델을 사용한다.

### 6.1 Linear Regression

가장 기본적인 선형 회귀 모델을 baseline으로 사용한다.

---

### 6.2 Ridge Regression

gene expression feature의 높은 차원성과 multicollinearity 문제를 완화하기 위해 Ridge Regression을 사용한다.

Ridge objective는 다음과 같다.

$$
\min_w ||y - Xw||^2 + \lambda ||w||^2
$$

---

### 6.3 Random Forest Regressor

비선형 관계를 학습하기 위해 Random Forest 기반 회귀 모델을 비교 대상으로 사용한다.

---

## 7. 평가 방법

모델 성능은 다음 지표를 사용하여 평가한다.

### Regression Metrics

- Mean Squared Error (MSE)
- Root Mean Squared Error (RMSE)
- Mean Absolute Error (MAE)

---

### Expression Similarity Metrics

- Pearson Correlation
- Cosine Similarity

gene expression profile은 absolute value보다 전체 패턴 유사성이 중요하므로 correlation 기반 평가를 함께 수행한다.

---

## 8. 실험 계획

### Step 1

LINCS 및 ChEMBL 데이터 전처리 수행

---

### Step 2

cell line별 control baseline 생성

---

### Step 3

drug target gene vector 생성

---

### Step 4

delta expression 생성

---

### Step 5

Regression 모델 학습

- Linear Regression
- Ridge Regression
- Random Forest Regressor

---

### Step 6

예측 성능 비교 및 분석

---

## 9. 기대 효과

본 프로젝트는 다음 의의를 가진다.

- 기본적인 machine learning 모델만으로도 drug-induced expression change 예측 가능성 확인
- drug-target 정보와 gene expression 정보를 통합한 분석 수행
- 복잡한 deep learning 없이도 bioinformatics 문제를 해결하는 baseline framework 제시

---

## 10. 한계점

본 프로젝트는 다음 제한 사항을 가진다.

- 실제 biological mechanism을 완전히 반영하지 못함
- 약물 추천(recommendation) 문제가 아닌 expression 변화 예측 문제임
- batch effect 및 experimental noise 영향 가능성 존재
- limited gene set(L1000 landmark genes) 사용

---

## 11. 결론

본 프로젝트는 diseased gene expression과 drug 정보를 기반으로 약물 처리 후의 gene expression 변화량을 예측하는 기본 machine learning framework를 구축하는 것을 목표로 한다.

특히 SOTA 모델 대신 Linear Regression, Ridge Regression, Random Forest와 같은 기본 모델을 활용하여 bioinformatics 데이터에 대한 기계학습 적용 가능성을 확인하고자 한다.
