# B0 (비제로)

> 지하 0층에서 출발하는 비행선을 타고 가상 세계를 여행하며
> 힐링과 자기성찰을 경험하는 온라인 커뮤니티

어느 날 방에서 발견한 신비한 핸드폰에 설치된 앱, B0.
존재하지 않는 층으로의 초대를 받아보세요.

## 📖 프로젝트 소개

**B0**는 일상에서 벗어나 가상의 도시를 여행하며 깊이 있는 대화와 자기성찰을 경험할 수 있는 웹 기반 온라인 커뮤니티입니다.

지하 0층(B0)의 비행선 터미널에서 출발하여 6개의 테마별 도시를 방문하고, 게스트하우스에서 다른 여행자들과 만나 이야기를 나누며, 개인 숙소에서 일기와 문답지를 통해 자신을 돌아볼 수 있습니다.

### 6개 테마 도시

- **세렌시아**: 관계의 도시
- **로렌시아**: 회복의 도시
- **에테리아**: 사랑의 도시
- **드리모스**: 꿈의 도시
- **셀레니아**: 성찰의 도시
- **아벤투라**: 모험의 도시

### 핵심 기능

- 🛫 **비행선 여행**: B0 터미널에서 원하는 도시로 비행선을 타고 이동
- 🏠 **게스트하우스**: 사랑방(그룹 채팅), 라운지(1:1 대화)에서 다른 여행자들과 교류
- 🤖 **AI 호스트**: Ollama 기반 AI가 대화를 촉진하고 자연스러운 분위기 조성
- 📔 **개인 숙소**: 일기와 문답지 작성을 통한 자기성찰
- 💎 **포인트 시스템**: 활동을 통해 포인트를 얻고 여행과 숙박에 사용
- ⏰ **체크아웃 시스템**: 매일 정오 12시 자동 체크아웃 (연장 가능)

## 🏗️ 프로젝트 구조

이 저장소는 **문서 중심의 모노레포**입니다. `docs/` 디렉토리에서 프로젝트의 기획, 설계, 개발 가이드를 관리하며, 실제 코드는 Git 서브모듈로 분리된 독립 프로젝트에서 개발됩니다.

```
b0/
├── docs/                    # 📚 프로젝트 문서 (중심 저장소)
│   ├── 00-concept.md        # 프로젝트 컨셉 및 전체 기획
│   └── 01-mvp.md            # MVP 개발 로드맵 및 기능 명세
│
├── bzero-api/               # 🔧 백엔드 API (Git Submodule)
├── bzero-web/               # 💻 메인 웹앱 (Git Submodule)
└── peregrina-landing/       # 🌐 랜딩 페이지 (Git Submodule)
```

### 하위 프로젝트

각 하위 프로젝트는 **Git 서브모듈**로 관리되며 독립적인 저장소를 가집니다:

| 프로젝트 | 기술 스택 | 설명 |
|---------|----------|------|
| **bzero-api** | FastAPI, PostgreSQL, Redis, Celery | 백엔드 API 서버 |
| **bzero-web** | React 19, TypeScript, Vite, Zustand | 메인 웹 애플리케이션 |
| **peregrina-landing** | Vite, React, TypeScript | 서비스 랜딩 페이지 |

> 💡 **개발 시 주의**: 각 하위 프로젝트를 개발할 때는 `docs/` 디렉토리의 문서를 참고하세요.
> 문서는 프로젝트의 단일 진실 공급원(Single Source of Truth)입니다.

## 🚀 시작하기

### 1. 저장소 클론 및 서브모듈 초기화

```bash
# 저장소 클론
git clone <repository-url> b0
cd b0

# 서브모듈 초기화 및 업데이트
git submodule update --init --recursive
```

### 2. 문서 확인

프로젝트의 전체 기획과 개발 가이드를 먼저 확인하세요:

- [`docs/00-concept.md`](docs/00-concept.md): 프로젝트 컨셉, 사용자 여정, 핵심 기능
- [`docs/01-mvp.md`](docs/01-mvp.md): MVP 개발 로드맵, 기능 명세, 일정

### 3. 하위 프로젝트 개발

각 하위 프로젝트는 독립적으로 개발됩니다:

```bash
# 백엔드 개발
cd bzero-api
# bzero-api/CLAUDE.md 참조

# 프론트엔드 개발
cd bzero-web
# bzero-web/CLAUDE.md 참조

# 랜딩 페이지 개발
cd peregrina-landing
# README 참조
```

### 4. Git 서브모듈 작업

```bash
# 서브모듈 내에서 작업
cd bzero-api
git checkout main
# ... 작업 수행 ...
git add .
git commit -m "feat: 새로운 기능 추가"
git push

# 루트 저장소에서 서브모듈 참조 업데이트
cd ..
git add bzero-api
git commit -m "chore: Update bzero-api submodule"
git push
```

## 📚 문서

### 핵심 문서

- **[프로젝트 컨셉](docs/00-concept.md)**: B0의 세계관, 6개 도시, 사용자 여정, 핵심 기능
- **[MVP 로드맵](docs/01-mvp.md)**: 8주 개발 계획, 기능 명세, 우선순위

### 하위 프로젝트 문서

- **[bzero-api/CLAUDE.md](bzero-api/CLAUDE.md)**: 백엔드 개발 가이드
- **[bzero-web/CLAUDE.md](bzero-web/CLAUDE.md)**: 프론트엔드 개발 가이드

## 🎯 타겟 사용자

- 20-30대 직장인
- 일상에서 벗어나 휴식과 자기성찰이 필요한 사람
- 깊이 있는 대화를 원하는 사람
- 온라인 커뮤니티 경험이 있는 사람

## 🛠️ 기술 스택

### 백엔드 (bzero-api)
- FastAPI (Python 3.11+)
- PostgreSQL (데이터베이스)
- Redis (캐싱, Celery 브로커)
- Celery (백그라운드 작업)
- SQLAlchemy (비동기 ORM)
- Ollama (AI 호스트)

### 프론트엔드 (bzero-web)
- React 19
- TypeScript
- Vite
- Zustand (상태 관리)
- TanStack Query (서버 상태 관리)
- Tailwind CSS + Shadcn UI

## 🗺️ 개발 로드맵

### Phase 1: MVP (8주, 2025년 1-2월)

핵심 기능만 구현하여 정식 런칭:
- 온보딩 & 회원가입
- B0 비행선 터미널 & 2개 도시 (세렌시아, 로렌시아)
- 게스트하우스 (사랑방, 라운지)
- AI 호스트 (Ollama)
- 개인 숙소 (일기, 문답지)
- 포인트 & 체크아웃 시스템

### Phase 2: 확장 (MVP 이후)

- 나머지 4개 도시 오픈
- 구조화된 이벤트 (불멍, 별멍 등)
- 과거 기록 조회
- 명상 기능
- 알림 시스템 고도화

자세한 내용은 [`docs/01-mvp.md`](docs/01-mvp.md)를 참조하세요.
