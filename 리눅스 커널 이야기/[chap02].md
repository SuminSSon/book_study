# Chap 2. top을 통해 살펴보는 프로세스 정보들

### 2.1 시스템의 상태 살피기

```Shell
top -b -n 1
```

### 2.2 VIRT, RES, SHR

- VIRT: task가 사용하고 있는 virtual memory의 전체 용량
- RES: 현재 task가 사용하고 있는 물리 메모리의 양
- SHR: 다른 포르세스와 공유하고 있는 shared memory의 양

### 2.3 Memory Commit

- 프로세스는 자신만의 작업 공간이 필요하고 그 공간은 메모리에 존재한다.
- 프로세스가 커널에 필요한 만큼의 메모리를 요청하면 커널은 프로세스에 사용 가능한 메모리 영역을 주고 실제로 할당은 하지 않지만 해당 영역을 프로세스에 주었다는 것을 저장해 둔다.

overcommit이 필요한 이유
- 순간적으로 많은 양의 메모리를 필요로 하는 작업이 필요하기 때문
- fork() 와 같은 프로세스를 생성하는 시스템 콜을 처리할 수 있어야 하기 때문
- fork() -> exec() 콜을 통하여 새로운 작업으로 넘어가는 경우가 많기 때문에 복사해 온 공간을 그대로 사용하는 경우는 매우 드물게 일어남
- COW(Copy-On-Write) 기법을 통해 복사된 메모리 영역에 실제 쓰기 작업을 발생한 후에야 실질적인 메모리 할당 시작

![image](https://github.com/user-attachments/assets/83633c00-2ad3-4ea9-98b8-c8bf937b6e0f)

### 2.4 프로세스의 상태 보기

```Shell
S -- Process Status
    The status of the task which can be one of:
        'D' = uninterruptible sleep
        'R' = running
        'S' = sleeping
        'T' = traced or stopped
        'Z' = zombie
```

![zmNE7a6](https://github.com/user-attachments/assets/b113e10a-1632-49e6-b358-b208756aa606)

좀비 프로세스 자체는 이미 사용이 중지된 프로세스이기 때문에 메모리를 사용하지 않지만 좀비 프로세스가 점유하고 있는 PID 때문에 문제가 된다. 생성 가능한 최대 PID 개수가 있어서 ```sysctl -a | grep -i pid_max``` 명령어로 최대값 확인하기

### 프로세스의 우선순위

![bXW9o3Q](https://github.com/user-attachments/assets/e71e6733-f00b-4d7c-9b94-f771b1c13481)

- PR(Priority) : The priority of the task. 커널에서 인식하는 해당 프로세스의 실제 우선순위 값
- NI(Nice value): The nice value of the task. 명령어를 통해 우선순위를 낮출 때 사용된다.