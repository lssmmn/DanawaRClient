# 원격 장치 실시간 감시 및 제어 통합 관제 시스템

## 프로젝트 개요
원격 장치들의 실시간 성능 정보(CPU, RAM, 디스크, 네트워크)를 통합하여 수집하고, 중앙 관제 시스템에서 실시간으로 표시 및 데이터베이스에 기록하는 최소 기능 제공을 구현하는 것입니다. 또한, 중앙 시스템에서 해당 장치에 원격으로 제어 명령을 내릴 수 있는 기능을 포함합니다.

## 프로젝트 정보
- **개발 기간**: 2025.11.14(금) ~ 2025.11.18(화) (05일)
- **개발 장소**: 드론융합실
- **주요 주제**: 원격 장치 실시간 감시 및 제어를 위한 통합 관제 하는 원격 모니터링 시스템 구축

## 개발 환경
- **프레임워크**: WPF
- **언어**: C#
- **네트워크**: TCP/IP Sockets
- **데이터베이스**: SQL Server Express, Dapper, MySQL
- **IDE**: Visual Studio 2022
- **OS**: Windows 10

## 팀원 및 담당 업무
| 이름 | 담당 파트 |
|------|----------|
| 팀원1 | 대시보드 (UI/UX) |
| 팀원2 | Agent (UI/서블) |
| 팀원3 | 대시보드 (Logic) |
| 팀원4 | 데이터베이스 (DB) |
| 팀원5 | 서버 (네트워킹) |
| **이수민** | **Agent (Logic/통신)** |

## 개발 목적
원격 장치들의 실시간 성능 정보(CPU, RAM 등)를 통합하여 수집하고, 중앙 관제 시스템에 실시간으로 표시 및 데이터베이스에 기록하는 최소 기능 제공을 구현하는 것입니다. 또한, 중앙 시스템에서 해당 장치에 원격으로 제어 명령을 내릴 수 있는 기능을 포함합니다.

## 시스템 아키텍처
```
[Agent (원격 장치)]  ← 이수민 담당 Logic/통신
    ↓ TCP/IP
[Server (네트워킹)]
    ↓
[Dashboard (UI/UX)]
    ↓
[Database (SQL Server)]
```

## 주요 구현 기능

### 1. 핵심 기능 구현 목표

#### 실시간 성능 지표 시각화
원격 장치들의 CPU, RAM, Disk, Network 등 주요 성능 데이터와 가장 오도 지표를 중앙 관제 시스템에서 LiveCharts를 활용하여 실시간으로 표시합니다.

#### 다중 장치 관리
중앙 관제 시스템은 여러 원격 장치에 동시에 접속하여 상태(접속/정지/작동)를 요약 뷰와 상세 부문 구분하여 표시하고 관리합니다.

#### 원격 제어
중앙 시스템의 UI를 통해 특정 원격 장치로 '정지/시작' 등의 제어 명령을 전송하는 기능을 구현합니다.

### 2. 데이터 기록 및 관리 기능

#### 성능 기록
원격 장치로부터 수신한 모든 실시간 성능 데이터를 SensorDataLog 테이블에 1초 단위로 기록합니다.

#### 제어 이력 관리
중앙 시스템에서 발생한 모든 원격 제어 명령(STOP, START)을 AuditLog 테이블에 기록하여 이력을 관리합니다.

### 3. 추가 선택 기능

#### 7일 게시에서 제외되는 기능
- OPC-UA 통신 통합
- 이력 데이터 조회 UI
- 완성된 역할 기반 접근 제어(RBAC) 로그인 기능
- MVP 기간 이후로 제외됩니다

#### 포함된 선택 기능
원격 장치의 데이터 전송을 제어하는 '정지/시작' 명령 전송 기능을 MVP에 포함하여 구현합니다.

## 이수민 담당 Agent (Logic/통신) 상세

### 🎯 핵심 역할
원격 장치에서 실행되는 Agent 프로그램의 핵심 로직과 통신 기능을 담당하여, 시스템 성능 데이터를 수집하고 서버와 통신하며 제어 명령을 처리

