### 멀티프로세스와 쓰레드

멀티프로세스가 필요한 경우 : 운영체제 관점에서 둘 이상의 프로그램을 실행하기 위해서 필요

하나의 프로그램 안에서 둘 이상의 실행 흐름을 가져야 하는 필요가 있음

#### 쓰레드의 등장

부모 프로세스의 구성

- Code 영역: 프로그램 코드
- Data 영역: 전역 변수
- Heap 영역: 동적 할당 메모리 공간
- Stack 영역: 지역 변수, 매개 변수

**부모 프로세스에서 여러 자식 프로세스가 생성될 경우**

- 컨텍스트 스위칭이 빈번하게 이러나서 성능을 저하시킴
- 프로세스의 Code, Data, Heap 영역을 계속 생성하는 것도 비효율적임
- 쓰레드는 프로세스의 Code, Data, Heap 영역을 같이 공유해서 사용하고 별도의 Stack 영역을 사용함



#### 프로세스와 쓰레드의 차이점

- 공유 = 쓰레드(Code, Data, Heap 영역이 없다)
- Code 영역 = 함수(전역 함수, main 함수)가 올라감
  - main : 하나의 흐름 / 흐름이 2개 이상 -> main 함수 역할을 하는 함수 2 이상
  - main이 실행되는 동안, main 함수 역할을 함수가 실행이 되는 구조로 코드 작성
  - 그런 함수를 쓰레드가 실행
- 프로세스는 자식 프로세스를 생성하면 공유가 불가능 -> IPC를 통해 데이터를 주고받음
- 쓰레드는 메모리 공간 자체를 공유하기 때문에 별도의 통신 기법 필요 X



#### Windows에서의 프로세스와 쓰레드

- 스케줄러는 각 프로세스의 쓰레드를 통합해 관리
- 상태 정보는 쓰레드가 가지고 있고 스케줄러가 실행의 단위로 쓰레드를 선택을 함
- 프로세스에서 적용되는 것 = 쓰레드의 상태와 우선순위, 스케줄링 알고리즘
- 실행의 주체는 쓰레드
  - 프로그램 처음 실행시킬 때의 main 함수도 쓰레드에 의해 실행 (=main 쓰레드)
- 쓰레드가 실행될 수 있는 환경을 제공 = 프로세스



### 쓰레드 구현 모델에 따른 구분

#### 커널 레벨 쓰레드

커널이 API 형태로 쓰레드 기능을 지원

- 쓰레드를 지원하는 커널 레벨에서 제공해주는 쓰레드
- 정보를 커널이 알고 있고 스케줄러가 직접 쓰레드를 관리
- 쓰레드가 I/O 상태가 되면 스케줄러가 다른 쓰레드에게 실행 우선순위를 넘김
- 유저 레벨 쓰레드에 비해 상대적으로 느림

#### 유저 레벨 쓰레드

유저가 만든 라이브러리 형태로 쓰레드 기능을 지원

- 쓰레드를 지원하지 않는 커널에서 유저가 라이브러리 형태로 만들어 사용하는 쓰레드
- 프로세스의 정보를 커널이 알고 있고, 스케줄러가 프로세스를 관리하며 그 안에서 쓰레드가 동작
- 프로세스 단위로 실행되기 때문에 쓰레드가 I/O 상태(Blocked)가 되면 해당 프로세스 안에 다른 쓰레드가 대기하고 있더라도 순서가 통째로 다른 프로세스로 전환



### 커널 모드와 유저 모드

Windows는 동작할 때 커널 모드와 유저 모드 중 한가지 모드로 동작

유저 영역에서 커널 영역의 메모리 공간에 접근하여 바꾸는 오류 가능성 존재

- 커널 영역이 실행될 때는 커널 모드가 실행되어 메모리 공간 어디에도 접근 가능
- 유저 영역이 실행될 때는 커널 영역으로는 접근 불가하도록 제한

#### 유저 영역에서 커널 영역으로 전환하는 경우

- 커널이 동작해야 할 경우
  - 스케줄러가 동작할 때
  - (자식) 프로세스 생성할 때

#### 커널 레벨 쓰레드의 장점 및 단점

- 장점 : 커널에서 직접 제공해주기 때문에 안전성과 다양한 기능성 제공
- 단점 : 유저 모드에서 커널 모드로의 전환이 빈번 -> 속도 저하

#### 유저 레벨 쓰레드의 장점 및 단점

- 장점 : 유저 모드에서 커널 모드로의 전환이 불필요 -> 속도 향상
- 단점 : 쓰레드 단위 컨텍스트 스위칭이 불가 -> 프로세스 단위의 블로킹 발생
