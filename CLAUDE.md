# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 프로젝트 개요

**B0 (비제로)**는 "지하 0층에서 출발하는 비행선을 타고 가상 세계를 여행하며 힐링과 자기성찰을 경험하는 온라인 커뮤니티" 프로젝트입니다.

이 저장소는 다음 3개의 하위 프로젝트로 구성된 모노레포입니다:

1. **bzero-api**: FastAPI 기반 백엔드 API 서버
2. **bzero-web**: React + Vite 기반 메인 웹 애플리케이션
3. **peregrina-landing**: Vite 기반 랜딩 페이지

각 하위 프로젝트는 Git 서브모듈로 관리되며, 독립적으로 개발 및 배포됩니다.

## 프로젝트 구조

```
/
├── bzero-api/           # 백엔드 API (git submodule)
├── bzero-web/           # 메인 웹앱 (git submodule)
├── peregrina-landing/   # 랜딩 페이지 (git submodule)
├── docs/                # 프로젝트 문서
│   ├── 00-concept.md    # 프로젝트 컨셉 및 기획
│   ├── 01-mvp.md        # MVP 개발 로드맵 및 기능 명세
│   ├── 02-design-system.md  # 디자인 시스템 (색상, 타이포, 컴포넌트)
│   ├── 03-screens.md    # 화면 명세서 (와이어프레임)
│   └── workflow.md      # Claude Code 작업 워크플로우
└── .claude/
    └── skills/
        └── brand-guide/ # 브랜드 가이드 스킬
```

## 하위 프로젝트별 명령어

각 하위 프로젝트는 독립적인 개발 환경을 가지고 있으며, 해당 디렉토리 내에서 작업해야 합니다.

### bzero-api (백엔드)

**기술 스택**: FastAPI, PostgreSQL, Redis, Celery, SQLAlchemy (비동기)

```bash
cd bzero-api

# 개발 서버 실행 (http://0.0.0.0:8000)
uv run dev

# 린팅 및 포매팅
uv run ruff check --fix .
uv run ruff format .

# 테스트
uv run pytest

# 데이터베이스 마이그레이션
uv run alembic revision --autogenerate -m "설명"
uv run alembic upgrade head
uv run alembic downgrade -1
```

자세한 내용은 `bzero-api/CLAUDE.md` 참조

### bzero-web (메인 웹앱)

**기술 스택**: React 19, TypeScript, Vite, Zustand, TanStack Query, Tailwind CSS, Shadcn UI

```bash
cd bzero-web

# 개발 서버 실행 (http://localhost:5173)
pnpm dev

# 빌드
pnpm build

# 린팅 및 포매팅
pnpm lint
pnpm lint:fix
pnpm format
```

자세한 내용은 `bzero-web/CLAUDE.md` 참조

### peregrina-landing (랜딩 페이지)

**기술 스택**: Vite, React, TypeScript, Tailwind CSS, Shadcn UI

```bash
cd peregrina-landing

# 개발 서버 실행
npm run dev

# 빌드
npm run build
```

## Git 서브모듈 작업 시 주의사항

이 프로젝트는 Git 서브모듈을 사용합니다:

```bash
# 서브모듈 초기화 및 업데이트
git submodule update --init --recursive

# 서브모듈의 최신 변경사항 가져오기
git submodule update --remote

# 서브모듈 내에서 작업 시
cd bzero-api  # 또는 bzero-web, peregrina-landing
# ... 작업 수행 ...
git add .
git commit -m "message"
git push

# 루트 저장소에서 서브모듈 변경사항 반영
cd ..
git add bzero-api  # 서브모듈 커밋 해시 업데이트
git commit -m "Update bzero-api submodule"
git push
```

## 핵심 비즈니스 로직

### 사용자 여정

1. **온보딩**: 신비한 핸드폰 앱 발견 → B0(지하 0층) 초대 → 회원가입 (1000P 지급)
2. **B0 비행선 터미널**: 테마별 도시 탐색 → 비행선 티켓 구매 (일반 300P/쾌속 500P)
3. **도시 도착**: 게스트하우스 유형 선택 (혼합형/조용한 방)
4. **활동**:
   - 사랑방: 단체 대화 (최대 6명/룸)
   - 라운지: 1:1 대화
   - 개인 숙소: 일기 작성 (50P), 문답지 작성 (50P)
5. **체크아웃**: 매일 정오 12:00 → 연장(300P) 또는 B0로 복귀

