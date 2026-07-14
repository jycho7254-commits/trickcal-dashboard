# 🎮 Trickcal Revive Revenue Dashboard

실시간 매출 대시보드 · BM 분석 · 운영 데이터 시각화

## 📊 대시보드

| 버전 | 파일 | 설명 |
|------|------|------|
| 🇰🇷 한국어 | `index_ko.html` | 한국어 실시간 대시보드 |
| 🇨🇳 中文 | `index_zh.html` | 중국어 실시간 대시보드 |

**Tech Stack:** Single-file HTML + Chart.js 4.4.7 (CDN), 다크 테마

## 📁 데이터 파일 (`data/`)

| 파일 | 출처 | 내용 |
|------|------|------|
| `global_revenue.csv` | 통합문서1.xlsx > "26년 매출" | 글로벌 일별 매출/DAU/ARPPU/유료전환율 (169일) |
| `jp_revenue.csv` | 통합문서1.xlsx > "JP" | JP iOS 단일 시장 매출 (337일) |

### 컬럼 설명

**global_revenue.csv**
- `date` - 날짜 (YYYY-MM-DD)
- `active_accounts` - 활성 계정수 (DAU)
- `paid_active` - 활성 유료 계정수
- `new_accounts` - 신규 계정수
- `revenue_usd` - 총 매출 (USD)
- `paying_accounts` - 유료 계정수
- `pay_rate` - 활성 계정 유료전환율
- `arpu` - 활성 ARPU (USD)
- `arppu` - 활성 ARPPU (USD)

**jp_revenue.csv**
- `date` - 날짜
- `platform` - 플랫폼 (ios)
- `country` - 국가 (日本)
- `active_accounts` - JP 활성 계정수
- `revenue_usd` - JP 매출 (USD)
- `paying_accounts` - JP 유료 계정수
- `pay_rate` - JP 유료전환율
- `arpu` - JP ARPU

## 📈 주요 수치 (2026 H1)

| 항목 | 수치 |
|------|------|
| 총 매출 | $19,865,749 |
| 일평균 매출 | $117,548 |
| 최고 일매출 | $976,414 (0.75주년, 6/18) |
| 환율 | 1 USD = 1,500 KRW |

## 🔧 데이터 업데이트

1. `통합문서1.xlsx` 최신 데이터 확보
2. `python3 extract_data.py` 실행 → `data/` CSV 갱신
3. `index_ko.html` / `index_zh.html`의 JavaScript 데이터 배열 갱신
4. Netlify 배포

## ⚠️ 주의사항

- 불완전 월(6월 18일)은 완전 월과 직접 비교 금지
- 기저매출(<$100K)와 전체평균 모두 표시
- 모든 데이터는 원본 출처 인용

## 📋 BM 구조 (8개 과금 채널)

1. 충전 (엘리프 7단계)
2. 월정액 (4종)
3. 가챠 (6종)
4. 패키지 (주차당 20~29개)
5. 패스 (3종)
6. 부띠끄 샵 (35+ 스킨)
7. 인테리어/펫
8. 토끼 토큰

---

Epid Games · Trickcal Revive Operations
