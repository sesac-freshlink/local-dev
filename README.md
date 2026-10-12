# local-dev

> **AWS 없이 내 PC에서 FreshLink를 개발하는 환경.** 이 레포 하나만 받아 `docker compose up` 하면 MySQL과 가짜 SQS(LocalStack)가 뜨고, 더미 데이터가 들어간다.

| 항목 | 내용 |
| --- | --- |
| 담당 | 김진아 |
| 관련 기능 ID | E-12 더미 데이터 |
| 기술 | Docker Compose, MySQL 8.0, LocalStack (SQS) |
| 브랜치 | `main` ← 작업 브랜치 (배포 없음) |

---

## 1. 왜 따로 두나

각 서비스 레포에 Compose 파일이 흩어지면 MySQL 버전, 큐 이름, 비밀번호가 레포마다 어긋난다. 한곳에 두면 모두 같은 환경에서 개발한다.

## 2. 무엇이 뜨나

| 컨테이너 | 포트 | 내용 |
| --- | --- | --- |
| MySQL 8.0 | 3306 | 스키마 `member`, `inventory`, `orders`와 서비스별 사용자 `member_app`, `inventory_app`, `order_app` |
| LocalStack | 4566 | SQS `inventory-commands.fifo`, `inventory-results`, DLQ 2개 |

운영(RDS 1대 + 스키마 3개, SQS 4개)과 같은 모양이다.

### 더미 데이터 (E-12)

점포 3개, 상품 30개, 점주 계정 3개가 기본으로 들어간다.

## 3. 폴더 구조

```
local-dev/
├── docker-compose.yml      # MySQL + LocalStack
├── .env.example            # 비밀번호 등 (복사해서 .env로 사용)
├── mysql/
│   └── init/               # 스키마·사용자 생성, 더미 데이터 SQL (번호순 실행)
└── localstack/
    └── init/               # SQS 큐 생성 스크립트
```

## 4. 사용법

> 아직 뼈대만 있는 상태다. `docker-compose.yml`과 초기화 스크립트는 1~2주 차에 채운다.

준비물: Docker Desktop

```bash
cp .env.example .env
docker compose up -d
docker compose ps
```

큐가 만들어졌는지 확인:

```bash
docker compose exec localstack awslocal sqs list-queues
```

전부 지우고 처음부터:

```bash
docker compose down -v
```

서비스에서는 프로필 `local`로 실행하면 이 환경에 붙는다. 각 서비스 README의 '로컬에서 실행하기'를 참고한다.

## 5. 기여

- `main` ← `chore/<설명>` 또는 `feat/E-12-<설명>`
- 스키마나 큐 이름을 바꾸면 각 서비스 담당자에게 알린다.
