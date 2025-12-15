# B0 데이터베이스 스키마

## 개요

B0 프로젝트의 데이터베이스 스키마 설계 문서입니다.

---

## 일기 및 문답지 관련 테이블

### questions (질문 테이블)

도시별 문답지 질문을 저장하는 테이블입니다.

| 필드 | 타입 | 제약조건 | 설명 |
|------|------|---------|------|
| id | UUID (v7) | PRIMARY KEY | 질문 ID |
| city_id | UUID (v7) | FOREIGN KEY, NOT NULL | 도시 ID (cities 테이블 참조) |
| question_1 | TEXT | NOT NULL | 첫 번째 질문 |
| question_2 | TEXT | NOT NULL | 두 번째 질문 |
| question_3 | TEXT | NOT NULL | 세 번째 질문 |
| created_at | TIMESTAMP | NOT NULL, DEFAULT NOW() | 생성 시간 |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT NOW() | 수정 시간 |

**제약사항:**
- city_id는 cities 테이블의 id를 참조
- 각 도시는 1개의 질문 세트를 가짐 (UNIQUE INDEX on city_id)
- 질문은 최대 200자

**예시 데이터:**

**세렌시아 (관계의 도시):**
- question_1: "요즘 나에게 힘이 되어주는 사람은?"
- question_2: "최근에 누군가와 나눈 의미 있는 대화는?"
- question_3: "관계에서 내가 가장 중요하게 생각하는 것은?"

**로렌시아 (회복의 도시):**
- question_1: "요즘 나를 지치게 하는 것은?"
- question_2: "나만의 휴식 방법이 있다면?"
- question_3: "완전히 쉬었다고 느꼈던 때는 언제인가요?"

---

### answers (답변 테이블)

사용자가 작성한 문답지 답변을 저장하는 테이블입니다.

| 필드 | 타입 | 제약조건 | 설명 |
|------|------|---------|------|
| id | UUID (v7) | PRIMARY KEY | 답변 ID |
| question_id | UUID (v7) | FOREIGN KEY, NOT NULL | 질문 ID (questions 테이블 참조) |
| user_id | UUID (v7) | FOREIGN KEY, NOT NULL | 사용자 ID (users 테이블 참조) |
| city_id | UUID (v7) | FOREIGN KEY, NOT NULL | 도시 ID (cities 테이블 참조) |
| question_1_answer | TEXT | NOT NULL | 첫 번째 질문에 대한 답변 |
| question_2_answer | TEXT | NOT NULL | 두 번째 질문에 대한 답변 |
| question_3_answer | TEXT | NOT NULL | 세 번째 질문에 대한 답변 |
| has_earned_points | BOOLEAN | NOT NULL, DEFAULT FALSE | 포인트 지급 여부 |
| created_at | TIMESTAMP | NOT NULL, DEFAULT NOW() | 생성 시간 |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT NOW() | 수정 시간 |

**제약사항:**
- question_id는 questions 테이블의 id를 참조
- user_id는 users 테이블의 id를 참조
- city_id는 cities 테이블의 id를 참조
- 한 사용자는 한 도시에 대해 1개의 답변만 작성 가능 (UNIQUE INDEX on user_id, city_id)
- 각 답변은 최대 200자
- has_earned_points는 포인트 중복 지급 방지용

**비즈니스 로직:**
- 문답지 최초 작성 시 has_earned_points = FALSE → TRUE로 변경하고 50포인트 지급
- 문답지 수정 시 has_earned_points가 이미 TRUE이면 포인트 지급하지 않음
- room_stay에 체크인한 사용자만 작성 가능

---

### diaries (일기 테이블)

사용자가 작성한 일기를 저장하는 테이블입니다.

