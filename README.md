# REFramework

## 1. REFramework 개요
- Robotic Enterprise Framework
- Enterprise?
    - 확장성 **Scalability**
    - 안정성 **Reliability**
    - 보안 **Security**
      - Group 권한 관리
        - Group 에 적절한 User를 할당하여 관리
      - Process 별 폴더 분리하여 Process 접근권한 관리
      - 
    - 유지보수성 **High Availability**
      - Orchestrator
      - dev, test, prod 를 위한 방안
        - DevOps
          - test 데이터 필요
          - test 성공 후, prod 배포
        - License
          - Non-Production => **test-enviroment**
          - Production => **prod-envirioment**

    - 통합성 **Interatability**

## 1.1 REFramework 요소
- Initialization
    - Read Config (**JSON** format)
    - PC 환경 확인
        - 해상도
        - 필수프로그램 실행어부 확인
    - Kill Process
    - 당일 영업일 여부 확인
      - 기타 영업일 확인 함수
      - Orchestrator 연동?
    - 폴더생성(삭제, 백업)
- Init_RecordTable
    - 작업수행 및 결과를 업데이트할 파일 테이블 양식
    - 작업대상을 만들고 해당 테이블 기준으로 Transaction 수행

### 1.1.1 REFramework 를 위한 Orchestrator API 연계
- User
- Machine
- Folder
  - Credential
  - Asset
- Group
 

<details>
  <summary>📝REFramework - Legacy</summary>

> ## 1.2 REFramwork 요소 - *Legacy*
>- Initialization
>    - InitAllSettings.xaml
>    - KillAllProcesses.xaml
>    - InitAllApplication.xaml
>- Get Transaction Data
>    - GetTransactionData.xaml
>- Process Transaction
>    - Process.xaml
>- SetTransactionStatus
>    - RetryCurrentTransaction.xaml
>    - TakeScreenshot.xaml
>    - KillAllProsses.xaml
>- EndProcess
>    - CloseAllApplication.xaml
>    - KillAllProcesses.xaml
</details>


## 2. TransactionTable(설계 필수)
>- Make Transaction Table 을 만드는데 여러 작업이 필요할 수 있음
>- 해당부분을 위한 재수행 혹은 재처리가 필요할 수 있음  

### 2.1 TransactionTable 개요

> | Sequence Name       | Description                                                    |
> |---------------------|----------------------------------------------------------------|
> | **Init_TransactionTable** | TransactionTable을 초기화 및 생성하는 작업                        |
> | **Get_Transaction**       | 다음 처리할 Transaction를 가져오는 작업                           |
> | **Process_Transaction**   | 하나의 Transaction에 대해 작업을 진행하는 메인 처리 Sequence      |
> | **Update_Transaction**    | Transaction의 처리 상태를 업데이트하는 작업                       |


### 2.2 Transaction Table **예시**
`Status` 는 옵션
>Key | User | ItemNo | SubItemNo | Step1 | Step2 | Step3 | Step1_R | Step2_R | Step3_R | Result | Status | Comment |
>|:---------:|:---------:|:----------:|:---:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------|:----------:|:----------:|:----------|
>| 01  | yskim   | A123 | 1 | N | N | N |   |   |   | N | Failed  | 데이터 부적합 
>| 02  | yskim   | A123| 2 | N | Y | Y |   |   |   | Y | Success |
>| 03  | thyoon  | B456| 1 | Y | Y | Y |   |   |   | Y | Success |
>| 04  | thyoon  | B456| 2 | N | N | N |   |   |   | N | Failed  | Step1 실패
>| 04  | thyoon  | B456 | 3 | Y | Y | Y |   |   |   | Y | Success |
>| 04  | thyoon  | B456 | 4 | Y | Y | Y |   |   |   | Y | Success |
>| 04  | cblim   | D234 | 1 | Y | Y | Y |   |   |   | Y | Success |
>| 04  | swjung  | D234 | 1 | Y | Y | Y |   |   |   | Y | Success |
>| 04  | fdx     | E567 | 1 | Y | Y | Y |   |   |   | Y | Success |
>| ... | ...     | ...| ...| ...| ...| ...|   |   |   | ...| ...| |
>| 99  | wdpark  | F890| 1  | Y | Y | N |   |   |   | N | Failed | Step3 승인작업실패, 승인자확인불가 

