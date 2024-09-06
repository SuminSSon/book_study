# Chap 9. TCP 재전송과 타임아웃

RTO(Retransmission Timeout): ACK를 기다리는 시간에 대한 값
- RTO 안에 ACK를 받지 못하면 보내는 쪽에서 재전송 진행
- InitRTO는 두 종단 간 최초 연결을 시작할 때 첫 번째 SYN 패킷에 대한 RTO를 의미
- ```ss -i``` 명령어로 리눅스 에서 설정한 세션의 RTO 값 확인 가능

TCP 재전송과 관련된 커널 파라미터 확인하기
```Shell
sysctl -a | grep -i retries
```

- net.ipv4.tcp_syn_retries: SYN에 대한 재시도 횟수
- 2의 배수로 증가하면서 최대 5개의 패킷을 보낸 후 연경르 종류한다.
- net.ipv4.tcp_synack_retries: 상대편이 보낸 SYN에 대한 SYN+ACK의 재전송 횟수 정의
- 특정 프로세스에 할당되지 않고 커널에 귀속되어 정리되기를 기다리는 소켓 주엥서도 FIN_WAIT1 상태의 소켓: orphan socket
- net.ipv4.tcp_orphan_retries: orphan 소켓의 재전송 횟수

RTO 최소값 설정하기
```Shell
ip route change default via <GW> dev <DEVICE> rto_min 100ms
```

### 애플리케이션 타임아웃
| 종류 | 발생 경로 | 최소 권장 설정 값 |
|--|--|--|
|Connection Timeout|TCP Handshake 과정에서 재전송이 일어날 경우 발생|3s 이상|
|Read Timeout|맺어져 있는 세션을 통해서 데이터를 요청하는 과정에서 발생|300ms 이상|

