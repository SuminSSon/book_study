# Chap 10. 알림 시스템 설계
- 정보를 비동기적으로 제공
- 모바일 푸시 알림, SMS 메시지, 이메일

### 1단계: 문제 이해 및 설계 범위 확정
- 하루 백만 건 이상의 알림 처리
- soft real-time 시스템: 가능한 빨리 전달되어야 하지만 시스템에 높은 부하가 걸렸을 때 약간의 지연은 무방함
- 클라이언트 애플리케이션과 서버측에서 스케쥴링을 통해 알림 전송

### 2단계: 개략적 설계안 제시 및 동의 구하기

**알림 유형별 지원 방안**
iOS 푸시 알림
- 알림 제공자(provider): 알림 요청을 만들어서 애플 푸시 알림 서비스(APNS)로 보내주는 주체
    - device token, payload 필요
    ```jSON
    {
        "aps": {
            "alert": {
                "title": "Game Request",
                "body": "Bob wants to play chess",
                "action-loc-key": "PLAY"
            },
            "badge":5
        }
    }
    ```
- APNS: 애플이 제공하는 원격 서비스
- iOS 단말: 푸시 알림을 수신하는 사용자 단말

안드로이드 푸시 알림
- APNS 대신 FCM(Firebase Cloud Messaging)을 사용한다는 점만 다르다.

SMS 메시지
- 트윌리오(Twilio), 넥스모(Nexmo) 같은 제3사업자의 서비스를 많이 이용한다.

이메일
- 유명한 상용 이메일 서비스로 센드그리드(Sendgrid), 메일침프(Mailchimp)가 있다.
- 전송 성공률도 높고, 데이터 분석 서비스(analytics)도 제공한다.

![image](https://github.com/user-attachments/assets/277b1c1b-5164-4a3e-9499-dc15f5fc4dbc)


**연락처 정보 수집 절차**

처음 계정 등록하면 API 서버는 해당 사용자의 정보를 수집하여 데이터베이스에 저장한다. 한 사용자가 여러 단말을 가질 수 있고, 알림은 모든 단말에 전송되어야 한다는 점을 고려하였다.

**알림 전송 및 수신 절차**

- 1부터 N까지의 서비스
- 알림 시스템(notification system)
- 제3자 서비스(third party services)

![image](https://github.com/user-attachments/assets/5ec90304-d13a-4c26-bf6a-89595f38c832)

설계의 문제점
- SPOF(Single-Point-Of-Failure)
- 규모 확장성
- 성능 병목

개선된 버전

- 데이터베이스와 캐시를 알림 시스템의 주 서버에서 분리
- 알림 서버를 증설하고 자동으로 수평적 규모 확장이 이루어질 수 있도록 한다.
- 메시지 큐를 이용해 시스템 컴포넌트 사이의 강한 결합을 끊는다.

![image](https://github.com/user-attachments/assets/3bfc4012-752a-4d76-894a-14fa9fb6381d)

### 3단계: 상세 설계

**안정성**

1. 데이터 손실 방지: 알림 로그 데이터베이스 유지하기
2. 알림 중복 전송 방지

**추가로 필요한 컴포넌트 및 고려사항**

1. 알림 템플릿
2. 알림 설정
3. 전송률 제한
4. 재시도 방법
5. 푸시 알림과 보안
6. 큐 모니터링
7. 이벤트 추적

**수정된 설게안**

![image](https://github.com/user-attachments/assets/81fb9551-4a98-4583-9f15-3544b39dde13)

### 4단계: 마무리

- Reliability
- Security
- 이벤트 추적 및 모니터링
- 사용자 설정
- 전송률 제한