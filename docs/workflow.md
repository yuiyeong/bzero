# Claude Code 작업 워크플로우

B0 프로젝트를 Claude Code와 함께 개발하는 실전 가이드입니다.

---

## 📋 목차

1. [프로젝트 시작 전 준비](#1-프로젝트-시작-전-준비)
2. [기능 개발 워크플로우](#2-기능-개발-워크플로우)
3. [백엔드 작업 (bzero-api - Clean Architecture)](#3-백엔드-작업-bzero-api---clean-architecture)
4. [프론트엔드 작업 (bzero-web - Feature-based)](#4-프론트엔드-작업-bzero-web---feature-based)
5. [Git 서브모듈 작업](#5-git-서브모듈-작업)
6. [Claude Code 활용 팁](#6-claude-code-활용-팁)
7. [문제 해결](#7-문제-해결)
8. [참고 자료](#8-참고-자료)

---

## 1. 프로젝트 시작 전 준비

### 1.1 설계 문서 작성 (Claude Code 활용)

모든 코딩을 시작하기 전에 **설계 문서를 먼저 작성**합니다.

#### ERD (Entity Relationship Diagram) 설계

```
💬 프롬프트 예시:

"@docs/01-mvp.md 문서를 기반으로 B0 프로젝트의 ERD를 설계해줘.
다음 항목들을 포함해야 해:
- User (사용자)
- City (도시)
- Guesthouse (게스트하우스)
- Room (채팅룸)
- Message (메시지)
- Diary (일기)
- Questionnaire (문답지)
- PointTransaction (포인트 거래 내역)
- Ticket (비행선 티켓)

각 엔티티의 필드, 데이터 타입, 관계(1:N, N:M), 인덱스를 명시해줘.
PostgreSQL을 사용하고, ID는 ULID를 사용할 거야."
```

**Claude가 생성한 ERD를 검토**하고, `docs/erd.md`에 저장합니다.

#### 도메인 모델 정리

```
💬 프롬프트 예시:

"B0 프로젝트의 도메인 모델을 정리해줘.
다음을 포함해야 해:
- 핵심 도메인 객체 (User, City, Room, Message 등)
- 각 도메인의 책임과 역할
- 도메인 간 관계
- 비즈니스 로직이 어느 도메인에 속하는지

FastAPI와 SQLAlchemy를 사용한 Clean Architecture + 도메인 모델 패턴으로 구성할 거야:
- Domain Layer: 엔티티, 값 객체, 도메인 서비스
- Application Layer: 유스케이스(비즈니스 로직)
- Infrastructure Layer: 리포지토리 구현체, ORM 모델
- Presentation Layer: API 엔드포인트, DTO/스키마"
```

**Claude가 생성한 도메인 모델을 검토**하고, `docs/domain-model.md`에 저장합니다.

#### API 명세 작성

```
💬 프롬프트 예시:

"@docs/01-mvp.md 의 10개 기능을 기반으로 RESTful API 명세를 작성해줘.
각 엔드포인트마다 다음을 포함:
- HTTP Method
- URL Path
- Request Body (JSON Schema)
- Response Body (JSON Schema)
- Status Codes
- 인증 필요 여부

예시:
POST /api/auth/register
POST /api/tickets/purchase
GET /api/cities
등"
```

**Claude가 생성한 API 명세를 검토**하고, `docs/api-spec.md`에 저장합니다.

### 1.2 초기 프로젝트 구조 확인

프로젝트는 이미 생성되어 있으므로, 구조를 확인하고 필요한 부분을 추가합니다.

#### 백엔드 구조 (Clean Architecture)

```
💬 프롬프트 예시:

"bzero-api 프로젝트의 Clean Architecture 구조를 확인하고 설명해줘.
다음 구조를 따라야 해:

bzero-api/
├── app/
│   ├── domain/              # 도메인 계층
│   │   ├── entities/        # 도메인 엔티티
│   │   ├── value_objects/   # 값 객체
│   │   ├── repositories/    # 리포지토리 인터페이스
│   │   └── services/        # 도메인 서비스
│   ├── application/         # 애플리케이션 계층
│   │   ├── use_cases/       # 유스케이스 (비즈니스 로직)
│   │   └── dtos/            # 데이터 전송 객체
│   ├── infrastructure/      # 인프라 계층
│   │   ├── db/              # DB 연결, ORM 모델
│   │   ├── repositories/    # 리포지토리 구현체
│   │   └── external/        # 외부 서비스 연동
│   ├── presentation/        # 프레젠테이션 계층
│   │   ├── api/             # API 엔드포인트
│   │   │   └── v1/
│   │   └── schemas/         # Pydantic 스키마
│   └── core/                # 공통 설정, 유틸리티
├── alembic/                 # DB 마이그레이션
├── tests/
└── pyproject.toml

누락된 디렉토리가 있으면 생성해줘."
```

#### 프론트엔드 구조 (Feature-based)

```
💬 프롬프트 예시:

"bzero-web 프로젝트의 Feature-based 구조를 확인하고 설명해줘.
다음 구조를 따라야 해:

bzero-web/
├── src/
│   ├── features/            # 기능별 모듈
│   │   ├── auth/            # 인증
│   │   │   ├── components/
│   │   │   ├── hooks/
│   │   │   ├── api/
│   │   │   └── store/
│   │   ├── city/            # 도시 탐색
│   │   ├── guesthouse/      # 게스트하우스
│   │   ├── chat/            # 채팅
│   │   └── diary/           # 일기
│   ├── shared/              # 공유 리소스
│   │   ├── components/      # 공통 컴포넌트
│   │   ├── hooks/           # 공통 훅
│   │   ├── utils/           # 유틸리티
│   │   ├── types/           # 타입 정의
│   │   └── api/             # API 클라이언트 기본 설정
│   ├── app/                 # 앱 설정
│   │   ├── routes/          # 라우팅
│   │   └── providers/       # 프로바이더
│   └── assets/              # 정적 리소스
├── public/
└── package.json

누락된 디렉토리가 있으면 생성해줘."
```

#### 공통 설정 파일 작성

Feature를 개발하기 전에 공통으로 사용할 설정 파일을 먼저 작성합니다.

```
💬 프롬프트 예시:

"bzero-web/src/shared/api/client.ts 에
공통 API 클라이언트 설정을 작성해줘.

axios 인스턴스를 생성하고:
- baseURL: 환경변수 VITE_API_URL 사용
- timeout: 10000ms
- headers: Content-Type application/json
- 요청 인터셉터: Authorization 헤더에 토큰 자동 추가
- 응답 인터셉터: 401 에러 시 자동 로그아웃 처리
- 에러 응답 변환

export default로 내보내서 각 feature의 API에서 사용할 수 있게 해줘."
```

---

## 2. 기능 개발 워크플로우

MVP 문서의 **10개 기능을 순서대로** 개발합니다. 각 기능마다 다음 순서를 따릅니다:

```
1. 기능 분석 (Claude와 대화)
2. 백엔드 개발 (bzero-api - Clean Architecture)
   ├── 2.1 도메인 엔티티/값 객체 작성 (Domain Layer)
   ├── 2.2 리포지토리 인터페이스 작성 (Domain Layer)
   ├── 2.3 도메인 서비스 작성 (Domain Layer - 필요시)
   ├── 2.4 유스케이스 작성 (Application Layer - 비즈니스 로직)
   ├── 2.5 ORM 모델 작성 (Infrastructure Layer)
   ├── 2.6 리포지토리 구현체 작성 (Infrastructure Layer)
   ├── 2.7 API 엔드포인트 작성 (Presentation Layer)
   ├── 2.8 Pydantic 스키마/DTO 작성 (Presentation Layer)
   ├── 2.9 의존성 주입 설정 (모든 계층 연결)
   ├── 2.10 마이그레이션 생성
   ├── 2.11 테스트 작성 (단위 테스트 + 통합 테스트)
   └── 2.12 개발 서버 실행 및 테스트
3. 프론트엔드 개발 (bzero-web - Feature-based)
   ├── 3.1 Feature 디렉토리 구조 확인/생성
   ├── 3.2 타입 정의 작성 (features/{feature}/types/)
   ├── 3.3 API 클라이언트 작성 (features/{feature}/api/)
   ├── 3.4 상태 관리 작성 (features/{feature}/store/)
   ├── 3.5 커스텀 훅 작성 (features/{feature}/hooks/)
   ├── 3.6 컴포넌트 작성 (features/{feature}/components/)
   ├── 3.7 페이지 조합 및 라우팅 (app/routes/)
   ├── 3.8 Feature Public API 작성
   └── 3.9 개발 서버 실행 및 테스트
4. 통합 테스트 (E2E)
5. 커밋 & 푸시
```

### 예시: 기능 1 - 온보딩 & 회원가입 개발

#### Step 1: 기능 분석

```
💬 프롬프트:

"@docs/01-mvp.md 의 '기능 1: 온보딩 & 회원가입'을 개발하려고 해.
먼저 이 기능에 필요한 것들을 정리해줘:
- 필요한 도메인 엔티티와 값 객체
- 필요한 유스케이스 (비즈니스 로직)
- 필요한 DB 테이블 (ORM 모델)
- 필요한 API 엔드포인트
- 백엔드 작업 목록 (Clean Architecture 계층별로)
- 프론트엔드 작업 목록 (Feature-based 구조로)"
```

Claude가 정리한 내용을 확인하고, 작업 계획을 수립합니다.

#### Step 2: 백엔드 개발 (Clean Architecture)

각 계층별로 Claude Code와 작업합니다. 자세한 내용은 [3. 백엔드 작업](#3-백엔드-작업-bzero-api---clean-architecture)을 참고하세요.

#### Step 3: 프론트엔드 개발 (Feature-based)

백엔드가 완료되면 프론트엔드 작업을 시작합니다. 자세한 내용은 [4. 프론트엔드 작업](#4-프론트엔드-작업-bzero-web---feature-based)을 참고하세요.

---

## 3. 백엔드 작업 (bzero-api - Clean Architecture)

### 3.1 도메인 엔티티/값 객체 작성 (Domain Layer)

도메인 계층은 비즈니스 로직의 핵심으로, 외부 의존성이 없어야 합니다.

```
💬 프롬프트 예시:

"bzero-api/app/domain/entities/user.py 에 User 도메인 엔티티를 작성해줘.
다음을 포함해야 해:

필드:
- id: ULID (문자열)
- email: Email (값 객체)
- hashed_password: str
- nickname: Nickname (값 객체)
- emoji: str
- points: int (기본값 1000)
- created_at: datetime
- updated_at: datetime

비즈니스 규칙 (도메인 로직):
- 포인트는 0 미만이 될 수 없음
- 포인트 차감/추가 메서드 제공
- 비밀번호 검증 메서드 제공

순수한 Python 클래스로 작성하고, ORM이나 프레임워크 의존성은 없어야 해."
```

```
💬 값 객체 작성 예시:

"bzero-api/app/domain/value_objects/email.py 에 Email 값 객체를 작성해줘.
- 이메일 형식 검증
- 불변 객체 (@dataclass(frozen=True))
- 동등성 비교 구현

bzero-api/app/domain/value_objects/nickname.py 에 Nickname 값 객체를 작성해줘.
- 2-10자 검증
- 특수문자 검증
- 불변 객체"
```

**Claude가 생성한 코드를 확인**하고, 도메인 규칙이 올바르게 구현되었는지 검토합니다.

### 3.2 리포지토리 인터페이스 작성 (Domain Layer)

리포지토리는 도메인 계층에 인터페이스(추상 클래스)로 정의하고, 구현체는 인프라 계층에 작성합니다.

```
💬 프롬프트 예시:

"bzero-api/app/domain/repositories/user_repository.py 에
UserRepository 인터페이스를 작성해줘.

from abc import ABC, abstractmethod를 사용해서:

추상 메서드:
- async def create(user: User) -> User
- async def get_by_id(user_id: str) -> User | None
- async def get_by_email(email: Email) -> User | None
- async def get_by_nickname(nickname: Nickname) -> User | None
- async def update(user: User) -> User
- async def delete(user_id: str) -> None

도메인 엔티티와 값 객체를 타입으로 사용하고,
외부 프레임워크 의존성은 없어야 해."
```

### 3.3 도메인 서비스 작성 (Domain Layer - 필요시)

도메인 서비스는 여러 엔티티에 걸친 비즈니스 로직이 있을 때 작성합니다. 단순한 경우 생략 가능합니다.

```
💬 프롬프트 예시:

"bzero-api/app/domain/services/password_service.py 에
비밀번호 관련 도메인 서비스를 작성해줘.

메서드:
- hash_password(plain_password: str) -> str
  비밀번호를 해싱 (bcrypt 사용)

- verify_password(plain_password: str, hashed_password: str) -> bool
  비밀번호 검증

순수한 비즈니스 로직만 포함하고,
외부 라이브러리(bcrypt)는 인터페이스로 추상화해야 해."
```

**참고**: 인증 관련 로직은 도메인 서비스로 분리하는 것이 좋습니다.

### 3.4 유스케이스 작성 (Application Layer)

유스케이스는 애플리케이션의 비즈니스 로직을 담당합니다. 도메인 엔티티와 리포지토리를 조합하여 작업을 수행합니다.

```
💬 프롬프트 예시:

"bzero-api/app/application/use_cases/register_user.py 에
RegisterUserUseCase를 작성해줘.

의존성:
- user_repository: UserRepository (도메인 리포지토리 인터페이스)
- password_service: PasswordService (도메인 서비스)

실행 흐름 (execute 메서드):
1. 입력 데이터: email, password, nickname, emoji
2. Email, Nickname 값 객체 생성 (검증 포함)
3. 이메일 중복 확인 (repository.get_by_email)
4. 닉네임 중복 확인 (repository.get_by_nickname)
5. 비밀번호 해싱 (password_service.hash_password)
6. User 도메인 엔티티 생성 (초기 포인트 1000)
7. 사용자 저장 (repository.create)
8. User 엔티티 반환

예외 처리:
- 중복 이메일/닉네임 시 도메인 예외 발생
- 검증 실패 시 도메인 예외 발생"
```

```
💬 로그인 유스케이스 예시:

"bzero-api/app/application/use_cases/login_user.py 에
LoginUserUseCase를 작성해줘.

의존성:
- user_repository: UserRepository
- password_service: PasswordService
- token_service: TokenService (JWT 생성)

실행 흐름:
1. 이메일로 사용자 조회
2. 사용자 없으면 예외
3. 비밀번호 검증
4. 검증 실패 시 예외
5. JWT 액세스 토큰 생성
6. 토큰 반환"
```

### 3.5 ORM 모델 작성 (Infrastructure Layer)

ORM 모델은 데이터베이스 테이블과 매핑되며, 도메인 엔티티와는 분리됩니다.

```
💬 프롬프트 예시:

"bzero-api/app/infrastructure/db/models/user_model.py 에
UserModel ORM 모델을 작성해줘.

필드:
- id: String (ULID, Primary Key)
- email: String (Unique, Index)
- hashed_password: String
- nickname: String (Unique, Index)
- emoji: String
- points: Integer
- created_at: DateTime
- updated_at: DateTime

SQLAlchemy 2.0의 Mapped, mapped_column을 사용하고,
비동기를 위해 AsyncAttrs를 사용해줘.

중요: 이 모델은 순수한 데이터 매핑용이며,
비즈니스 로직은 포함하지 않아야 해."
```

### 3.6 리포지토리 구현체 작성 (Infrastructure Layer)

도메인 계층의 리포지토리 인터페이스를 구현합니다. ORM과 도메인 엔티티 간의 변환을 담당합니다.

```
💬 프롬프트 예시:

"bzero-api/app/infrastructure/repositories/user_repository_impl.py 에
UserRepositoryImpl을 작성해줘.

UserRepository 인터페이스를 구현하고:

의존성:
- AsyncSession (SQLAlchemy 비동기 세션)

메서드 구현:
- create(): UserModel을 생성하고, 도메인 엔티티로 변환하여 반환
- get_by_id(): DB에서 조회 후 도메인 엔티티로 변환
- get_by_email(): Email 값 객체를 문자열로 변환하여 조회
- get_by_nickname(): Nickname 값 객체를 문자열로 변환하여 조회
- update(): 도메인 엔티티를 ORM 모델로 변환하여 업데이트
- delete(): ID로 삭제

중요:
1. ORM 모델 ↔ 도메인 엔티티 변환 로직 포함
2. 값 객체 ↔ 원시 타입 변환 처리
3. 비동기 쿼리 사용 (await)"
```

**매퍼 함수 작성**:
```
💬 프롬프트:

"같은 파일에 매퍼 함수를 작성해줘:

- to_domain(user_model: UserModel) -> User
  ORM 모델을 도메인 엔티티로 변환

- to_model(user: User) -> UserModel
  도메인 엔티티를 ORM 모델로 변환"
```

### 3.7 API 엔드포인트 작성 (Presentation Layer)

API 계층은 HTTP 요청을 받아 유스케이스를 호출하고 응답을 반환합니다.

```
💬 프롬프트 예시:

"bzero-api/app/presentation/api/v1/auth.py 에
인증 관련 API 엔드포인트를 작성해줘.

FastAPI의 APIRouter를 사용하고,
의존성 주입(Depends)으로 유스케이스를 받아야 해.

엔드포인트:

POST /api/v1/auth/register
- Request Body: RegisterRequest (Pydantic 스키마)
- Response: UserResponse (201 Created)
- 로직: RegisterUserUseCase.execute() 호출

POST /api/v1/auth/login
- Request Body: LoginRequest
- Response: TokenResponse (200 OK)
- 로직: LoginUserUseCase.execute() 호출

GET /api/v1/auth/check-email/{email}
- Response: {available: bool}
- 로직: UserRepository.get_by_email() 호출

GET /api/v1/auth/check-nickname/{nickname}
- Response: {available: bool}
- 로직: UserRepository.get_by_nickname() 호출

예외 처리:
- 도메인 예외를 HTTP 예외로 변환 (400, 409 등)"
```

### 3.8 Pydantic 스키마/DTO 작성 (Presentation Layer)

API 요청/응답을 위한 Pydantic 스키마를 작성합니다.

```
💬 프롬프트 예시:

"bzero-api/app/presentation/schemas/auth_schema.py 에
인증 관련 스키마를 작성해줘.

Request 스키마:

1. RegisterRequest
   - email: EmailStr
   - password: str (최소 8자)
   - nickname: str (2-10자)
   - emoji: str

2. LoginRequest
   - email: EmailStr
   - password: str

Response 스키마:

1. UserResponse
   - id: str
   - email: str
   - nickname: str
   - emoji: str
   - points: int
   - created_at: datetime

2. TokenResponse
   - access_token: str
   - token_type: str (기본값 'bearer')

Pydantic V2를 사용하고,
field_validator로 검증 규칙 추가해줘."
```

### 3.9 의존성 주입 설정

모든 계층을 연결하기 위한 의존성 주입을 설정합니다.

```
💬 프롬프트 예시:

"bzero-api/app/core/dependencies.py 에 의존성 주입 설정을 작성해줘.

FastAPI의 Depends를 사용해서:

1. DB 세션 의존성:
   - get_db() -> AsyncSession
   - yield 패턴으로 세션 관리

2. 리포지토리 의존성:
   - get_user_repository(db: AsyncSession) -> UserRepository
   - UserRepositoryImpl 인스턴스 반환

3. 서비스 의존성:
   - get_password_service() -> PasswordService
   - get_token_service() -> TokenService

4. 유스케이스 의존성:
   - get_register_user_use_case(
       user_repository: UserRepository,
       password_service: PasswordService
     ) -> RegisterUserUseCase

   - get_login_user_use_case(
       user_repository: UserRepository,
       password_service: PasswordService,
       token_service: TokenService
     ) -> LoginUserUseCase

각 의존성 함수는 필요한 의존성을 주입받아 인스턴스를 생성해야 해."
```

```
💬 API에서 의존성 사용 예시:

"API 엔드포인트에서는 다음과 같이 의존성을 주입받아:

from app.core.dependencies import get_register_user_use_case

@router.post('/register')
async def register(
    request: RegisterRequest,
    use_case: RegisterUserUseCase = Depends(get_register_user_use_case)
):
    user = await use_case.execute(...)
    return UserResponse.from_entity(user)

이렇게 사용하도록 API 엔드포인트를 수정해줘."
```

### 3.10 마이그레이션 생성

```
💬 프롬프트 예시:

"User 모델에 대한 Alembic 마이그레이션을 생성해줘.
다음 명령어를 실행해야 해:

cd bzero-api
uv run alembic revision --autogenerate -m 'Add User model'

그리고 생성된 마이그레이션 파일을 확인하고,
문제가 없으면 적용해줘:

uv run alembic upgrade head"
```

**Claude가 명령어를 실행**하고 결과를 보여줍니다. 에러가 있으면 함께 해결합니다.

### 3.11 테스트 작성

Clean Architecture에서는 각 계층별로 테스트를 작성합니다.

```
💬 프롬프트 예시:

"인증 기능에 대한 테스트를 작성해줘.

1. 도메인 계층 테스트 (tests/domain/test_user_entity.py):
   - User 엔티티의 비즈니스 로직 테스트
   - 포인트 차감/추가 테스트
   - 검증 규칙 테스트

2. 애플리케이션 계층 테스트 (tests/application/test_register_user_use_case.py):
   - RegisterUserUseCase 단위 테스트
   - Mock 리포지토리 사용
   - 정상 케이스, 중복 케이스 테스트

3. 인프라 계층 테스트 (tests/infrastructure/test_user_repository_impl.py):
   - 리포지토리 구현체 통합 테스트
   - 테스트 DB 사용
   - CRUD 작업 테스트

4. API 계층 테스트 (tests/presentation/test_auth_api.py):
   - API 엔드포인트 통합 테스트
   - FastAPI TestClient 사용
   - HTTP 요청/응답 테스트

pytest와 pytest-asyncio를 사용해줘."
```

### 3.12 개발 서버 실행 및 테스트

```
💬 프롬프트 예시:

"백엔드 개발 서버를 실행해줘:

cd bzero-api
uv run dev

그리고 Swagger UI(http://0.0.0.0:8000/docs)로
회원가입 API를 테스트해볼게. 결과를 확인해줘."
```

---

## 4. 프론트엔드 작업 (bzero-web - Feature-based)

백엔드 API가 완성되면 프론트엔드 작업을 시작합니다. Feature-based 구조로 기능별로 모듈화하여 개발합니다.

### 4.1 Feature 디렉토리 구조 확인/생성

각 기능별로 독립적인 디렉토리 구조를 만듭니다.

```
💬 프롬프트 예시:

"온보딩 & 회원가입 기능을 위한 Feature 구조를 생성해줘.

bzero-web/src/features/auth/ 디렉토리 아래:
├── api/              # API 클라이언트
│   └── authApi.ts
├── components/       # 컴포넌트
│   ├── LoginForm.tsx
│   ├── RegisterForm.tsx
│   └── OnboardingSlides.tsx
├── hooks/            # 커스텀 훅
│   ├── useLogin.ts
│   ├── useRegister.ts
│   └── useAuth.ts
├── store/            # 상태 관리
│   └── authStore.ts
├── types/            # 타입 정의
│   └── auth.types.ts
└── index.ts          # Public API (exports)

누락된 디렉토리가 있으면 생성해줘."
```

### 4.2 타입 정의 작성

먼저 기능에 필요한 타입을 정의합니다.

```
💬 프롬프트 예시:

"bzero-web/src/features/auth/types/auth.types.ts 에
인증 관련 타입을 정의해줘.

타입:

1. User
   - id: string
   - email: string
   - nickname: string
   - emoji: string
   - points: number
   - createdAt: string

2. RegisterRequest
   - email: string
   - password: string
   - nickname: string
   - emoji: string

3. LoginRequest
   - email: string
   - password: string

4. AuthResponse
   - access_token: string
   - token_type: string

5. AuthError (타입 가드 포함)
   - message: string
   - code?: string

TypeScript를 사용하고, export로 내보내야 해."
```

### 4.3 API 클라이언트 작성

Feature 내부에 API 클라이언트를 작성합니다.

```
💬 프롬프트 예시:

"bzero-web/src/features/auth/api/authApi.ts 에
인증 관련 API 클라이언트를 작성해줘.

필요한 함수:
- register(data: RegisterRequest): Promise<User>
- login(data: LoginRequest): Promise<AuthResponse>
- checkEmailAvailable(email: string): Promise<boolean>
- checkNicknameAvailable(nickname: string): Promise<boolean>

axios 또는 fetch를 사용하고:
- baseURL은 환경변수 VITE_API_URL 사용
- 에러 처리 포함
- 타입은 features/auth/types/auth.types.ts에서 import

공통 API 설정은 shared/api/client.ts에서 가져와야 해."
```

### 4.4 상태 관리 작성 (Zustand)

Feature 내부에 상태 관리를 작성합니다.

```
💬 프롬프트 예시:

"bzero-web/src/features/auth/store/authStore.ts 에
인증 상태 관리를 작성해줘.

Zustand를 사용하고:

State:
- user: User | null
- token: string | null
- isAuthenticated: boolean (computed)

Actions:
- setUser(user: User): void
- setToken(token: string): void
- logout(): void
- clearAuth(): void

Middleware:
- persist 미들웨어 사용 (localStorage에 저장)
- key: 'auth-storage'
- 저장 항목: user, token

타입은 features/auth/types/auth.types.ts에서 import해야 해."
```

### 4.5 커스텀 훅 작성

비즈니스 로직을 커스텀 훅으로 분리합니다.

```
💬 프롬프트 예시:

"bzero-web/src/features/auth/hooks/ 에 커스텀 훅을 작성해줘.

1. useRegister.ts
   - authApi.register를 사용
   - TanStack Query의 useMutation 활용
   - 성공 시: authStore.setUser, authStore.setToken 호출
   - 에러 처리 포함
   - 반환: { mutate, isLoading, error, isSuccess }

2. useLogin.ts
   - authApi.login를 사용
   - useMutation 활용
   - 성공 시: authStore.setToken 호출, 사용자 정보 조회
   - 반환: { mutate, isLoading, error, isSuccess }

3. useAuth.ts
   - authStore의 상태를 wrapping
   - 인증 관련 유틸리티 함수 제공
   - 반환: { user, isAuthenticated, logout }

4. useCheckEmail.ts
   - 이메일 중복 확인
   - debounce 적용 (500ms)
   - useQuery 사용

5. useCheckNickname.ts
   - 닉네임 중복 확인
   - debounce 적용 (500ms)
   - useQuery 사용

TanStack Query와 Zustand를 조합해서 사용해야 해."
```

### 4.6 컴포넌트 작성

Feature 내부에 UI 컴포넌트를 작성합니다.

```
💬 프롬프트 예시:

"bzero-web/src/features/auth/components/ 에 컴포넌트를 작성해줘.

1. RegisterForm.tsx
   - react-hook-form + zod 사용
   - useRegister 훅 사용
   - useCheckEmail, useCheckNickname으로 중복 확인
   - 필드: email, password, passwordConfirm, nickname, emoji
   - Shadcn UI의 Form, Input 컴포넌트 사용
   - 실시간 검증 피드백 표시

2. LoginForm.tsx
   - react-hook-form + zod 사용
   - useLogin 훅 사용
   - 필드: email, password
   - Shadcn UI 컴포넌트 사용
   - 에러 메시지 표시

3. OnboardingSlides.tsx
   - 3개의 스토리 화면 슬라이드
   - Swiper 또는 자체 구현
   - 애니메이션 효과
   - 다음/이전 버튼
   - 진행 상태 표시

4. EmojiPicker.tsx
   - 10개 이모지 선택 UI
   - 선택된 이모지 하이라이트
   - 그리드 레이아웃

모든 컴포넌트는:
- TypeScript 사용
- Tailwind CSS로 스타일링
- 반응형 디자인
- 접근성 고려 (aria-label 등)"
```

### 4.7 페이지 조합 및 라우팅

Feature 컴포넌트들을 조합하여 페이지를 만들고 라우팅을 설정합니다.

```
💬 프롬프트 예시:

"온보딩 페이지를 조합해줘.

1. bzero-web/src/app/pages/OnboardingPage.tsx 작성:
   - features/auth에서 컴포넌트 import
   - OnboardingSlides, LoginForm, RegisterForm 조합
   - 화면 전환 로직 구현
   - 레이아웃 구성

2. bzero-web/src/app/routes/index.tsx 에 라우팅 추가:
   - react-router-dom v6 사용
   - 라우트 정의:
     / → OnboardingPage
     /home → HomePage (B0 터미널)
     /city/:cityId → CityDetailPage
     /guesthouse → GuesthousePage
     /sarangbang → SarangbangPage
     /lounge → LoungePage
     /private → PrivateRoomPage

3. bzero-web/src/app/routes/ProtectedRoute.tsx 작성:
   - useAuth 훅으로 인증 확인
   - 미인증 시 / (온보딩)으로 리다이렉트
   - 인증된 경우 자식 컴포넌트 렌더링

로그인 필요 페이지는 ProtectedRoute로 감싸야 해."
```

### 4.8 Feature Public API 작성

Feature의 Public API를 정의하여 외부에서 사용할 수 있도록 합니다.

```
💬 프롬프트 예시:

"bzero-web/src/features/auth/index.ts 에
Feature의 Public API를 작성해줘.

Export:
- Components: LoginForm, RegisterForm, OnboardingSlides
- Hooks: useLogin, useRegister, useAuth
- Store: useAuthStore
- Types: User, RegisterRequest, LoginRequest, AuthResponse

내부 구현 세부사항은 export하지 않고,
외부에서 필요한 것만 export해야 해.

이렇게 하면 다른 곳에서:
import { LoginForm, useAuth } from '@/features/auth'
로 사용할 수 있어."
```

### 4.9 개발 서버 실행 및 테스트

```
💬 프롬프트 예시:

"프론트엔드 개발 서버를 실행해줘:

cd bzero-web
pnpm dev

http://localhost:5173 에서 Onboarding 페이지를 확인해볼게.
백엔드 API와 연동이 잘 되는지 테스트해줘."
```

---

## 5. Git 서브모듈 작업

B0는 모노레포이지만 서브모듈을 사용하므로, 각 하위 프로젝트를 독립적으로 커밋합니다.

### 5.1 백엔드 커밋 (bzero-api)

```
💬 프롬프트 예시:

"bzero-api에서 User 도메인과 인증 유스케이스를 커밋해줘.

cd bzero-api

git status로 변경사항 확인하고,
git diff로 변경 내용 확인 후,
적절한 커밋 메시지로 커밋해줘.

커밋 메시지는 다음 형식:
feat: add user authentication with clean architecture

그리고 푸시해줘."
```

**Claude가 자동으로** git status, diff를 확인하고, 커밋 메시지를 작성한 뒤 커밋합니다.

### 5.2 프론트엔드 커밋 (bzero-web)

```
💬 프롬프트 예시:

"bzero-web에서 auth feature를 커밋해줘.

cd bzero-web

변경사항 확인하고, 커밋 메시지는:
feat: add auth feature with onboarding and registration

푸시까지 해줘."
```

### 5.3 루트 저장소 업데이트

```
💬 프롬프트 예시:

"루트 저장소에서 서브모듈 변경사항을 반영해줘.

cd /Users/joyuiyeong/projects/b0

git status로 서브모듈 변경 확인하고,
git add bzero-api bzero-web
git commit -m 'feat: update submodules - add user authentication'
git push"
```

---

## 6. Claude Code 활용 팁

### 6.1 효율적인 프롬프팅

#### ✅ 좋은 예시

```
💬 명확하고 구체적인 요청:

"먼저 bzero-api/app/domain/entities/city.py 에 City 도메인 엔티티를 작성하고,
bzero-api/app/infrastructure/db/models/city_model.py 에 CityModel ORM 모델을 작성해줘.

필드: id(ULID), name(String), theme(String), description(Text),
      image_url(String), is_active(Boolean), created_at(DateTime).

도메인 엔티티는 순수 Python 클래스로,
ORM 모델은 SQLAlchemy 비동기 모델로 작성해줘.

@docs/01-mvp.md 의 세렌시아, 로렌시아 2개 도시 정보를 seed 데이터로 추가해줘."
```

#### ❌ 나쁜 예시

```
💬 모호하고 불명확한 요청:

"도시 모델 만들어줘"
→ 어떤 필드가 필요한지, 어떤 기술을 사용하는지 불명확
```

### 6.2 컨텍스트 참조 활용

Claude Code는 `@` 기호로 파일이나 문서를 컨텍스트에 포함할 수 있습니다.

```
💬 예시:

"@docs/01-mvp.md @docs/erd.md 를 참고해서
bzero-api/app/domain/entities/room.py 에 Room 도메인 엔티티와
bzero-api/app/infrastructure/db/models/room_model.py 에 RoomModel ORM 모델을 작성해줘."
```

이렇게 하면 Claude가 **해당 문서의 내용을 읽고** 정확히 작성합니다.

### 6.3 단계적 요청

복잡한 작업은 한 번에 요청하지 말고, **단계별로 나눠서** 요청합니다.

```
💬 Step 1:

"먼저 Room 도메인 엔티티의 필드와 관계를 설계해줘. 코드는 아직 작성하지 말고."

💬 Step 2 (검토 후):

"좋아. 이제 이 설계를 바탕으로:
1. bzero-api/app/domain/entities/room.py - Room 도메인 엔티티
2. bzero-api/app/domain/repositories/room_repository.py - RoomRepository 인터페이스
3. bzero-api/app/infrastructure/db/models/room_model.py - RoomModel ORM
4. bzero-api/app/infrastructure/repositories/room_repository_impl.py - 구현체
를 작성해줘."

💬 Step 3:

"Room 모델에 대한 마이그레이션을 생성하고 적용해줘."
```

### 6.4 에러 해결 패턴

에러가 발생하면 **에러 메시지를 그대로 공유**합니다.

```
💬 예시:

"개발 서버를 실행했는데 다음 에러가 발생했어:

[에러 메시지 복사 붙여넣기]

원인을 분석하고 해결해줘."
```

Claude가 **에러를 분석**하고, 해결 방법을 제시하고, 코드를 수정합니다.

### 6.5 코드 리뷰 요청

작성된 코드를 검토받을 수 있습니다.

```
💬 예시:

"@bzero-api/app/application/use_cases/register_user.py 와
@bzero-api/app/infrastructure/repositories/user_repository_impl.py 를 리뷰해줘.
다음 관점에서 확인해줘:
- 보안 취약점 (SQL Injection, 비밀번호 해싱 등)
- 비즈니스 로직 오류
- Clean Architecture 원칙 준수 여부
- 성능 문제
- 코드 스타일"
```

### 6.6 테스트 자동 생성

```
💬 예시:

"인증 기능에 대한 테스트를 작성해줘.

1. tests/domain/test_user_entity.py - User 엔티티 단위 테스트
2. tests/application/test_register_user_use_case.py - RegisterUserUseCase 단위 테스트
3. tests/infrastructure/test_user_repository_impl.py - 리포지토리 통합 테스트
4. tests/presentation/test_auth_api.py - API 엔드포인트 통합 테스트

모든 엔드포인트를 커버하고, 성공 케이스와 실패 케이스를 모두 테스트해야 해."
```

### 6.7 리팩토링

```
💬 예시:

"@bzero-api/app/application/use_cases/purchase_ticket.py 의 코드가 너무 길어.
다음과 같이 리팩토링해줘:
- 유스케이스를 더 작은 단계로 분리
- 중복 코드를 도메인 서비스로 추출
- 값 객체를 활용하여 타입 안정성 향상
기능은 그대로 유지해야 해."
```

---

## 7. 문제 해결

### 7.1 서브모듈 관련 이슈

#### 문제: 서브모듈이 업데이트되지 않음

```
💬 해결:

"git submodule update --init --recursive --remote 를 실행해줘.
그리고 각 서브모듈의 최신 커밋을 확인해줘."
```

#### 문제: 서브모듈이 detached HEAD 상태

```
💬 해결:

"cd bzero-api
git checkout main
git pull origin main

이렇게 각 서브모듈을 main 브랜치로 전환해줘."
```

### 7.2 백엔드 이슈

#### 문제: 마이그레이션 충돌

```
💬 해결:

"alembic heads 를 실행해서 여러 헤드가 있는지 확인하고,
있다면 alembic merge 로 병합해줘."
```

#### 문제: 비동기 세션 에러

```
💬 해결:

"AsyncSession을 제대로 사용하고 있는지 확인해줘.
모든 DB 쿼리 앞에 await가 있어야 하고,
컨텍스트 매니저로 감싸야 해."
```

### 7.3 프론트엔드 이슈

#### 문제: CORS 에러

```
💬 해결:

"백엔드(bzero-api)의 CORS 설정을 확인해줘.
app/core/config.py 와 app/main.py 에서 CORSMiddleware가
http://localhost:5173 을 허용하도록 설정되어 있어야 해."
```

#### 문제: API 호출 실패

```
💬 해결:

"브라우저 개발자 도구의 Network 탭을 확인해볼게.
[Network 탭 스크린샷 또는 에러 메시지]

이 에러를 분석하고 해결해줘."
```

### 7.4 일반적인 디버깅 패턴

```
💬 프롬프트 템플릿:

"[기능명]이 작동하지 않아.
증상: [상세 설명]
기대 동작: [예상 결과]
실제 동작: [실제 결과]
에러 메시지: [에러 메시지]

관련 파일:
@[파일1]
@[파일2]

원인을 찾고 해결해줘."
```

---

## 8. 참고 자료

- **프로젝트 문서**: `docs/00-concept.md`, `docs/01-mvp.md`
- **백엔드 가이드**: `bzero-api/CLAUDE.md`
- **프론트엔드 가이드**: `bzero-web/CLAUDE.md`
- **Claude Code 공식 문서**: https://docs.claude.com/en/docs/claude-code

---

이 워크플로우를 따라 **10개 기능을 순서대로 개발**하면, MVP 완성까지 체계적으로 진행할 수 있습니다! 🚀
