# Chap 3. Load Average와 시스템 부하

### 3.1 Load Average의 정의

- 얼마나 많은 프로세스가 실행 중 혹은 실행 대기 중이냐를 의미하는 수치
- Load Average가 높으면 많은 수의 프로세스가 실행 중이거나 I/O 등을 처리하기 위한 대기 상태에 있다는 것
- CPU Core 수에 따라 상대적인 값

### 3.2 Load Average 계산 과정

```Shell
uptime
```

![img](https://github.com/user-attachments/assets/942a6bd6-d192-457a-aaee-3da6f18d2d6a)

결국 프로세스의 개수를 세어 계산함

### 3.3 CPU Bound vs I/O Bound

Load Average가 높다는 것은 CPU 사용량이 높은지 I/O 처리량이 높은지 알 수 없음

### 3.4 vmstat으로 부하의 정체 확인하기

```Shell
vmstat 1
```

r: 실행되기를 기다리거나 현재 실행되고 있는 프로세스의 개수
b: I/O를 위해 대기열에 있는 프로세스의 개수

Load Average 중 CPU 사용량이 높을 때는 서버에게서 응답 시간을 받아올 때 I/O 사용량이 높을 때보다 느리다.

커널에도 버그가 있으며 Load Average 값을 절대적으로 신뢰해서는 안된다.