# 4 My Money — CLAUDE.md

> Claude가 이 레포를 열면 반드시 이 파일부터 읽을 것.
> 프로젝트 컨텍스트, 규칙, 현재 상태가 모두 여기 있음.

---

## 프로젝트 개요

**목적:** DALBONZ 가족용 투자 대시보드 + 텔레그램 알림 봇
**URL:** https://4-my-money.pages.dev (Cloudflare Pages)
**레포:** `Dalbonz/4-my-money` (Public — data.json, youtube.json 서빙용)
**봇 레포:** `Dalbonz/investment-news-bot` (Private 전환 권장)
**참고 페이지:** https://dalbonz.github.io/investment-news-bot/ (브리핑/포트폴리오 코드 원본)

---

## 파일 구조

```
4-my-money/
├── dashboard-v5.html   ← 현재 커밋된 최신 UI (v6 롤백 후 v5가 기준)
├── index.html          ← Cloudflare Pages 진입점
├── data.json           ← investment-news-bot → GitHub Actions → push
├── youtube.json        ← GAS(유튜브봇) → push
└── README.md
```

---

## 현재 UI 상태 (dashboard-v5.html 기준)

### 4탭 구조
1. **인사이트** — 오늘 한 줄 요약 + 한국/미국/AI전망 + 추천분야/종목(반폭) + 오늘액션(서술형)
2. **브리핑** — 주요지표(칩+차트) + 외환/원자재(접기) + 주식표+스파크라인 + 뉴스
3. **미디어** — 경제·뉴스·주식 카테고리, 카테고리/최신/채널 정렬, 핀고정
4. **포트폴리오** — 비밀번호(1234)+지문인식 잠금, 구글시트 구조 재해석

### 디자인 시스템
- Apple 라이트 테마
- DALBONZ 로고: 레포에 `logo.png` 올리고 `<img src="logo.png">` 로 참조할 것 (base64 X)
- 네브바: 다크(#1d1d1f), 탭바: 라이트 sticky
- 색상: --primary:#0066cc / --up:#34c759 / --down:#ff3b30

---

## 데이터 파이프라인

```
[GitHub Actions] news_bot.py
    → data.json → 4-my-money 레포 push
    secret: DATA_REPO_TOKEN

[GAS] youtube_summary_bot_v14
    → youtube.json → 4-my-money 레포 push
    채널: 12시에 만나요 / 경제사냥꾼 / 슈페tv
    수신자: 8649321702(본인) / 8799181333(경) / 8689376301(대규)
    트리거: 매 1시간 runScheduled
```

---

## 다음 작업 (우선순위순)

### Phase 1 — UI 마무리
- [ ] **브리핑** → `investment-news-bot` index.html 코드 흡수
  - 상단 차트: 전일 종가 기준선 + 실시간 표시
  - 나머지 구조 그대로 가져올 것 (3일 걸린 코드 재작성 금지)
- [ ] **포트폴리오** → `investment-news-bot` 포트폴리오 코드 흡수
  - 구글시트 양방향 연동 이미 구현되어 있음
  - 라이트 테마로만 변환
- [ ] **로고** → `logo.png` 레포에 업로드 후 img 태그로 참조
- [ ] **인사이트** → 추천분야/종목 반폭 나란히 + 이유 한 줄씩 (현재 full로 깨져있음)

### Phase 2 — 기능 연동
- [ ] 인사이트 AI API 연동 (Claude Haiku, 08:30/17:30 발송)
  - 08:30: 미국 장 결과 → 한국장 전망
  - 17:30: 한국장 결과 → 미국장 전망
- [ ] 미디어 youtube.json 실데이터 연동
- [ ] 구글시트 포트폴리오 Apps Script URL 연동

---

## 주요 설정값

| 항목 | 값 |
|------|-----|
| 포트폴리오 비번 | 1234 (임시, 배포 전 변경) |
| Cloudflare Pages | 4-my-money.pages.dev |
| GitHub Actions secret | DATA_REPO_TOKEN |
| 인사이트 발송 | 08:30 / 17:30 |
| 미디어 체크주기 | 매 1시간, 09:00~22:00만 발송 |

---

## Claude 작업 규칙 (반드시 준수)

1. **버전 남발 금지** — 수정 전 원인 정확히 특정, str_replace로 최소 변경
2. **고 사인 받고 진행** — 수정 목록 확인 후 승인 받을 것
3. **셀프 검증 필수** — 변경 후 코드상으로 렌더링 결과 확인
4. **investment-news-bot 코드 재작성 금지** — 이미 동작하는 코드는 흡수할 것
5. **로고는 base64 X** — 레포 파일 참조 방식 사용
6. **보안 주의** — GAS 코드에 API키 하드코딩 노출됨, 재발급 권장
   - 재발급 대상: CLAUDE_API_KEY, TELEGRAM_BOT_TOKEN

---

## 구글시트 포트폴리오 구조

**Sheet1 (자산 현황):**
- KPI 4개: 입출금합계 / 예적금합계 / 보험합계 / 주식합계
- 도넛차트: 주식(66.4%) / 예적금(13.9%) / 현금(15%) / 보험(4.8%)
- 월별 누적 세로막대 그래프
- 현금 보유 현황 테이블 (계좌/통화/잔액)
- 예적금 현황 테이블 (구분/은행/만기일/이율/만기해지금액)

**Sheet2 (주식):**
- 섹터별 도넛 + 기간별 수익/손실 차트
- 종목 테이블: 종목명/수량/총수익%/1주평균금액/총금액/일간수익/차트
- 실제 보유 종목: 브로드컴, 엔비디아, 마이크로소프트, 한국전력, 애플, 테슬라, SK하이닉스, TSMC, IVV_S&P500, 네이버 등

---

## 참고 이슈 / 삽질 기록

- `cwrap-full` 차트 전체폭: `margin:-20px` + `overflow:hidden` 조합이 렌더링 환경마다 다르게 동작함 → 포기하고 타일 안에 넣는 게 나음
- 테이블 스파크라인 폭: `table-layout:fixed` + `.spark-td svg{width:100%}` 조합 필요
- base64 로고: 네브바에서 흰 박스로 깨짐 → 레포 파일 참조로 해결
- 포트폴리오 비번: WebAuthn 지문인식 로컬에서 동작 안 함 (rpId 이슈) → Cloudflare Pages 도메인에서만 동작
