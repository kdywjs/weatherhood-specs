# 로그인 앱 작업

이번 구현은 서버 API·테스트 우선이며 앱 구현은 후속이다. [설계](design.md)와 [API 계약](../../contracts/auth-api.md)에 따라 Sign in with Apple, nonce 전달, Keychain, credential state 확인, 만료·로그아웃·탈퇴 UI를 연결한다. 앱 Bundle ID를 등록된 co.kr.ansandy.weatherhood와 맞추고 Sign in with Apple entitlement를 연결한다. 실제 기기 로그인과 Apple 연결 해제는 이 단계에서 검증한다.