| 필드 | 타입 | 제약조건 | 설명 |
|------|------|---------|------|
| id | UUID (v7) | PRIMARY KEY | 일기 ID |
| user_id | UUID (v7) | FOREIGN KEY, NOT NULL | 사용자 ID (users 테이블 참조) |
| city_id | UUID (v7) | FOREIGN KEY, NOT NULL | 도시 ID (cities 테이블 참조) |
| room_stay_id | UUID (v7) | FOREIGN KEY, NOT NULL | room_stay ID (room_stays 테이블 참조) |
| title | TEXT | NULLABLE | 일기 제목 (선택사항) |
| content | TEXT | NOT NULL | 일기 본문 |
| mood | VARCHAR(20) | NOT NULL | 기분 (이모지 또는 코드) |
| has_earned_points | BOOLEAN | NOT NULL, DEFAULT FALSE | 포인트 지급 여부 |
| created_at | TIMESTAMP | NOT NULL, DEFAULT NOW() | 생성 시간 |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT NOW() | 수정 시간 |

**제약사항:**
- user_id는 users 테이블의 id를 참조
- city_id는 cities 테이블의 id를 참조
- room_stay_id는 room_stays 테이블의 id를 참조
- 한 room_stay당 1개의 일기만 작성 가능 (UNIQUE INDEX on room_stay_id)
- content는 최대 500자
- mood는 이모지 또는 코드 형태로 저장 (예: "happy", "sad", "angry", "love", "neutral")
- has_earned_points는 포인트 중복 지급 방지용

**비즈니스 로직:**
- 일기 최초 작성 시 has_earned_points = FALSE → TRUE로 변경하고 50포인트 지급
- 일기 수정 시 has_earned_points가 이미 TRUE이면 포인트 지급하지 않음
- room_stay에 체크인한 사용자만 작성 가능
- 체크인 후 24시간 이내에만 작성/수정 가능
- 24시간 지나면 (연장한 경우) 새 일기 작성 가능

---

## 인덱스

### questions 테이블
```sql
CREATE UNIQUE INDEX idx_questions_city_id ON questions(city_id);
```

### answers 테이블
```sql
CREATE UNIQUE INDEX idx_answers_user_city ON answers(user_id, city_id);
CREATE INDEX idx_answers_user_id ON answers(user_id);
CREATE INDEX idx_answers_city_id ON answers(city_id);
CREATE INDEX idx_answers_question_id ON answers(question_id);
```

### diaries 테이블
```sql
CREATE UNIQUE INDEX idx_diaries_room_stay_id ON diaries(room_stay_id);
CREATE INDEX idx_diaries_user_id ON diaries(user_id);
CREATE INDEX idx_diaries_city_id ON diaries(city_id);
CREATE INDEX idx_diaries_created_at ON diaries(created_at DESC);
```

---

## API 엔드포인트 (예시)

### 질문 조회
```
GET /api/v1/cities/{city_id}/questions
```

### 문답지 작성
```
POST /api/v1/cities/{city_id}/answers
{
  "question_1_answer": "...",
  "question_2_answer": "...",
  "question_3_answer": "..."
}
```

### 문답지 수정
```
PUT /api/v1/cities/{city_id}/answers/{answer_id}
{
  "question_1_answer": "...",
  "question_2_answer": "...",
  "question_3_answer": "..."
}
```

### 문답지 조회 (사용자별)
```
GET /api/v1/users/me/answers
```

### 문답지 상세 조회
```
GET /api/v1/cities/{city_id}/answers/{answer_id}
```

### 일기 작성
```
POST /api/v1/diaries
{
  "city_id": "...",
  "room_stay_id": "...",
  "title": "...",
  "content": "...",
  "mood": "happy"
}
```

### 일기 수정
```
PUT /api/v1/diaries/{diary_id}
{
  "title": "...",
  "content": "...",
  "mood": "sad"
}
```

### 일기 조회 (사용자별)
```
GET /api/v1/users/me/diaries
```

### 일기 상세 조회
```
GET /api/v1/diaries/{diary_id}
```

---

## 마이그레이션 순서

1. cities 테이블 생성 (선행 조건)
2. users 테이블 생성 (선행 조건)
3. room_stays 테이블 생성 (선행 조건)
4. questions 테이블 생성
5. answers 테이블 생성
6. diaries 테이블 생성
7. 세렌시아, 로렌시아 질문 데이터 시딩
