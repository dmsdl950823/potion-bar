# Potion Database TODO

[← 전체 TODO](../TODO.md) · [← 제품 기획](01-PLANNING.md)

> 문서 상태: 데이터베이스 요구사항 확정, 구현 전
>
> 기본 원칙: **Supabase Local에서 시작하고 같은 migration을 Supabase Cloud에 적용한다. 원문과 가공 데이터를 분리하며 모든 레코드의 출처와 검수 상태를 추적한다.**

## 확정된 설계 결정

1. 로컬 환경은 **Supabase Local(PostgreSQL)** 로 통일한다. Supabase CLI가 관리하는 Docker 환경을 사용하며 SQLite를 중간 저장소로 사용하지 않는다.
2. `카테고리 → 표준 재료 → 유통 제품`을 분리한다. 예를 들어 `위스키 → 버번 위스키 → 특정 브랜드·제품`처럼 탐색할 수 있어야 한다.
3. 술 자체의 맛과 완성된 칵테일의 맛은 별도 프로필로 저장한다. 원재료 평가가 레시피 결과에 덮어쓰이지 않게 한다.
4. 모든 주요 행의 PK는 UUID로 발급한다. 외부 API ID는 PK가 아니라 `(source_id, entity_type, external_id)` UNIQUE 자연키로 관리해 출처 간 ID 충돌을 막는다.
5. 영문 원문은 그대로 보존하고 한국어 번역을 별도 저장한다. 자동 번역 결과는 사용 모델·프롬프트 버전·생성 시각·검수 상태를 기록한다.
6. API와 공식 다운로드를 우선한다. 크롤링은 명시적으로 허용된 공개 페이지만 대상으로 하며 robots.txt, 이용약관, 호출 간격과 저작권 조건을 지킨다. 인증·CAPTCHA·접근 제한이나 차단을 IP 우회로 회피하지 않는다.
7. 향후 `술 추가 요청하기`를 받을 수 있도록 요청 원문, 제안 제품, 처리 상태, 중복 병합과 관리자 검수 이력을 담는 확장 지점을 둔다.

## 스키마 및 환경

- [x] **P0** Local DB를 Supabase Local로 확정
- [ ] **P0** Local First → Supabase 전환 구조 설계
- [ ] **P0** ERD와 PostgreSQL DDL 작성
  - `categories`: 주종 계층. `parent_id`로 대분류·소분류 표현
  - `ingredients`: 레시피가 참조하는 표준 술·음료·시럽·가니시
  - `ingredient_aliases`: 한국어·영문·출처별 재료명과 검색 별칭
  - `products`: 실제 유통 술 이름, 브랜드, 용량, ABV, 원산지와 표준 재료 연결
  - `product_taste_profiles`, `cocktail_taste_profiles`: 개별 술 제품과 완성 칵테일의 맛을 분리
  - `cocktails`, `cocktail_ingredients`: 레시피와 재료 N:M, 용량·단위·대체 가능 여부
  - `ingredient_substitutions`: 국내 대체 재료와 대체 품질·설명
  - `foods`, `cocktail_food_pairings`
  - `tags`, `cocktail_tags`
  - `sources`, `external_refs`, `raw_records`: 출처 원장, 외부 자연키, 원문 보존
  - `translations`, `content_reviews`: 번역·가공·수동 검수 이력
  - `catalog_requests`: P2 술 추가 요청과 처리 상태
- [ ] **P0** PK, FK, UNIQUE, CHECK, NOT NULL 및 삭제 정책 정의
  - 내부 PK는 `uuid DEFAULT gen_random_uuid()` 사용
  - 유통 제품마다 서로 다른 `products.id` 발급
  - 외부 레코드는 `(source_id, entity_type, external_id)` UNIQUE 적용
  - 참조 중인 카테고리·재료·제품은 물리 삭제보다 `status` 또는 `archived_at` 사용
- [ ] **P0** 검색·조인·Upsert용 인덱스 설계
- [ ] **P0** 출처 원장과 참조 필드 설계 (`sources`, `external_refs`, `source_url`, `fetched_at`)
- [x] **P0** 원본 보존 정책 확정: `raw_records.payload jsonb`에 수정하지 않은 응답 저장
- [ ] **P0** migration과 seed 작성
- [ ] **P1** 감사·검수 필드 및 사용자·술장·자작 레시피 확장성 검토

## Open API 및 데이터 수집원

