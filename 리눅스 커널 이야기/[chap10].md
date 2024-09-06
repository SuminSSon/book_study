# Chap 10. dirty page가 I/O에 끼치는 영향

Dirty page
- PageCache에 있는 페이지 중에서도 쓰기 작업이 이루어진 메모리
- dirty page로 표시된 메모리들을은 생성될 때마다 디스크에 쓰면 쓰개ㅣ I/O를 일으켜 성능 저하 발생

<img width="360" alt="파일 읽기 작업이 발생할 때의 과정" src="https://github.com/user-attachments/assets/7e93eeaa-f504-41e8-8cd6-1c2357ecc188">

Page writeback: 커널은 조건을 만족시키면 dirty page를 디스크로 동기화한다. (dirty page 동기화라고도 함)

### dirty page 관련 커널 파라미터

```Shell
sysctl -a | grep -i dirty
```
- vm.dirty_background_ratio
- vm.dirty_background_bytes
- vm.dirty_ratio
- vm.dirty_bytes
- vm.dirty_writeback_centisecs
- vm.dirty_expire_centisecs

### 백그라운드 동기화

1. 백그라운드 동기화
    - 관련 파라미터로는 vm.dirty_background_ratio와 vm.dirty_background_bytes가 있다. 
    - 더티 페이지를 생성할 때마다 현재까지 생성된 dirty page와 전체 메모리의 비율을 바탕으로 동기화를 한다. 
2. 주기적인 동기화
    - 관련 파라미터로는 vm.dirty_writeback_centisecs와 vm.dirty_expire_centisecs가 있다. 
    - 해당 값들을 조절하여 특정 주기로 flush 커널을 깨워 디스크 동기화를 수행한다. 
3. 명시적인 동기화
    - 명시적인 동기화는 명령어를 통해 명시적으로 동기화시키는 것을 의미한다. 
    - sync, fsync 명령을 이용해서, 현재 생성되어 있는 더티 페이지를 명시적으로 디스크에 쓰는 것이다. 

### dirty page 설정과 I/O 패턴

dirty page를 너무 빨리 동기화시키면 flush 커널 스레드가 너무 자주 깨어나게 되며, dirty page를 너무 늦게 동기화시키면 동기화해야 할 dirty page가 너무 많아서 vm.dirty_ratio에 도달할 가능성이 커지게 된다. 따라서 워크로드와 시스템 구성에 맞게 dirty page 동기화 수준을 설정해야 한다.