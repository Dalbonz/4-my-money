# 4 My Money — CLAUDE.md

> Claude가 이 레포를 열면 반드시 이 파일부터 읽을 것.
> 프로젝트 컨텍스트, 규칙, 현재 상태가 모두 여기 있음.

---

## 프로젝트 개요

**목적:** DALBONZ 가족용 투자 대시보드 + 텔레그램 알림 봇
**URL:** https://4-my-money.pages.dev (Cloudflare Pages)
**레포:** `Dalbonz/4-my-money` (Public)
**봇 레포:** `Dalbonz/investment-news-bot`
**참고 페이지:** https://dalbonz.github.io/investment-news-bot/

---

## 파일 구조

```
4-my-money/
├── dashboard-v5.html   ← 현재 최신 UI
├── CLAUDE.md           ← 이 파일 (버전 낼 때 항상 같이 커밋)
├── logo.png            ← DALBONZ 로고 (레포에 업로드 필요)
├── index.html          ← Cloudflare Pages 진입점
├── data.json           ← GitHub Actions → push
├── youtube.json        ← GAS → push
└── README.md
```

---

## dashboard-v5.html 현재 완료 (2026.05.15)

1. **인사이트** — 요약카드, 추천분야/종목(반폭+이유), 색구분 액션(서술형)
2. **브리핑** — 앵커메뉴, 주식표+스파크라인, 외환/원자재 접기, yfetch 실시간 차트
3. **미디어** — 경제·뉴스·주식 순, 핀고정, 정렬 우상단
4. **포트폴리오** — investment-news-bot 흡수 (라이트 테마)
   - 비번: SHA-256('Bong1204') + 지문/Face ID
   - 전체화면 모달, KPI 4개, 도넛+막대, 주식/현금/예적금 편집
   - Google Sheets Apps Script 양방향 연동
   - Total/Bong/Kyoung 오너 탭
5. **관리자** — 채널/수신자/발송시간/API키 시트

---

## 다음 작업

- [ ] logo.png 레포 업로드
- [ ] 브리핑 data.json 실데이터 연동
- [ ] 미디어 youtube.json 실데이터 연동
- [ ] 인사이트 AI API 연동 (08:30/17:30)
- [ ] 포트폴리오 GS Apps Script URL 연동

---

## 주요 설정값

| 항목 | 값 |
|------|-----|
| 포트폴리오 비번 | Bong1204 |
| Cloudflare Pages | 4-my-money.pages.dev |
| GitHub Actions secret | DATA_REPO_TOKEN |
| 인사이트 발송 | 08:30 / 17:30 |
| 미디어 체크 | 매 1시간, 09:00~22:00 |

---

## Claude 작업 규칙

1. **CLAUDE.md 항상 같이 커밋** — 버전 낼 때 반드시 업데이트
2. **버전 남발 금지** — str_replace 최소 변경
3. **고 사인 받고 진행**
4. **셀프 검증 필수**
5. **베이스 파일 확인 필수** — 작업 전 현재 파일 내용 확인 (구버전 원복 방지)
6. **investment-news-bot 코드 재작성 금지**
7. **로고 base64 X** — logo.png 파일 참조

---

## 삽질 기록

- base64 로고 → 흰 박스 깨짐 → logo.png 파일 참조
- @media 3컬럼 → 탭 구조 깨짐 → 제거
- Claude Code 코드 통째로 → 재해석 → str_replace 유지
- WebAuthn 지문인식 → 로컬 안됨, Cloudflare Pages 도메인에서만 동작
- 베이스 파일 잘못 선택 → 구버전 원복 → 항상 최신 파일 확인