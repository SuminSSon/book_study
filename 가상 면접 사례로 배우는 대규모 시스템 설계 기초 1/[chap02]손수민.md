# Chap 2. 개략적인 규모 추정

개략적인 규모 추정(back-of-the-envelope estimation)은 보편적으로 통용되는 성능 수치상에서 사고 실험(thought experiments)을 행하여 추정치를 계산하는 행위로서, 어떤 설계가 요구사항에 부합할 것인지 보기 위한 것이다.
---

**데이터 볼륨 단위** </p>
![image](https://github.com/aws-cloud-clubs/2024-khu-study/assets/56192209/1a4414c4-4e79-4df2-abc0-0aa8cc3c335a)

**모든 프로그래머가 알아야 하는 응답지연 값** </p>
![image](https://github.com/aws-cloud-clubs/2024-khu-study/assets/56192209/ce6d2b9d-27ec-4568-b6b7-ea2290dfe479) </p>
위 수치를 분석하면 다음과 같은 결론이 나온다.
1. 메모리는 빠르지만 디스크는 아직도 느리다.
2. 디스크 탐색(seek)은 가능한 한 피하라.
3. 단순한 압축 알고리즘은 빠르다.
4. 데이터를 인터넷으로 전송하기 전에 가능하면 압축하라.
5. 데이터 센터는 보통 여러 지역(region)에 분산되어 있고, 센터들 간에 데이터를 주고받는 데는 시간이 걸린다.

**가용성에 관계된 수치들** </p>
고가용성(high availability)은 시스템이 오랜 시간 동안 지속적으로 중단 없이 운영될 수 있는 능력이다.
![image](https://github.com/aws-cloud-clubs/2024-khu-study/assets/56192209/25b545df-a7c6-4230-a1be-3fc164cb4abe) </p>
