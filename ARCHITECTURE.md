# 아키텍처 (Architecture)

> Trickcal Revive Operations Dashboard — 기술 구조 문서
> 최종 갱신: 2026-08-28 (12차 점검 08/27 데이터 반영)

## 1. 개요

순수 HTML/CSS/JS 단일 파일 대시보드 2종(한국어/중국어). 프레임워크·빌드 없이 GitHub Pages로 배포. 모든 데이터는 HTML 내 인라인 JS 배열 + `data/` 폴더 JSON으로 관리.

| 구분 | 내용 |
|------|------|
| 배포 | GitHub Pages (`jycho7254-commits/trickcal-dashboard`, branch `main`) |
| URL (KO) | https://jycho7254-commits.github.io/trickcal-dashboard/ |
| URL (ZH) | https://jycho7254-commits.github.io/trickcal-dashboard/index_zh.html |
| 차트 | Chart.js 4.4.7 (CDN) |
| 환율 | 1 USD = 1,500 KRW (고정) |
| 테마 | 다크 (#0a0a1a 배경, #00d4ff 시안 액센트), 이모지 없음 (☰ 예외) |

## 2. 파일 구조

```
trickcal-dashboard/
├── index.html              # 한국어 대시보드 (18탭, 단일 파일)
├── index_zh.html           # 중국어 대시보드 (KO와 동기화)
├── index_ko.html           # 레거시 백업
├── pages_payload.json      # (참고) 구버전 페이로드
├── data/
│   ├── inspection_metrics.json   # 점검 데이터 12시점 지표
│   ├── newuser_metrics.json      # 버전별 신규 유저 지표 (11버전)
│   ├── newuser_cache/            # 스냅샷별 user_id 캐시 CSV
│   ├── global_revenue.csv        # 글로벌 일별 매출
│   ├── jp_revenue.csv            # 일본 일별 매출 (iOS/Android)
│   └── region_transition.json    # 지역 전환 데이터
├── scripts/auto_update.py  # 매출 CSV 갱신 (경로 수정 필요)
└── README.md / ARCHITECTURE.md
```

## 3. 데이터 아키텍처 — 2개의 독립 타임라인

### 3.1 G[] 배열 (일별 매출)
- `const G=[[date, rev_usd, active, paid_active, new_accounts, pay_rate], ...]` — 최신날짜 우선(내림차순)
- 현재 196 엔트리 (2026-01-01 ~ 2026-07-15)
- 출처: Arura 시트 스냅샷(`게임대시보드_구버전/sheet_global_daily.csv`)과 KRW↔USD 교차검증(±3 USD)됨
- **점검 데이터와 독립** — 새 점검 데이터가 와도 자동 확장 안 됨. 실제 행 추가 시만 subtitle 종료일 갱신

### 3.2 점검 데이터 (2주 스냅샷)
- 출처: `8_점검데이터\MMDD 점검 데이터\`의 JP 4파트 + SGP 1 + VA 1 xlsx
- 12시점: 0326, 0409, 0423, 0507, 0521, 0604, 0618, 0702, 0716, 0730, 0813, 0827
- index.html 내 **5개의 `inspections` 배열** (약 line 1318/1777/1943/2366/2527) + `data/inspection_metrics.json`
- 지표: total/paid users, pay_rate, total_krw/usd, ARPPU, avg_cash/free_cash, 가챠 횟수, 과금군 분포

### 3.3 NEWUSER 배열 (버전별 신규 유저)
- `{"date", "label", "gm", "new_users", "new_paid", "pay_rate", "rev_krw", "arppu_krw", "share_pct"}`
- 산출: 연속 스냅샷 간 user_id diff (`8_점검데이터` xlsx 기반)
- 11버전 (04/09 ~ 08/27), `data/newuser_metrics.json`과 동기화

## 4. 인라인 배열 동기화 규칙

새 점검 시점 추가 시 **5개 inspections 배열 + NEWUSER 배열 + JSON 2종**을 모두 갱신해야 한다:

| 배열 | 위치(KO 기준) | 필드 |
|------|--------------|------|
| Full-data | ~1318 | 전체 지표 + pay_tiers |
| Minimal | ~1777 | date, total_users, paid_users |
| Mid A | ~1943 | label, pay_rate, total_krw, arppu_krw, pay_tiers |
| Mid B | ~2366 | Mid A와 동일 |
| No-label | ~2527 | label 없음 |
| NEWUSER | ~375 | 버전별 신규 유저 |

- 마지막 엔트리는 `}}` (중괄호 2개) — `}}}` 금지, 중간 엔트리 trailing 쉼표 필수
- ZH 대시보드는 pay_tiers 키가 중국어 (免费/低付费/中等付费/高付费/超高付费/鲸鱼)
- 캐릭터명: KO=리뉴아/티그/롤렛/아야/피라, ZH=丽纽雅(공식표기 莉纽阿 아님, 대시보드 관례)/提格/罗利特/绫/皮拉

## 5. 라벨 규칙 (운영 로드맵 기반)

- 점검 라벨 = 해당 주 **메인 픽업 사도명** (로드맵 xlsx '운영 로드맵' 시트 8행)
- 메인 픽업이 없는 주만 `D+N` 갭 표기 (예: 07/02 리뉴아 D+14)
- D+N은 0.75주년 리뉴아(06/18) 기준 일수
- ZH 사도명은 동일 xlsx '사도 리스트' 시트 (col C=KR, col F=CH)에서 조회

## 6. UI/UX 구조

- **스티키 상단 탭바** 17버튼 + 햄버거(☰), 페이지 전환은 `display:none/block`
- 18섹션: 매출 추이, JP 비교, 월별 요약, 스파이크 분석, 매출 피크 BM, 트렌드, 실행 과제, ROI, 체크리스트, BM 채널, 이동 매트릭스, 점검 트렌드, 코호트, LTV, 지역 비교, 전환 퍼널, 고래 추적, 신규 유저
- 스파이크 분석: 4서브탭 (전체/메이저/기저 비교/핵심 트렌드) — 각 스파이크에 원인(운영 로드맵 기반)·BM 포인트·비고 명시
- 기저 매출: <$150K 조건 일평균 + 순수 기저(<$100K) 병기 (불완전 월 표시: 7월*)

## 7. 갱신 파이프라인 (표준 절차)

1. `8_점검데이터` 신규 폴더 확인 → 컬럼명 검증 (`pd.read_excel(nrows=3)`)
2. 지표 추출 (pandas, ~60-175초, background 실행)
3. 5개 inspections 배열 + NEWUSER + JSON 갱신
4. 라벨 결정: 로드맵 8행 메인 픽업 확인 → 사도명 or D+N
5. subtitle/footer/N시점/버전 수 등 하드코딩 라벨 일괄 갱신
6. `node --check`로 스크립트 블록 문법 검증
7. 임시 스크립트 삭제 → git push → 30초 후 curl 200 확인
8. 검증: 데이터 정확성 에이전트 + UI 무결성 에이전트 상호 피드백 → OMP 자가검증

## 8. 데이터 소스 우선순위

1. **점검데이터** (`8_점검데이터\`) — 대시보드 기준
2. 운영 로드맵 xlsx (로컬) — 라벨/픽업/GM 버전
3. G[] 배열 — Arura 시트 스냅샷 (로그인벽, 수동 갱신)
4. 통합 문서1.xlsx (26년 매출 시트) — 후순위

## 9. 알려진 제약

- G[]는 7/15까지 (Arura 시트 로그인벽으로 수동 갱신 필요)
- jp_revenue.csv는 6/17까지, J[] 배열은 02/28까지 — JP 탭 일수 표기는 csv 고유 일수 기준
- `scripts/auto_update.py` 경로가 구 경로(`Desktop\전달문서\`)로 하드코딩됨 — 실행 전 수정 필요
- gviz 엔드포인트는 링크공유 해제된 시트에서 로그인벽 발생
