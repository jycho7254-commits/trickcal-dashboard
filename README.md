# 🎲 Trickcal Revive 대시보드

> 트릭컬 글로벌/일본 실매출·지표 통합 대시보드

🔗 **한국어**: https://jycho7254-commits.github.io/trickcal-dashboard/
🔗 **중국어**: https://jycho7254-commits.github.io/trickcal-dashboard-zh/

---

## 📊 주요 기능

### 핵심 지표
| 항목 | 내용 |
|------|------|
| 글로벌 매출 | 일별 USD 매출, ARPU, ARPPU, 결제율 |
| 일본 매출 | iOS/Android 플랫폼별 일매출 |
| 활성 유저 | DAU, 신규 가입자, 결제 계정 수 |
| 이벤트 마킹 | 업데이트/콜라보 기간 마킹 |
| 지역 전환 | 글로벌↔일본 매출 비교 |

### 차트 (Chart.js 4.4)
- **일별 매출 추이** — 글로벌/일본 라인차트
- **ARPU/ARPPU 추이** — 결제 단가 분석
- **결제율 추이** — 페이레이션 전환율
- **이벤트 마킹** — 업데이트/콜라보 기간 배경 하이라이트

---

## 🏗 아키텍처

### 구조
```
trickcal-dashboard/
├── index.html          # 한국어 대시보드 (166KB, 인라인)
├── index_zh.html       # 중국어 대시보드
├── index_ko.html       # 한국어 백업
├── data/
│   ├── global_revenue.csv   # 글로벌 일별 매출 (date, revenue_usd, arpu, arppu, pay_rate)
│   ├── jp_revenue.csv       # 일본 일별 매출 (iOS/GP별)
│   ├── inspection_metrics.json  # 검수 데이터
│   └── region_transition.json   # 지역 전환 데이터
├── scripts/
│   └── auto_update.py   # 매출 데이터 자동 수집
└── README.md
```

### 기술 스택
| 영역 | 기술 |
|------|------|
| 프론트엔드 | 순수 HTML/CSS/JS (프레임워크 없음) |
| 차트 | Chart.js 4.4.7 (CDN) |
| 데이터 | CSV 인라인 (fetch 불필요) |
| 배포 | GitHub Pages (KR/ZH 2개 repo) |
| 자동화 | Python 스크립트 (매출 CSV 갱신) |

### 디자인
- **다크 테마** (#0a0a1a 배경, #00d4ff 시안 액센트)
- KRW 바 (원화 환산 매출 상단 표시)
- 카드 레이아웃 (background: #141432)
- 모바일 반응형 (flex-wrap)

---

## 📈 데이터 소스

| 항목 | 소스 |
|------|------|
| 글로벌 매출 | 에피드 내부 데이터 (daily revenue) |
| 일본 매출 | 에피드 내부 데이터 (iOS/GP별) |
| 환율 | 1,500원/USD 기준 |
| 갱신 주기 | 수동 (데이터 전달 시) |

---

## 👥 기여자

- **명훈 조** — 기획, 데이터 분석, QA
- **Hermes Agent (2호)** — 개발, 빌드, 자동화
