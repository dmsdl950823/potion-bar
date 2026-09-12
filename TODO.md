# Potion TODO

> 기준 문서: `AGENT.md`, `docs/database/1-base-prompt.md`
> 우선순위: **P0** MVP 필수 · **P1** 출시 전 권장 · **P2** 출시 후 확장

분야별 할 일은 아래 문서에서 관리한다.

- [기획 TODO](todo/01-PLANNING.md) — 제품 범위, 사용자, 콘텐츠 및 정책
- [마케팅·수익화 TODO](todo/02-GROWTH.md) — 유입, 광고, 제휴 및 수익화
- [Database TODO](todo/03-DATABASE.md) — ERD, DDL, 데이터 수집·정제, 마이그레이션
- [Backend TODO](todo/BE.md) — 서버 구조, 검색·추천 API, 보안 및 운영
- [Frontend TODO](todo/FE.md) — 화면, 사용자 경험, 접근성 및 E2E

## 전체 권장 실행 순서

1. 기획에서 MVP 범위와 데이터 정책을 확정한다.
2. Database에서 ERD, DDL, 정규화 규칙과 최소 데이터셋을 만든다.
3. Backend에서 검색·추천 API를 구현한다.
4. Frontend에서 핵심 사용자 여정과 화면을 구현한다.
5. 분야별 테스트 후 Supabase로 이전하고 베타를 출시한다.

## MVP 통합 완료 기준

- [ ] 신규 환경에서 문서화된 명령으로 DB와 앱을 실행할 수 있다.
- [ ] seed를 반복 실행해도 데이터가 중복되지 않는다.
- [ ] 사용자가 재료를 선택하면 제조 가능한 칵테일 목록을 볼 수 있다.
- [ ] 상세 화면에서 한국어 재료·용량·제조 단계·안주 추천을 볼 수 있다.
- [ ] 주종·도수·상황 필터가 정상 동작한다.
- [ ] 핵심 사용자 여정의 자동 테스트가 통과한다.
- [ ] 검증된 데이터를 Supabase로 이전하고 무결성을 확인할 수 있다.
