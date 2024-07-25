# Chap 9. 웹 크롤러 설계

- Search engine indexing
- Web archiving
- Web mining
- Web monitoring

### 1단계: 문제 이해 및 설계 범위 확정

웹 크롤러 알고리즘

1. URL 집합이 입력으로 주어지면, 해당 URL들이 가리키는 모든 웹 페이지를 다운로드한다.
2. 다운받은 웹페이지에서 URL을 추출한다.
3. 추출된 URL들을 다운로드할 URL 목록에 추가하고 위의 과정을 처음부터 반복한다.


비요구사항
- 규모 확장성
- 안정성(robustness)
- 예절(politeness)
- 확장성(extensibility)

### 2단계: 개략적 설계안 제시 및 동의 구하기

![image](https://github.com/aws-cloud-clubs/2024-khu-study/assets/56192209/7bf4c458-a430-484a-95c4-fa453ceced9d)


### 3단계: 상세 설계

**DFS(Depth-First Search) vs BFS(Breadth-First Search)**

DFS는 그래프 크기가 클 경우 깊이를 가늠하기 어려워서 보통 웹크롤러는 BFS를 사용한다.

BFS는 FIFO 큐를 사용하여 한쪽에서는 탐색할 URL을 집어넣고, 다른 한쪽으로는 꺼낸다.

BFS 문제점
- 링크를 병렬로 처리할 경우 서버 과부화가 걸리게 되어 '예의 없는(impolite)'크롤러가 된다.
- BFS 알고리즘은 URL 간에 우선순위가 없다. 웹 페이지 순위, 사용자 트래픽의 양, 업데이트 빈도 등 여러 가지 척도에 비추어 처리 우선순위를 구별하는 것이 온당할 것이다.

**미수집 URL 저장소**

예의 바른 크롤러 요구 사항
- 동일 웹 사이트에 대해서는 한 번에 한 페이지만 요청하는 것

-> hostname과 다운로드를 수행하는 작업 스레드 사이의 관계를 유지하자. 각 다운로드 스레드는 별도 FIFO 큐를 가지고 있어서, 해당 큐에서 꺼낸 URL만 다운로드 한다.

![image](https://github.com/aws-cloud-clubs/2024-khu-study/assets/56192209/16cf30b4-46b8-419d-b51c-f611ccc45397)

우선순위를 위해 유용성에 따라 URL의 우선순위를 나눌 대는 패이지랭크, 트래픽 양, 갱신 빈도 등 다양한 척도를 사용할 수 있을 것이다. 순위결정장치(prioritizer)는 두어 URL 우선순위를 정하는 컴포넌트이다.

- front queue: 우선순위 결정 과정 처리
- back queue: 크롤러가 예의 바르게 동작하도록 보증

데이터의 신선함을 위해 주기적으로 재수집할 필요가 있다. 모든 URL을 재수집하는 것은 많은 시간과 자원이 들어 이 작업을 최적화하기 위해 다음과 같이 실행한다.

- 웹 페이지의 update history 활용
- 우선순위를 활용하여, 중요한 페이지는 좀 더 자주 재수집

대부분의 URL은 디스크에 두지만 IO 비용을 줄이기 위해 메모리 버퍼에 큐를 둔다. 버퍼에 있는 데이터는 주기적으로 디스크에 기록할 것이다.

**HTML 다운로더**

성능 최적화 기법

1. 분산 크롤링
2. 도메인 이름 변환 결과 캐시
3. 지역성
4. 짧은 타임아웃

**안정성 확보 전략**

- Consistent hashing
- 크롤링 상태 및 수집 데이터 저장
- Exception handling
- Data validation

**확장성 확보 전략**

- PNG 다운로더
- URL 추출기
- 웹 모니터

**문제 있는 콘텐츠 감지 및 회피 전략**

1. 중복 콘텐츠
2. 거미 덫
3. 데이터 노이즈


### 4단계: 마무리

- Server-side rendering
- 원치 않은 페이지 필터링
- 데이터베이스 다중화 및 샤딩
- 수평적 규모 확장성
- 가용성, 일관성, 안정성
- 데이터 분석 솔루션(analytics)
