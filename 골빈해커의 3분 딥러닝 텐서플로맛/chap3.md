# 텐서플로 프로그래밍 101

```python
import tensorflow as tf

hello = tf.constant('Hello, TensorFlow!')
print(hello)
```
-> tf.Tensor(b'Hello, TensorFlow!', shape=(), dtype=string)

Rank: 0이면 스칼라, 1이면 벡터, 2이면 행렬 3이상이면 n-Tensor도는 n차원 텐서라고 부름

Shape: 각 차원의 요수 개수
```python
3 # 랭크가 0인 텐서; 셰이프는[]
[1. ,2., 3.] #랭크가 1인 텐서; 셰이프는 [3]
[[1. ,2., 3.], [4., 5., 6.]] #랭크가 2인 텐서; 셰이프는 [2,3]
```

텐서플로 구조
1. 그래프 생성
2. 그래프 실행

- 그래프는 텐서들의 연산 모음
- 지연 실행 방식 (함수형 프로그래밍에서 많이 사용함)
- 그래프의 실행은 Session 안에서 이뤄져야 하며, Session 객체와 run 메서드 이용 -> TensorFlow 2.x에서 tf.Session() 없어짐. 즉시 실행 모드로 Session 객체 없이 작업 가능

---

- 플레이스홀더: 그래프에 사용할 입력값을 나중에 받기 위해 사용하는 매개변수
- 변수: 그래프를 최적화하는 용도로 텐서플로가 학습한 결과를 갱신하기 위해 사용하는 변수

X = tf.placeholder(tf.float32, [None, 3])
-> TensofFlow 2.x에서 사라짐. tf.Tensor 사용

예시: ```X = tf.keras.Input(shape=(3,), dtype=tf.float32)```

```python
X = tf.constant([[1.0, 2.0, 3.0], [4.0, 5.0, 6.0]], dtype=tf.float32)
W = tf.Variable([[0.1, 0.1], [0.2, 0.2], [0.3, 0.3]])
b = tf.Variable([[0.1], [0.1]])

expr = tf.matmul(X, W) + b

print(expr)
```

---
### 선형 회귀 모델 구현

- 손실 함수: 한 쌍(x,y)의 데이터에 대한 손실값 계산
- 비용: 손실을 전체 데이터에 대한 구한 경우
- 학습: 변수들의 값을 다양하게 넣어 계산해보면서 손실값을 최소화하는 W와 b의 값을 구하는 것
- learning rate: 값이 크면 최적의 손실값 못 찾고, 너무 작으면 학습 속도가 느림
- hyperparameter: 값에 따라 학습 속도나 신경망 성능이 크게 달라짐