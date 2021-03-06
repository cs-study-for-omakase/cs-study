### 커널 오브젝트에 대한 이해

#### 커널

- 모든 운영체제에 존재하는 핵심 모듈
- 커널의 기능을 추가하고 보강한 운영체제

#### 커널 오브젝트

- 커널에 의해 관리되는 리소스(운영체제에 의해서 생성 및 소멸) 정보를 담고 있는 데이터 블록
- 프로세스의 상태 정보, 우선 순위 정보 데이터 저장
- 프로세스 생성 시 구조체의 형태로 함께 생성
- 리소스마다 관리되어야 하는 정보가 다르기 때문에 각각 다르게 디자인 됨
- 프로그래머가 직접적으로 접근 및 제어 불가
- 커널 오브젝트는 Windows 운영체제에 종속적

#### 운영체제

- 접근 정보로 접근을 허용 (ex. read)
- 조회 정보로 어디까지 읽었는지 전달

#### 핸들(Handle)

- 특정 프로세스의 우선순위를 높이기 위해서 프로세스 커널 오브젝트에 존재하는 우선순위 정보를 변경해 줘야함
- 프로세스의 커널 오브젝트에 부여된 고유한 정보
- 커널 오브젝트가 가진 정보를 수정하기 위한 간접적인 접근에 필요
- 핸들을 얻는 방법은 각 리소스마다 다름
- 핸들은 커널 오브젝트와 반대로 운영체제에 종속적이지 않고 프로세스에 종속적

우선순위 기반 커널 오브젝트의 프로세스 관리 = 고속의 CPU에서 출력 결과를 확인하기 위해 실행 속도를 늦추는 방법

 

### 커널 오브젝트와 핸들의 종속관계

#### 운영체제 - 커널 오브젝트 간 종속 관계

운영 체제에 종속된 커널 오브젝트는 여러 프로세스에 의해 간접적으로 접근 가능

- 커널 오브젝트가 가진 Usage Count 정보를 통해 해당 커널 오브젝트를 참조하고 있는 프로세스의 개수 확인
- 참조하는 프로세스가 소멸하면 Usage Count 값이 감소
- 커널 오브젝트를 직접 참조하는 자식 프로세스가 소멸하더라도 부모 프로세스는 여전히 해당 커널 오브젝트를 참조 가능 (Usage Count ≠ 0)

#### Usage Count(참조 횟수)의 사용

- Usage Count가 0이 되는 순간 해당 커널 오브젝트는 소멸
- 프로세스는 생성과 동시에 커널 오브젝트의 Usage Count가 1
- 생성과 동시에 Usage Count가 0으로 초기화된다면, 커널 오브젝트 소멸 원칙에 의해 생성과 동시에 소멸
- 초기화된 이후, 커널 오브젝트에 접근 가능한 대상이 늘어날 때마다 Usage Count가 하나씩 증가. 이는 커널 오브젝트에 접근 가능한 핸들 개수의 증가를 의미
- 부모 프로세스는 자식 프로세스를 생성하는 과정에서 자식 프로세스의 핸들을 얻기 때문에 자식 프로세스가 생성되면 Usage Count는 2

**Usage Count가 0으로 감소하여 커널 오브젝트가 완전히 소멸되는 시점**

- 자식 프로세스가 종료되었다는 의미는 자식 프로세스의 커널 오브젝트에 접근하는 대상이 하나 줄었다는 의미이고, 이는 Usage Count가 1
- 자식 프로세스가 종료되었더라도, 자식 프로세스가 종료되었다는 종료코드 정보를 담고있는 커널 오브젝트의 Usage Count가 1이기 때문에 소멸되지 않음
- CloseHandle 함수에 전달되는 인자는 자식 프로세스의 핸들이므로, 자식 프로세서의 커널 오브젝트에 더 이상 접근하지 않겠다는 의미를 지님. 따라서 이 때 Usage Count가 하나 더 감소
- 커널 오브젝트의 Usage Count는 0이되고, 커널 오브젝트 소멸 원칙에 의해서 소멸
