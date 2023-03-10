![](https://t1.kakaocdn.net/thumb/R1920x0.fwebp.q100/?fname=https%3A%2F%2Ft1.kakaocdn.net%2Fkakaocorp%2Fkakaocorp%2Fadmin%2Fservice%2Fa85d0594017900001.jpg)

## 목차
1. 들어가며
2. 추천 모델
3. 협업 필터링(Collaborative Filtering, CF) 모델
4. 어떤 CF 모델을 선택할 것인가(1): 피드백(Feedback) 데이터
5. 어떤 CF 모델을 선책할 것인가(2): 메트릭(Metrics)
6. 어떤 CF 모델을 선택할 것인가(3): Bias & Feedback Loop

## 1. 들어가며
- 협업 필터링(Collaborative Filtering, CF)을 활용하여 새로운 추천 모델을 만들어야 한다고 생각해보자.
- 가장 중요하게 고민할 부분은 "어떤 문제를 풀까" 이다.
- 먼저 추천 모델의 종류를 알아보고, 이중 추천 시스템에서 가장 많이 활용되고 있는 협업 필터링(CF)가 무엇인지 살펴보자

## 2. 추천 모델
- 추천 모델은 크게 두 가지로 나눌 수 있다.
    - 유저, 아이템 상호작용 데이터를 활용하는 협업 필터링 모델
    - 유저 및 아이템의 텍스트 및 이미지 정보 등을 활용하는 콘텐츠 기반 필터링 모델
- CF 모델의 핵심 가정은 나와 비슷한 취향을 가진 유저들은 어떠한 아이템에 대해 비슷한 선호도를 가질 것이라는 점.
- 신규 아이템인 경우, 유저와의 상호작용 데이터가 없기 때문에 CF 모델에서 추천하기 어렵지만, CB 모델에서는 가능하다.
- 이처럼 CF와 CB는 서로 상호 보완적인 역할을 한다고 볼 수 있다.
- 이 중에서, 추천 시스템에서 가장 많이 사용되는 기술인 만큼 CF 모델에 초점을 맞춰 보겠다.

## 협업 필터링(CF)
- 추천에서 가장 많이 사용되는 기술로, 유저-아이템 간 상호 작용 데이터를 활용하는 방법론
- 즉, “이 영화를 좋아했던 다른 사람들은 또 어떤 영화를 좋아해요?”

## 콘텐츠 기반 필터링(CB)
- 콘텐츠 자체를 분석해 유사한 콘텐츠를 찾는 기술로, 대상 자체의 특성을 바탕으로 추천하는 방법론.
- 즉, “라디오헤드를 좋아하는 사람은 콜드플레이도 좋아하지 않을까요?”

## 3. 협업 필터링(Collaborative Filtering, CF) 모델
- CF 모델은 크게 두 가지 접근 방법으로 나뉜다.
    - 메모리 기반의 접근 방식
    - 모델 기반의 접근 방식

## 메모리 기반의 접근 방식
- 가장 전통적인 접근 방식
- 유저 간/아이템 간 유사도를 메모리에 저장해두고 있다가, 특정 유저에 대하여 추천이 필요할 때 해당 유저와 유사한 k 명의 유저가 소비한 아이템들을 추천하거나, 혹은, 특정 아이템에 대한 Rating 예측이 필요할 때 해당 아이템과 유사한 k 개의 아이템의 Rating을 기반으로 추정을 할 수 있습니다.

## 모델 기반의 접근 방식
- Latent Factor 방식과 Classification/Regression 방식 및 딥러닝을 사용한 접근 등 다양한 접근 방식이 있습니다. 

## 4. 어떤 CF 모델을 선택할 것인가(1): 피드백(Feedback) 데이터
- CF 모델은 피드백 데이터의 종류에 따라서 모델의 선택이 달라질 수 있디.
- 암시적 피드백(implicit Feedback)의 이러한 특성을 잘 고려한 모델이 흔히 ALS(Alternating Least Squares)라 불리는 모델이다.
- ALS 모델은 단순히 관찰된 피드백뿐만 아니라 관찰되지 않은 제로 피드백의 경우에도 학습 과정에 반영 가능
- ALS
    - 교채 최소 제곱법
    - 목적함수를 최적화하는 기법으로, 사용자와 아이템의 Latent Factor를 한 번씩 번갈아가며 학습시킴
    - 아이템의 행렬을 상수로 놓고 사용자 행렬을 학습시키고, 사용자 행렬을 상수로 놓고 아이템 행렬을 학습시키는 과정을 반복함으로써 최적의 Latent Factor를 학습시키는 방법
    
## 5. 어떤 CF 모델을 선책할 것인가(2): 메트릭(Metrics)
- CF 모델을 어떤 기준으로 평가하느냐에 따라서도 모델의 선택이 달라질 수 있으며 최적화 해야하는 메트릭도 달라질 수 있다.
- 하나의 모델을 학습할 때, 여러 가지 메트릭 중에서 어떤 메트릭은 개선이 되어도, 다른 메트릭에서는 개선이 일어나지 않은 경우도 종종 있다.
- 예를 들어, 예측값과 실제 클릭 여부(0, 1) 사이의 차이를 측정하는 지표인 Log Loss가 줄어들어도 랭킹은 그대로일 수 있고, 따라서 랭킹 메트릭은 개선이 되지 않을 수도 있다.
- 이처럼, 여러 메트릭을 측정하더라도, 현재 문제 상황에서 가장 중요한 개선 메트릭이 무엇인지를 파악하는 것이 매우 중요하다.
- 많은 추천 모델들이 랭킹 문제를 풀고 있지만, 랭킹 자체를 최적화하기보다는, 아이템 한 개와 관련된 Pointwise Loss의 최적화를 통해 랭킹 메트릭의 최적화 도한 기대한다.
- 즉, 특정 아이템에 대해 예측한 값이 실제 값과 얼마나 다른지를 Loss로 정의하고, 그 Loss를 최소화하는 방식으로 작동한다.
- 그러나 BPR의 경우, 단순히 아이템 한 개가 아니라, 선호하는 아이템과 선호하지 않는 아이템 페어를 활용하여, 선호하는 아이템이 더 상위에 랭크되는지를 측정하는 메트릭인 AUC를 직접 최적화하는 학습 프레임워크를 제공한다.
- 즉, 선호하는 아이템과 선호하지 않는 아이템의 예측값 사이의 차이를 최대한 벌리는 방식으로 작동한다.

## 6. 어떤 CF 모델을 선택할 것인가(3): Bias & Feedback Loop
- 마지막으로 고려할 점은, 추천 시스템에서 학습에 활용하는 피드백 데이터는 본질적으로 실험 데이터가 아닌 관찰 데이터라는 점이다.
- 즉, 통제된 환경이 아닌, 여러 가지 요인에 의해 데이터 수집에 영향을 받아 다양한 Bias가 끼어있는 데이터이다.

## 참고자료 출처
[https://tech.kakao.com/2021/10/18/collaborative-filtering/](https://tech.kakao.com/2021/10/18/collaborative-filtering/)