# Chap 1. 시스템 구성 정보 확인하기

### 1.1 커널 정보 확인하기
```Shell
uname -a
```

커널 디버그 메시지로, 커널이 부팅할 때 나오는 메시지와 운영 중에 발생하는 메시지를 볼 수 있게 해주는 명령어
```Shell
dmesg
```

### 1.2 CPU 정보 확인하기

```Shell
dmidecode -t bios
dmidecode -t system
dmidecode -t processor
lscpu
```

### 1.3 메모리 정보 확인하기

```Shell
dmidecode -t memory | grep -i size
free -m
```

### 1.4 디스크 정보 확인하기

```Shell
df -h
smartctl -a /dev/sda #디스크의 물리적 정보 확인
```

### 1.5 네트워크 정보 확인하기

```Shell
lspci | grep -i ether
ethtool eth0 #연결 상태 확인
ethtool -k eth0 #성능 최적화 옵션 확인
```

tcp-offload: MTU 이상의 크기를 가지는 패킷의 분할 작업을 CPU가 아닌 네트워크 카드가 직접 함으로써 CPU의 부담을 줄이고 패킷 처리 성능을 높이는 기능