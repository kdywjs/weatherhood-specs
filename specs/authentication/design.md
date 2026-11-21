# 로그인 설계

[요구사항](requirements.md)과 [인증 API 계약](../../contracts/auth-api.md)을 따른다.

## 로그인 흐름
1. 앱이 challenge를 발급받는다. 서버는 무작위 256비트 nonce의 SHA-256만 DB에 저장하며 유효기간은 5분이다.
2. 앱은 응답의 nonce를 변형 없이 Apple 요청에 전달한다. 앱이 별도로 해시하지 않는다.
3. 앱은 challengeId, identityToken, authorizationCode를 서버로 보낸다.
4. 서버는 Apple 공개키로 RS256 서명, iss, aud, exp, iat, sub, nonce를 검증한다. Apple 코드 교환 결과의 토큰도 검증하고 sub·nonce 일치를 확인한다.
5. DB에서 challenge를 원자적으로 소비하고 Apple sub로 사용자를 생성/조회한다. 30일 유효한 256비트 무작위 Bearer 토큰을 발급한다.

서버 토큰은 JWT가 아닌 불투명 세션 토큰이다. DB에는 SHA-256 해시만 저장하여 즉시 로그아웃·삭제할 수 있다. 앱에는 최초 발급 때만 원문을 반환한다. 세션·challenge 응답은 Cache-Control: no-store다. 앱은 세션 토큰을 Keychain에 보관한다.

## Apple 연동과 비밀값
앱 ID는 co.kr.ansandy.weatherhood, Team ID는 T9WYJM529D, Key ID는 D56B4XU824다. .p8은 Git 외부 파일로 공급한다. ES256으로 5분짜리 client_secret을 서명한다. native iOS 코드 교환에는 redirect_uri를 보내지 않는다.
Apple refresh token은 별도 256비트 AES-GCM 키로 암호화해 보관한다. 암호화 키와 .p8을 DB·Git·로그에 넣지 않는다. 계정별 최신 refresh token을 유지하고 마지막 확인에서 24시간이 지난 인증 요청에서 검증한다. 사용자 행 잠금으로 동시 검증·수정을 직렬화한다. 공급처 실패는 세션을 연장하거나 검증을 건너뛰지 않는다.

## DB와 동시성
- user_account: UUID, Apple sub(unique), 암호화한 refresh token, Apple 확인 시각, 기본 regionId(null=현재 위치).
- user_session: 토큰 해시 PK, 사용자 FK, 절대 만료 시각. 사용자 삭제 시 cascade.
- login_challenge: UUID, nonce hash, 만료 시각. 조건부 delete 성공 건수로 1회 사용을 보장한다.
- 저장 지역 변경·재로그인·탈퇴는 사용자 행을 잠가 경쟁을 제어한다. 탈퇴와 겹친 변경은 401이다.
- 만료된 challenge와 세션은 매시간 정리한다. DB 정리 전에도 만료 검증은 항상 수행한다.
- Apple 호출은 2초 연결·5초 응답 제한을 사용하며 자동 코드 교환 재시도는 하지 않는다. 인증 코드는 1회용이므로 타임아웃 때 앱에서 새 로그인을 시작한다.

## 앱 책임
Apple 로그인, nonce 전달, Keychain, credential state 확인 및 로컬 로그아웃, 재로그인 안내와 탈퇴 확인 UI를 구현한다. 서버 준비가 앱 구현 완료를 의미하지 않는다.

## 참고 및 검증
- [Apple 사용자 검증](https://developer.apple.com/documentation/signinwithapple/verifying-a-user)
- [Apple 코드 교환](https://developer.apple.com/documentation/signinwithapplerestapi/generate-and-validate-tokens)
- [Apple 계정 삭제 및 토큰 폐기](https://developer.apple.com/documentation/technotes/tn3194-handling-account-deletions-and-revoking-tokens-for-sign-in-with-apple)
- 실제 사용자 Apple 로그인 최종 검증은 iOS 연결 후 수행한다. 자동 테스트는 생성한 RSA 키와 로컬 Apple HTTP 대역을 사용한다.
