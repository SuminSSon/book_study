# Chap 7. TIME_WAIT 소켓이 서비스에 미치는 영향

소켓: 표준 유닉스 파일 디스크럽터(File Descriptors)를 통해서 다른 프로그램과 정보를 교환하는 방법

### 7.1 TCP 통신 과정

![1_IE_SkdCYUrY3HyQ5U59WZg](https://github.com/user-attachments/assets/7739811c-d086-4597-bd8f-d23ae878c50c)

3-way handshake 방식 : 최초의 연결을 맺게 되는 과정
4-way handshake 방식 : HTTP 통신일 경우 요청하고, 통신을 모두 마친 후에는 연결 종료

### 7.2 TIME_WAIT 소켓의 문제점

TIME_WAIT: 네트워크상에서 지연이 되거나 상실되어 도착하지 못한 패킷을 처리하기 위해 존재

연결을 먼저 끊는 쪽을 active closer라고 하고 그 반대를 passive closer라고 한다. active closer 쪽에서 TIME_WAIT 소켓이 생성된다.

```Shell
netstat -napo | grep -i time_wait
```

TIME_WAIT 소켓의 개수 확인 명령어

TIME_WAIT 상태에 타이머가 종료되어 커널로 다시 돌아갈 때까지 사용할 수 없다. 로컬 포트 고갈에 따른 애플리케이션 타임아웃이 발생할 수 있다. ```net.ipv4.ip_local_prort_range``` 커널 파라미터를 이용하여 포드 범위 지정한다.

방안. Connection Pool과 같은 방식을 사용해서 TCP 연결 재사용

### 7.3 클라이언트에서의 TIME_WAIT

일반적인 2 tier 구조의 시스템 구성일 경우 Web Server가 DB Server의 클라이언트가 됨.

### 7.4 net.ipv4.tcp_tw_reuse

TIME_WAIT 소켓을 처리하는 커널 파라미터 중 net.ipv4.tcp_tw_reuse는 외부로 요청할 때 소켓을 재사용할 수 있게 해준다.

![90rafkG](https://github.com/user-attachments/assets/73317b50-a37d-491e-8368-f2cd270314ea)

- timestamp 기능을 함께 사용하여 패킷 구분
- net.ipv4.tcp_timestamps값이 반드시 1이어야 한다.
- 클라이언트의 요청을 직접 받는 웹 서버에서 주로 발생할 수 있기 때문에 웹 서버에서는 tw_recycle 을 절대로 활성화 해서는 안된다.
- keepalive를 이용하여 TIME_WAIT 소켓을 줄일 수 있음
- 세션을 유지하기 때문에 handshake 과정이 최소화되고 서비스 응답 속도가 향상 가능

### 7.5 ConnectionPool 방식 사용하기

Connection Pool: 미리 소켓을 열어놓고 요청을 처리하는 방식

불필요한 TCP 3 way handshake 방지

### 7.6 서버 입장에서의 TIME_WAIT 소켓

keepalive_timeout을 0으로 하여 웹 서버가 먼저 연결을 끊는다.

### 7.7 net.ipv4.tcp_tw_recycle

net.ipv4.tcp_tw_reuse: 클라이언트 입장에서나갈 때 사용하는 로컬 포트에서 TIME_WAIT 상태의 소켓을 재사용할 수 있게 해주는 파라미터

net.ipv4.tcp_tw_recycle: 서버 입장에서 TIME_WAIT 상태의 소켓을 빠르게 회수하고 재활용할 수 있게 해주는 파라미터

tw_recycle이 켜지면 커널은 두 가지 작업을 수행한다.
1. 가장 마지막에 해당 소켓으로부터 들어온 timestamp 저장
2. TIME_WAIT 소켓의 타이머를 RTO(Retransmission Timeout) 기반의 값으로 변경

![IbwwDRm](https://github.com/user-attachments/assets/c3b827b4-1fb8-480e-9233-d9016d62324e)

NAT 환경에서의 다양한 클라이언트가 있을 때 항상 Timestamp가 단조증가한다는 보장이 없으므로 이는 치명적인 문제를 야기한다. (커널 4.12버전에서 해당 파라미터가 제거된 원인)

### 7.8 keepalive 사용하기

keepalive
- 한번 맺은 세션을 요청이 끝나더라도 유지해주는 기능
- keepalive의 타임아웃을 10초로 설정하면 10초 뒤에 서버에서 먼저 연결을 끊게 된다.

### 7.9 TIME_WAIT 상태의 존재 이유

TIME_WAIT 소켓의 목적은 연결이 종료된 후에도 소켓을 바로 정리하지 않고 일종의 연결 종료에 대한 흔적을 남겨 놓는 것에 있다. 일정 시간 동안 연결 종료에 대한 문제점을 방지하는 것이 TIME_WAIT 소켓의 목적이다. 다시 말해서 패킷 유실에 따른 비정상적인 통신 흐름의 발생을 막는 것이다.

안정적인 TCP통신을 위해 TIME_WAIT는 반드시 필요하다.