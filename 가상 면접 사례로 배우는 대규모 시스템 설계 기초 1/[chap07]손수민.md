# Chap 7. 분산 시스템을 위한 유일 ID 생성기 설계

### 1단계: 문제 이해 및 설계 범위 확정
- ID는 유일해야 한다.
- ID는 숫자로만 구성되어야 한다.
- ID는 64비트로 표현될 수 있는 값이어야 한다.
- ID는 발급 날짜에 따라 정렬 가능해야 한다.
- 초당 10,000개의 ID를 만들 수 있어야 한다.

### 2단계: 개략적 설계안 제시 및 동의 구하기
**multi-master replication**

데이터베이스의 auto_increment 기능을 활용하는 것이다. 다만 ID의 값을 구할 때 데이터베이스 서버의 수 K만큼 증가시킨다(규모 확장성 문제 해결).

하지만 세 가지 단점이 존재한다.
- 여러 데이터 센터에 걸쳐 규모를 늘리기 어렵다.
- ID의 유일성은 보장되겠지만 그 값이 시간 흐름에 맞추어 커지도록 보장할 수는 없다.
- 서버를 추가하거나 삭제할 때도 잘 동작하도록 만들기 어렵다.


**UUID(Universally Unique Identifier)**

컴퓨터 시스템에 저장되는 정보를 유일학데 식별하기 위한 128비트의 수이다. UUID를 만드는 것이 단순하고 규모 확장도 쉽다. 하지만 문제의 요구사항은 64비트이고, 시간순으로 정렬할 수 없고, 숫자가 아닌 값이 포함될 수 있다.

**ticket server**

Flicker는 distributed primary key를 만들어 내기 위해 이 기술을 사용하였다. auto_increment 기능을 갖춘 티켓 서버(데이터베이스 서버)를 중앙 집중형으로 하나만 사용하는 것이다.

유일성이 보장되는 오직 숫자로만 구성된 ID를 쉽게 만들 수 있다. 구현이 쉽고 중소 규모 애플리케이션에 적합하지만 티켓 서버가 SPOF(Single-Point-of-Failure)가 된다.

**twitter snowflake 접근법**

divide and conquer을 적용하여 생성해야 하는 ID의 구조를 여러 section으로 분할한다.

![image](https://github.com/aws-cloud-clubs/2024-khu-study/assets/56192209/3ac867cd-8950-457e-bffe-c140a4b98d2b)

### 3단계: 상세 설계

타임스탬프: UTC 시각을 추출하여 타임스탬프 값으로 변환
일련번호: 밀리초 동안 하나 이상의 ID를 만들어낸 경우에만 0보다 큰 값을 갖게 된다.

### 4단계: 마무리
- clock synchronization
- 각 section의 길이 최적화
- high availability