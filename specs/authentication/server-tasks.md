# 로그인 서버 작업

[요구사항](requirements.md)·[설계](design.md)·[계약](../../contracts/auth-api.md)을 따른다.

- challenge 발급·일회성 소비, Apple 서명·claim 검증, 코드 교환 및 client_secret 서명.
- 암호화 refresh token, 30일 세션·로그아웃·탈퇴와 정기 정리.
- 공개 API 유지, 개인 API 인증, 공통 오류·Swagger Bearer 설정.
- 유효/무효 서명·issuer·audience·만료·nonce, 반복/동시 로그인, 세션 만료·로그아웃·격리·탈퇴를 검증.
- 외부 장애·폐기 실패 재시도, DB에 비밀 원문 미저장 검증.

진행 상태: [서버 이슈 #18](https://github.com/kdywjs/weatherhood-server/issues/18), [스펙 이슈 #9](https://github.com/kdywjs/weatherhood-specs/issues/9), [Project](https://github.com/orgs/kdywjs/projects/1).
