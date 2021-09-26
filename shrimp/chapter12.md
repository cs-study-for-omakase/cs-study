### 쓰레드의 생성

```
HANDLE CreateThread(
	LPSECURITY_ATTRIBUTES // 핸들 상속 여부 결정
	SIZE_T // 독립 stack의 크기
	LPTHREAD_START_ROUTINE // 쓰레드로 동작하기 위한 함수
	LPVOID // 쓰레드 함수의 전달인자
	DWORD // 생성 옵션
	LPDWORD // 쓰레드 ID
)
```



### 쓰레드의 소멸

- return을 사용하는 경우
  - 모든 함수가 return을 마치고 나서 메인 함수가 return하면 종료 됨
- ExitThread 함수 호출을 사용하는 경우
  - 쓰레드 내의 어떤 함수에서든 사용하면 쓰레드가 종료
- TerminateThread 함수 호출을 사용하는 경우
  - 외부에서 쓰레드를 강제 종료



### 쓰레드의 성격과 특성

#### **쓰레드에서는 힙, 데이터, 코드 영역 공유**

- 전역변수를 쓰레드가 접근할 수 있음

#### **동시 접근의 문제점**

- 임계영역 접근
  - 둘 이상의 쓰레드가 하나의 전역 변수에 접근을 하면 문제 발생가능
  - 쓰레드가 전역변수에 작업을 하고 있는 동안 다른 쓰레드 접근 불가

#### **프로세스로부터 쓰레드 분리**

- 쓰레드 종료 시 Usage Count = 1
- 커널 오브젝트의 메모리에 여전히 잔재
- 쓰레드를 생성과 동시에 CloseHandle()를 호출하면 쓰레드 종료 시 Usage Count = 0 (쓰레드 분리)

#### ANSI 표준 C라이브러리와 쓰레드

**_beginthreadex 함수**

- 내부적으로 CreateThread 함수를 호출
- 둘 이상의 쓰레드가 1개의 메모리 공간에 동시 접근하지 않도록 각 쓰레드를 위한 별도의 메모리 공간 할당 (임계영역 문제 해소)

**_endthreadex 함수**

- 할당된 메모리 공간 해제(반환)



### 쓰레드의 상태 컨트롤

- 프로세스 상태 컨트롤 내용이 동일
- 스케줄러는 프로세스가 아닌 쓰레드를 컨트롤 함

- **SuspendThread 함수** : Running→ blocked

- **ResumeThread 함수** : Blocked → Ready
