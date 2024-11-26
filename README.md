# yskim-reframwork
reframework 만들어보기

## REFramework
- Robotic Enterprise Framework
- 엔터프라이즈 급
  - 확장성 (Scalability)
  - 안정성 (Reliability)
  - 보안 (Security)
  - 고가용성 (High Availability)
  - 유지보수성 (Maintainability)
  - 통합성 (Integratability)


## 내가 생각하는 구조

#### 1. Initialization
1. Kill Process
2. Check Robot Enviroment
   - 해상도
   - 필수 프로그램
3. Read Config `.xlsx`, `.json` (이왕이면 빠른 json)

#### 2. Make TransactionTable