- [ ] **P0** [TheCocktailDB](https://www.thecocktaildb.com/) 수집기 구현
  - [공식 API 문서](https://www.thecocktaildb.com/api.php)
  - Base: `https://www.thecocktaildb.com/api/json/v1/1/`
  - 검색: `search.php?s=margarita`
  - 재료 필터: `filter.php?i=Gin`
  - 상세: `lookup.php?i={drink_id}`
  - 무료·유료 범위, 호출 제한, 이미지 이용 조건 확인
- [ ] **P1** API Ninjas Cocktails API 키·요금·중복 범위 검토 후 수집기 구현
- [ ] **P0** 공공데이터포털 국립농업과학원 전통주 API 명세 확인·접근 신청·수집기 구현
- [ ] **P1** [더술닷컴](https://thesool.com/)의 공개 API 또는 제휴·다운로드 제공 여부 확인
  - 크롤링 전 `robots.txt`, 이용약관, 저작권, 이미지 재사용 범위 검토
- [ ] **P1** [Cocktail Maker API 문서](https://dongju93.github.io/cocktail-maker/)와 구현 저장소 검토
  - localhost 기반 자체 호스팅형 API이므로 데이터 원본과 라이선스 확인
  - 맛, 향, 피니시, 도수, 원산지 필드 모델 참고
- [ ] **P0** 일본술·커스텀 꿀조합 로컬 JSON 스키마와 seed 작성

### 번역·페이지 수집 원칙

- 수집기는 API 응답이나 허용된 페이지를 `raw_records`에 먼저 저장한다. AI가 웹페이지를 직접 탐색하며 건별로 복사하지 않는다.
- 번역 배치는 아직 번역되지 않은 고유 원문만 처리하고 동일 원문 해시의 결과를 재사용해 토큰 비용을 줄인다.
- 한국어 서비스 필드는 자동 번역할 수 있지만 `translation_status = machine_translated`로 시작하고, 출시 대상 콘텐츠는 표본 또는 전수 검수 후 `reviewed`로 전환한다.
- 레시피명, 재료명, 제조 명령, 주의사항은 각각 분리해 번역한다. 수량·단위·제품명은 번역 모델이 임의로 바꾸지 못하게 구조화 필드로 전달한다.
- 번역 실패나 낮은 신뢰도는 원문을 덮어쓰지 않고 검수 큐로 보낸다.
- 크롤러는 고정 User-Agent에 서비스와 연락처를 식별하고, 보수적 호출 간격·동시성 제한·캐시·체크포인트를 적용한다.
- robots.txt 또는 이용약관에서 자동 수집을 금지하거나 로그인·CAPTCHA·접근 차단이 나타나면 해당 소스 수집을 중단하고 API, 데이터 다운로드 또는 제휴를 검토한다.

## 참고 데이터셋 후보

> [주상낙원 사용 데이터셋](https://github.com/YEJIN012/Jusangnakwon#사용한-데이터-셋) 참고. API가 아닌 출처는 라이선스와 재배포 조건을 먼저 확인한다.

- [ ] **P1** 와인: [WINE21](https://www.wine21.com/13_search/wine_list.html) 크롤링 허용 여부 확인
- [ ] **P1** 위스키: [Scotch Whisky Dataset](https://www.kaggle.com/datasets/koki25ando/scotch-whisky-dataset) 라이선스 확인
- [ ] **P1** 전통주: [traditional-liquor-RS CSV](https://github.com/learning-human/traditional-liquor-RS/blob/main/traditional_liquor_df_final.csv) 라이선스 확인
- [ ] **P1** 칵테일: [Cocktail Wheel Dataset](https://dachang.github.io/CocktailWheel/) 파일·스키마·라이선스 확인
- [ ] **P2** 맥주: [Bear-Recommendation](https://github.com/quipu1/Bear-Recommendation) 데이터 위치·라이선스 확인
- [ ] **P1** 커스텀 칵테일: [KADX](https://kadx.co.kr/) 만개의레시피 데이터 승인·이용 조건 확인
- [ ] **P0** 출처 원장 작성: URL, 수집일, 버전, 라이선스, 상업 이용, 재배포, 이미지 권한

## ETL 및 데이터 품질

- [ ] **P0** Extract → Raw → Normalize → Enrich → Upsert 단계 정의
- [ ] **P0** 페이지네이션, rate limit, timeout, 재시도·지수 백오프 구현
- [ ] **P0** 부분 실패 격리, 체크포인트, 수집 이력 구현
- [ ] **P0** 외부 JSON과 내부 스키마 필드 매핑 작성
- [ ] **P0** 중복 판별과 멱등 Upsert 구현
- [ ] **P0** 재료명·별칭·국내 대체 재료·구매처 정규화
- [ ] **P0** 용량·단위 표준화 및 원문 보존
- [ ] **P0** 자동 번역 배치, 번역 캐시와 수동 검수 상태 관리
- [ ] **P0** 맛·도수·난이도·상황·가성비 태그 적재
- [ ] **P0** 술 자체 맛과 완성 칵테일 맛 프로필을 분리해 적재
- [ ] **P0** 칵테일-안주 페어링 데이터 적재
- [ ] **P1** 수집·변환 통계, 오류 리포트, 정기 동기화 정책 작성

## 테스트 및 프로덕션 이전

- [ ] **P0** migration, seed, 파서, 정규화, Upsert 테스트
- [ ] **P0** 대표 데이터의 재료·용량·번역·페어링 표본 검수
- [ ] **P0** Supabase 프로젝트와 환경별 DB 권한 구성
- [ ] **P0** schema migration, dump, restore, 검증 절차 작성
- [ ] **P0** 백업·rollback 절차와 row count·FK·샘플·이미지 URL 검증
- [ ] **P1** DB 모니터링과 백업·복구 정책 설정

## 완료 기준

- [ ] 빈 환경에서 migration과 seed로 DB를 재현할 수 있다.
- [ ] seed를 반복 실행해도 중복이 발생하지 않는다.
- [ ] 모든 데이터의 출처와 이용 조건을 추적할 수 있다.
- [ ] 원문, 자동 번역, 수동 수정 이력을 역추적할 수 있다.
- [ ] 술 자체의 맛과 제조 후 칵테일 맛이 독립적으로 조회된다.
- [ ] Supabase 이전과 rollback을 검증했다.