### 📊 데이터 수집 로직
- **PerformanceCounter 활용**: CPU, RAM, Disk, Network 사용률 실시간 수집
- **LibreHardwareMonitorLib/WMI 통합**: 다양한 하드웨어 정보 수집
- **데이터 전송 최적화**: 수집 주기와 전송 주기 분리로 네트워크 부하 최소화
- **Batch Insert 구현**: 일정 시간 단위로 데이터를 모아 DB 효율적 저장

### 🔗 네트워킹 (TCP/IP 통신)
- **Socket 기반 통신**: 서버와의 안정적인 TCP 연결 유지
- **자동 재연결**: 연결 끊김 시 자동 재시도 메커니즘
- **JSON 직렬화**: 성능 데이터를 JSON 형식으로 직렬화하여 전송
- **명령 수신 처리**: 서버로부터 STOP/START 명령 수신 및 즉시 처리

### ⚙️ 제어 명령 처리
- **STATUS_UPDATE 메시지**: Agent 상태 변경 시 실시간 전송
- **명령 큐 시스템**: 여러 명령을 순차적으로 안전하게 처리
- **상태 동기화**: Host UI와 Agent 실제 상태 일치 보장

### 🗄️ 로컬 데이터 관리
- **비동기 DB 연결**: MySqlConnector를 통한 효율적 데이터베이스 접근
- **Connection Pooling**: 연결 재사용으로 성능 최적화
- **트랜잭션 처리**: 데이터 일관성 보장을 위한 트랜잭션 관리

## 예상 문제점 및 해결 방안

### 데이터 전송 안정성 및 파싱 문제
**문제**: Agent가 PC의 성능 데이터를 수집할 때, 특히 Network나 Disk 같은 지표의 PerformanceCounter 인스턴스명이 개발 환경과 실제 운영 환경(다른 PC)에서 달라져 데이터 수집이 실패할 수 있습니다.

**해결방안**:
- Host 실행 시 Agent 연결 시 인스턴스명을 수신, 파싱, 그리고 DB 로컬(IF)과 UI 시각화(E1) 로직으로 중계하는 과정에서의 복잡 및 안정성 문제입니다.
- 원격 제어 동기화: Host 서버에서 Agent로 보낸 제어 명령(STOP/START)이 Agent에 도착했을 때, Agent의 데이터 전송 타이밍과 정확히 반주가나 시작되었는지 확인하고, 동시에 Host UI에도 Agent의 상태 변화(예: "정지됨")가 실시간으로 반영되도록 동기화하는 로직이 구현 난이도.

### 데이터 전송 안정성 및 파싱 문제
**문제**: Agent는 데이터 전송 주기를 다양한 PerformanceCounter 대신 LibreHardwareMonitorLib나 WMI를 사용해 Network나 Disk 지표를 동적으로 검색하고 안정적으로 수집합니다.

**해결방안**:
- 수집 실패 시 오류 코드를 전송하여 프로그램 중단을 방지합니다.
- Host 서버의 부하를 최소화하기 위해, 실시간으로 수신한 JSON을 파싱해 DB에 Concurrence\<T>에 버퍼링한 뒤, 그리웬은 작업이 UI 큐에서 데이터를 주기적으로 꺼내 Batch Insert(일괄 삽입)를 수행하여 DB 접근 부하를 분산하고 서버 안정성을 확보합니다.

### 원격 제어 동기화
**문제**: 완전한 상태 피드백 루프를 구축하여 실시간 동기화를 구현합니다.

**해결방안**:
- Agent는 Host로부터 STOP/START 명령을 받아 타이머를 제어한 직후에, 자신의 새로운 상태(예: {"type": "STATUS_UPDATE", "status": "STOPPED"})를 다시 Host에 전송합니다.
- Host 서버는 이 STATUS_UPDATE 메시지를 수신하여 파싱하고, Application.Current.Dispatcher.Invoke를 사용해 Host UI 스레드에서 안전하게 ViewModel에 반영해 각 Agent의 실제 상태를 표시합니다.

### DB 동시 접근 문제
**문제**: 상단은 다수의 로직의 동시 접근 문제를 해결하기 위해, DB 접근 시 MySqlConnector와 비동기 프로그래밍(async/await)을 활용합니다.

