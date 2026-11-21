# 지역 선택 서버 작업

[요구사항](requirements.md), [설계](design.md), [API 계약](../../contracts/region-api.md)을 따른다.

- 기존 행정동 DB 검색·ID 조회와 카카오 H 코드 매핑.
- 사용자별 저장·삭제·순서·기본 지역 API 및 트랜잭션.
- 검색 내부 공백·특수문자·페이지, 좌표 범위·매핑 부재·외부 실패 검증.
- 중복과 동시 추가·최대 개수, 사용자 격리, 순서 충돌, 기본 지역 삭제, 탈퇴 후 개인 데이터 삭제 검증.

[서버 이슈 #18](https://github.com/kdywjs/weatherhood-server/issues/18), [Project](https://github.com/orgs/kdywjs/projects/1). 앱 연결은 [앱 작업](app-tasks.md)을 따른다.
