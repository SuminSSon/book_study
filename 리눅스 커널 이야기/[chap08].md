# Chap 8. TCP Keepalive를 이용한 세션 유지

TCP Keepalive는 두 종단 간 맺은 세션을 유지해서 통신이 일어날 때마다 유지 중인 세션을 이용하게 한다.

```Shell
netstat -napo
```

netstat을 이용해서 TCP Keepalive 타이머 확인하기

소켓을 생성할 때 setsocketopt()을 이용하여 소켓 옵션을 설정하는데 이 중 SO_KEEPALIVE를 선택하면 TCP Keepalive를 사용할 수 있다.

TCP Keepalive는 일정 시간이 지나면 연결된 세션의 두 종단이 서로 살아 있는지를 확인하는 아주 작은 양의 패킷(68byte)을 하나 보낸다. 한 쪽에서만 보내도 세션이 유지된다.

### TCP Keepalive 파라미터
1. net.ipv4.tcp_keepalive_time: keepalive 소켓의 유지 시간 (최소 240초)
2. net.ipv4.tcp_keepalive_probes: keepalive 패킷을 보낼 최대 전송 횟수(재전송 횟수)
3. net.ipv4.tcp_keepalive_intvl: keepalive 재전송 패킷을 보내는 주기

-> 좀비 커넥션 문제 방지

- TCP Keepalive: connection을 지속적으로 얼마나 유지하는가
- HTTP Keepalive: 최대 얼마동안 connection을 유지하는가

HTTP Keepalive가 설정됭 ㅓ있다면 TCP Keepavlie 설정 값과 다르다고 하더라도 의도한 대로 정상적으로 동작한다.

TCP Keepalive 설정을 이용하면 연결이 끊어졌는데도 FIN 패킷을 받지 못해 정리되지 않고 남아있는 좀비 커넥션을 없앨 수 있다.

로드밸런서를 사용하는 환경에서 TCP 기반의 서비스를 하는 경우에는 반드시 TCP Keepalive를 설정해야 한다.