## 3. Process 트리거 방식
### 3.1 시간 기반 스케줄
### 3.2 Queue 기반 실행
- `Queue`의 `QueueItem`에 해당하는 일정 개수 이상이면 Process 시작
- `Queue` - 활용예시

### 3.3 사용자 기반 Process 실행 (Attended)
  
  
## 4. 소프트웨어 개발 제안
### 4.1 라이센스를 소모하기엔 기능이 단순한 부분들
- `Background Process` 로 대체 가능함
- 필요 기능 예시
    1. 메일
        - 메일을 계속 감지해서 메일이 새로 들어오면 `Queue` 에 추가
        - 메일을 계속 감지해서 메일이 새로 들어오면 `PID001-Mail` 수행하기
    2. 스토리지 버킷 업데이트(파일공유)
        - `Microsoft Teams`, `공유드라이브-경로`, 등에 현업담당자가 파일을 공유하면 소프트웨어가 감지하고 있다가 Orchestrator에 파일 업로드 해주기
    3. 현업 요청 시, `Credential`, `Asset` 업데이트 프로세스
    4. `Queue` 모니터링하여, `Attended Bot` 자동수행
    5. `소프트웨어` <> `Orchestrator`

## 4. Process 수행 방식
### 4.1 매번 새로운 데이터를 조회해서 데이터가 존재하면 수행 (ERP 조회, 메일 확인, Queue 확인)
- 데이터 존재시 수행
   - 데이터 수만큼 Transaction 수행
- 없으면 중단

## 5. Process 수행 결과
### 5.1 정상수행
#### 5.1.1 모두성공
- Transaction Table
    - `RPA수행결과` 항목
        - 모두 `Y`
#### 5.1.2 일부 BisunessException & SystemException
- Transaction Table
    - `RPA수행결과`, `예외구분`, `RPA비고` 항목
        - `Y`
        - `N`, `BizException`, `필수 조회조건 없음`
        - `N`, `SystemException`, `데이터 조회이후 ERP 업로드 중 오류`
#### 5.1.3 SystemException 으로 인한 수행 중단
- 연속적인 `SystemException` 발생
- `Process.xaml` 이외의 부분에서의 `Exception` 발생 시 
    - 업무 시작조차 제대로 못하고 중단 
    - 업무는 정상적으로 `EndProcess`에 도달 하였는데, 마지막 데이터 전달(메일)을 수행하지 못하고 중단

## 6. REFramework 상세제안
- TransactionTable
    - MakeTransactionTable
        - Transaction을 위한 Table 생성
        - Transaction 가져오기
        - Transaction 결과업데이트
- OpenApplicationForProcess




## 6. Process 재수행 방식

### 6.1 Process-Multi 재수행

### 6.2 Transaction 단위 재수행
1. 기본적으로 Transaction 단위 재수행을 (Default:3 번 수행하게 되어있음)
2. Transaction 단위 재수행
    - 여러 Step 이라면 기존에 수행한 Step은 수행하지 않아야 함
    - ex. 다운로드파일 - 데이터 가공 - 업로드 - 승인요청
        - Transaction 재시도 시, 데이터 가공까지 성공하고, 업로드에서 실패했다면
            - 다운로드파일, 데이터 가공 부분은 기존 작업그대로 진행하고
            - 업로드, 승인요청 단계를 진행 한다. 

### 6.3 Process-Single 재수행


### 오케스트레이터 폴더구조
- 적합한 권한을 위해 폴더별 Process 구분
- Process 일괄 수행은 DashBoard + @ 에서 확인

### Shared
- Credential 은 Shared에서 관리하고 로봇 Account Mapping 하여 사용
