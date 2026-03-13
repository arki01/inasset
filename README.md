# 🏛️ InAsset

**부부의 스마트 가계부 앱** — BankSalad 데이터를 자동으로 수집·분석하고, AI 챗봇으로 자연어 질의를 지원하는 개인 자산 관리 서비스

> N100 미니 PC 홈랩에서 Docker로 운영 중. Cloudflare Tunnel로 외부 접속.

---

## 주요 기능

| 페이지 | 설명 |
|--------|------|
| 📊 분석 리포트 | 이상 지출 탐지 (2σ), Burn-rate 예측, 자산 트렌드 |
| 🤖 AI 챗봇 | GPT-4o 기반 NL-to-SQL — "지난달 식비 얼마야?" |
| 💰 수입/지출 현황 | 기간·카테고리·소유자 필터, 월별 추이 차트 |
| 🏦 자산 현황 | 스냅샷 기반 자산/부채 추적, 전월 대비 delta |
| 🎯 목표 예산 | 카테고리별 월 예산 설정, 고정/변동 지출 구분 |
| 📂 데이터 관리 | BankSalad ZIP/Excel 업로드, GPT 카테고리 매핑, 재분류 |

---

## 아키텍처

```
BankSalad 이메일 → n8n (Gmail Trigger)
  → POST /api/ingest (FastAPI, 포트 3102)
    → file_handler.py (ZIP/Excel 파싱)
    → db_handler.py (SQLite)
    → Telegram 알림

BankSalad ZIP 수동 업로드
  → GPT-4o 카테고리 매핑
  → SQLite 저장

Streamlit UI (포트 3101)
  → GPT-4o NL-to-SQL 챗봇
  → Plotly 시각화
```

**기술 스택:** Python 3.11 · Streamlit · FastAPI · SQLite · OpenAI GPT-4o · Docker · n8n

---

## 실행 방법

```bash
# 1. 환경변수 설정
cp .env.example .env
# .env 파일에 API 키 입력

# 2. 인증 설정 (최초 1회)
python scripts/init_auth.py

# 3. Docker 실행
docker compose up -d
# Streamlit → http://localhost:3101
# Ingest API → http://localhost:3102
```

---

## 환경변수 (.env)

```
OPENAI_API_KEY=sk-...
INGEST_SECRET=랜덤_토큰
TELEGRAM_BOT_TOKEN=텔레그램_봇_토큰
TELEGRAM_CHAT_ID=채팅_ID
ZIP_PASSWORD_HYEONGJUN=zip_비밀번호
ZIP_PASSWORD_YUNHEE=zip_비밀번호
```

---

## 스크린샷

> 추가 예정

---

## 개발 현황

- [x] Step 1 — 로그인/인증 (관리자 승인 기반)
- [x] Step 2 — 목표 예산 설정
- [x] Step 3 — 멀티 포맷 업로더 (ZIP/Excel, 파일명 날짜 자동 감지)
- [x] Step 4 — GPT 카테고리 자동 매핑 + 검수 UI
- [x] Step 5 — 이상 지출 탐지 (2σ 기준)
- [x] Step 6 — Burn-rate 분석 (월말 지출 예측)
- [x] Step 7 — 자산 트렌드 (선형 회귀)
- [x] Step 8 — NL-to-SQL 멀티턴 챗봇
- [x] Step 9 — 이메일 기반 자동 업데이트 (n8n + Telegram)
- [x] Step 10 — 페이지별 URL 라우팅
- [ ] Step 11 — 챗봇 동적 시각화 (Plotly 차트)
- [ ] Step 12 — 모바일 PWA
