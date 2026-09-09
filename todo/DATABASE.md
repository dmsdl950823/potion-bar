# Database TODO

[← 전체 TODO](../TODO.md)

## 스키마 및 환경

- [ ] **P0** Local DB를 Docker PostgreSQL, SQLite, Supabase Local 중 선택
- [ ] **P0** Local First → Supabase 전환 구조 설계
- [ ] **P0** ERD와 PostgreSQL DDL 작성
  - `cocktails`, `ingredients`, `cocktail_ingredients`
  - `foods`, `cocktail_food_pairings`
  - `categories`, `tags`, `cocktail_tags`
- [ ] **P0** PK, FK, UNIQUE, CHECK, NOT NULL 및 삭제 정책 정의
- [ ] **P0** 검색·조인·Upsert용 인덱스 설계
- [ ] **P0** 출처 필드 설계 (`source`, `external_id`, `source_url`, `fetched_at`)
- [ ] **P0** raw 테이블 또는 원본 JSON 보존 정책 결정
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
- [ ] **P0** 자동 번역과 수동 검수 상태 관리
- [ ] **P0** 맛·도수·난이도·상황·가성비 태그 적재
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
- [ ] Supabase 이전과 rollback을 검증했다.
