# 인증 API 계약

기본 경로 `/api/v1`. 성공은 `{ "data": {...}, "error": null }`, 실패는 `{ "data": null, "error": { "code": "...", "message": "...", "retryAfterSeconds": null } }`다. 시각은 UTC ISO 8601이다. 토큰·challenge 응답을 캐시하지 않는다.

| 메서드·경로 | 인증 | 요청 | data |
| --- | --- | --- | --- |
| POST /auth/apple/challenges | 없음 | 본문 없음 | challengeId(UUID), nonce(Apple 요청에 그대로 전달), expiresAt |
| POST /auth/apple | 없음 | challengeId, identityToken, authorizationCode | accessToken, tokenType=`Bearer`, expiresAt, user={id} |
| POST /auth/logout | Bearer | 본문 없음 | completed=true |
| GET /me | Bearer | 없음 | id |
| DELETE /me | Bearer | 본문 없음 | completed=true |

개인 API 헤더는 `Authorization: Bearer <accessToken>`이다. 사용자 ID를 요청에서 받아 권한을 결정하지 않는다. Bearer 토큰은 30일 고정 만료이며 별도 갱신 토큰 API는 없다. 로그아웃은 현재 토큰만 종료한다. 탈퇴는 Apple 폐기까지 성공한 뒤 completed=true를 반환한다.

```json
{
  "data": {
    "accessToken": "<발급 시에만 반환되는 무작위 세션 토큰>",
    "tokenType": "Bearer",
    "expiresAt": "2026-12-21T03:00:00Z",
    "user": {"id": "211b67bb-e052-4eb6-98ef-12f78e1baac9"}
  },
  "error": null
}
```

## 오류
- 400 INVALID_REQUEST: 필수값·형식·길이 오류.
- 401 AUTH001: 세션 없음·무효·만료. WWW-Authenticate: Bearer.
- 401 AUTH002: Apple 토큰·코드·challenge·nonce 검증 실패.
- 503 AUTH003: Apple 통신·키 설정·서명 또는 토큰 보관 설정 실패. 키와 공급 응답은 공개하지 않는다.

이메일·실명·Apple sub·refresh token은 응답하지 않는다. 인증 성공 여부와 독립적으로 기존 날씨/지역 API는 공개다. [요구사항](../specs/authentication/requirements.md)을 참조한다.