**해결방안**:
- DB 연결 풀(Connection Pooling)을 사용하되도 연결 문자열을 설정하여, 다수 요청이 독립적인 비동기 커넥션에서 지리되도록 보장합니다.
- 이 방식은 트랜잭션 레벨을 위험을 최소화하며, 읽기와 갈이 대부분 읽기작업인 INSERT 작업으로 구성된 로깅에 높은 정확성과 성능을 제공합니다.

## 데이터베이스 스키마

### SensorDataLog 테이블
```sql
CREATE TABLE SensorDataLog (
    LogID INT AUTO_INCREMENT PRIMARY KEY,
    AgentID VARCHAR(50) NOT NULL,
    Timestamp DATETIME NOT NULL,
    CPU_Usage DECIMAL(5,2),
    RAM_Usage DECIMAL(5,2),
    Disk_Usage DECIMAL(5,2),
    Network_Usage DECIMAL(10,2),
    INDEX idx_agent_time (AgentID, Timestamp)
);
```

### AuditLog 테이블
```sql
CREATE TABLE AuditLog (
    AuditID INT AUTO_INCREMENT PRIMARY KEY,
    AgentID VARCHAR(50) NOT NULL,
    Command VARCHAR(20) NOT NULL,
    IssuedBy VARCHAR(50),
    IssuedAt DATETIME DEFAULT CURRENT_TIMESTAMP,
    Status VARCHAR(20)
);
```

## 통신 프로토콜

### Agent → Server (성능 데이터)
```json
{
  "type": "PERFORMANCE_DATA",
  "agentId": "AGENT_001",
  "timestamp": "2025-11-18T10:30:00",
  "cpu": 45.2,
  "ram": 62.8,
  "disk": 73.5,
  "network": 1024.5
}
```

### Server → Agent (제어 명령)
```json
{
  "type": "CONTROL_COMMAND",
  "command": "STOP",
  "agentId": "AGENT_001"
}
```

### Agent → Server (상태 업데이트)
```json
{
  "type": "STATUS_UPDATE",
  "agentId": "AGENT_001",
  "status": "STOPPED",
  "timestamp": "2025-11-18T10:30:05"
}
```

## 설치 및 실행

### 사전 요구사항
- Visual Studio 2022
- .NET 6.0 이상
- SQL Server Express 또는 MySQL 8.0
- Windows 10 이상

### 실행 순서
1. **데이터베이스 설정**
   ```sql
   CREATE DATABASE RemoteMonitoring;
   -- 테이블 생성 (위 스키마 참조)
   ```

2. **서버 실행**
   ```bash
   cd Server
   dotnet run
   ```

3. **Dashboard 실행**
   ```bash
   cd Dashboard
   dotnet run
   ```

4. **Agent 실행** (모니터링할 각 PC에서)
   ```bash
   cd Agent
   dotnet run
   ```

## 프로젝트 구조
```
Remote-Monitoring-System/
├── Agent/                # Agent 프로그램 (이수민 담당)
│   ├── Logic/
│   │   ├── DataCollector.cs
│   │   └── PerformanceMonitor.cs
│   ├── Network/
│   │   ├── TcpClient.cs
│   │   └── MessageHandler.cs
│   └── Program.cs
├── Server/               # 네트워킹 서버
│   ├── TcpServer.cs
│   └── ClientManager.cs
├── Dashboard/            # WPF 대시보드
│   ├── ViewModels/
│   ├── Views/
│   └── App.xaml
└── Database/
    └── schema.sql
```

## 개발 성과
- **WPF 프레임워크 활용**: MVVM 패턴 기반 UI 개발
- **실시간 데이터 처리**: PerformanceCounter 및 하드웨어 모니터링
- **네트워크 프로그래밍**: TCP/IP 소켓 기반 양방향 통신
- **비동기 프로그래밍**: async/await 패턴을 통한 효율적 데이터 처리
- **데이터베이스 최적화**: Connection Pooling 및 Batch Insert
- **분산 시스템 설계**: Agent-Server-Dashboard 아키텍처 구현

## 라이센스
이 프로젝트는 교육 목적으로 제작되었습니다.
