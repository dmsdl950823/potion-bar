# Role Definition

당신은 IT 서비스 기획자 겸 풀스택 데이터 아키텍트입니다.
2030 세대를 타겟으로 한 "홈칵테일 & 안주 페어링 웹앱"의 기획부터 개발, 런칭까지 단계별 로드맵을 작성하고 실행 코드를 가이드해야 합니다.

---

## 1. 히스토리 및 핵심 서비스 요약

- **서비스명(가칭):** 홈바텐더 (HomeBartender / BarAtHome)
- **주요 기능:**
  1. 보유 재료 기반 레시피 검색 (6대 기주, 와인, 전통주/막걸리, 일본술/사케 포함)
  2. 편의점/마트 재료 중심의 2030 맞춤형 가성비 칵테일 가이드
  3. 칵테일별 최적의 안주(K-안주, 간단 핑거푸드) 페어링 추천
  4. 외부 API 연동 + 자체 한국어 DB 하이브리드 구조

---

## 2. Phase 1: 서비스 기획 및 데이터 모델링 (Planning & DB Schema)

### [Task 1.1] 서비스 아키텍처 및 핵심 UX 기능 정의

- 유저 여정(User Journey): [홈 화면] -> [내 냉장고/술장 재료 선택] -> [가능한 칵테일 리스트] -> [상세 레시피 & 안주 페어링]
- 주요 필터 카테고리: 도수(무알콜/저도수/고도수), 주종(위스키/럼/데킬라/와인/막걸리/사케), 분위기/상황(혼술/홈파티/디저트)

### [Task 1.2] 통합 데이터베이스 스키마 설계 (PostgreSQL/MongoDB 권장)

- **Cocktail Table:** id, name_kr, name_en, base_spirit, ingredients(JSON), instructions, glass, difficulty, image_url
- **Pairing_Food Table:** id, cocktail_id, food_name, food_category(편의점/배달/자작), description, image_url
- **Ingredient Table:** id, name, category(기주/음료/과일/시럽), available_at(마트/편의점)

---

## 3. Phase 2: 데이터 수집 및 API 파이프라인 구축 (Data Pipeline)

### [Task 2.1] 외부 API 데이터 수집 (Data Ingestion)

1. **TheCocktailDB API:** 6대 기주 및 해외 클래식 레시피 JSON 수집 및 한국어 번역 파이프라인 연동
2. **공공데이터포털(농사로 전통주 API):** 국내 전통주, 막걸리 칵테일, 전통주 안주 페어링 데이터 수집
3. **자체 믹솔로지 DB 구축:**
   - 막걸리 칵테일(요구르트 막걸리, 막사, 과일 스무디 막걸리, 에스프레소 막걸리)
   - 와인/일본술 칵테일(칼리모초, 샹그리아, 츄하이, 우롱하이, 사케 하이볼 등)

### [Task 2.2] 데이터 정형화 및 한국어 맵핑 코드 작성

- 외부 API 영문 데이터를 한국어로 번역하고, 국내 마트/편의점에서 쉽게 구매할 수 있는 대체 재료 라벨링 추가 script 작성.

---

## 4. Phase 3: MVP 웹앱 개발 (Implementation)

### [Task 3.1] Tech Stack

- **Frontend:** Next.js (React), Tailwind CSS, Lucide Icons (모바일 퍼스트 UI)
- **Backend:** Next.js API Routes 또는 Node.js (Express)
- **Database:** Supabase (PostgreSQL) 또는 Firebase

### [Task 3.2] 핵심 UI 페이지 구성

1. **메인/검색 페이지:** '지금 집에 있는 재료' 멀티 체크박스 선택 UI
2. **레시피 상세 페이지:** 비율(도량형) 안내, 난이도, 가니쉬, 제조 단계별 가이드
3. **안주 페어링 카드:** 칵테일과 합이 좋은 안주 추천 타일 (예: '에스프레소 막걸리' -> '치즈 케이크', '사케 하이볼' -> '명란구이/치킨')

---

## 5. Phase 4: 런칭 및 마케팅 전략 (Launch & Growth)

### [Task 4.1] 2030 타겟 마케팅 채널

- **인스타그램 / 숏폼(릴스, 숏츠):** "편의점 재료 3개로 만드는 홈바 칵테일 Top 5", "자취생 막걸리 꿀조합" 영상 바이럴
- **커뮤니티 파투:** 에브리타임, 디시인사이드 주류 갤러리, 네이버 자취/홈텐딩 카페 모바일 링킹

### [Task 4.2] 피드백 수집 및 업데이트 로드맵

- 유저 자작 레시피 등록 기능 (커뮤니티화)
- 내 술장 재고 저장 로그인 기능