### 5개 도시 테마

| 도시명 | 테마 | 분위기 |
|-------|-----|-------|
| **세렌시아** | 관계 | 노을빛 항구 마을 |
| **로렌시아** | 회복 | 숲 속 오두막 |
| **엠마시아** | 희망 | 빛이 머무는 초원 |
| **다마린** | 고요 | 물안개 속 항구 |
| **갈리시아** | 성찰 | 석양이 머무는 순례의 길 |

### 게스트하우스 시스템

- **혼합형**: AI 호스트가 대화 촉진, 구조화된 이벤트 (불멍, 별멍 등)
- **조용한 방**: 개인적 대화와 자기성찰 중심, AI는 환영 메시지만 표시
- **자동 룸 배정**: 6명 미만 룸에 자동 배정, 모든 룸이 6명이면 새 룸 생성

### AI 호스트 (Ollama)

- 5분 무대화 시 대화 촉진 질문 생성
- 혼합형 게스트하우스에서만 적극 개입
- 최근 대화 맥락과 도시 테마를 반영한 자연스러운 질문

### 포인트 시스템

- **획득**: 회원가입(1000P), 일기(50P/일), 문답지(50P/도시)
- **사용**: 일반 비행선(300P), 쾌속 비행선(500P), 숙박 연장(300P/일)

## 개발 프로세스

### Phase 1: MVP (8주, 240시간)

핵심 기능만 구현하여 정식 런칭:

- 온보딩 & 회원가입
- B0 비행선 터미널 & 도시 탐색
- 비행선 티켓 구매 & 이동
- 게스트하우스, 사랑방, 라운지 (6명/룸 자동 배정)
- AI 호스트 (Ollama 기반)
- 개인 숙소 (일기, 문답지)
- 포인트 시스템
- 체크아웃 & 시간 시스템
- 프로필 & 설정
- 데이터 수집 & 기능 제어

자세한 내용은 `docs/01-mvp.md` 참조

### Phase 2: 확장 기능 (MVP 이후)

- 구조화된 이벤트 (불멍, 별멍 등)
- 과거 일기/문답지 조회
- 명상 기능
- 비밀번호 찾기/재설정
- 회원 탈퇴
- 알림 시스템 고도화
- 나머지 3개 도시 확장 (엠마시아, 다마린, 갈리시아)

## 타겟 사용자

- 20-30대 직장인
- 일상에서 벗어나 휴식과 자기성찰이 필요한 사람
- 깊이 있는 대화를 원하는 사람
- 온라인 커뮤니티 경험이 있는 사람

## 개발 시 주의사항

### 보안

- 민감한 정보(API 키, 토큰, 비밀번호 등)는 절대 코드에 하드코딩하지 않음
- 환경 변수(.env)를 통해 관리
- 채팅 내용, 일기, 문답지는 서버에서 수집/분석하지 않음 (개인정보 보호)

### 성능

- 채팅 히스토리는 7일간만 보관
- 메시지 로딩은 페이지네이션 사용 (100개씩)
- AI 호스트 응답 시간은 2초 이내
- 비동기 I/O 우선 사용 (FastAPI, SQLAlchemy async)

### 데이터베이스

- UUID v7을 기본 ID로 사용 (RFC 9562 표준)
- 비동기 SQLAlchemy 사용
- Alembic 마이그레이션은 항상 검토 후 커밋

### 사용자 경험

- 욕설 및 부적절한 내용 필터링
- 스팸 방지: 메시지는 2초에 1회, 대화 신청은 1분에 3회 제한
- 신고/차단 기능 제공
- 최대 줄 길이 제한 (메시지 500자, 일기 500자, 문답 200자)

## 문서

- **프로젝트 컨셉**: `docs/00-concept.md`
- **MVP 개발 로드맵**: `docs/01-mvp.md`
- **디자인 시스템**: `docs/02-design-system.md`
- **화면 명세서**: `docs/03-screens.md`
- **작업 워크플로우**: `docs/workflow.md`
- **백엔드 가이드**: `bzero-api/CLAUDE.md`
- **프론트엔드 가이드**: `bzero-web/CLAUDE.md`

## Claude Code 스킬

- **brand-guide**: 브랜드 가이드 스킬 (`.claude/skills/brand-guide/`)
  - 문서 작성, 프론트엔드 디자인, UI/UX 작업, 카피라이팅 시 사용
  - 색상, 타이포그래피, 톤앤매너, 세계관 용어 가이드 제공