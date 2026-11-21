# 지역 API 계약

모든 응답은 기존 ApiResponse의 data/error 형식이다. 지역 객체는 `{ "id": "내부 지역 ID", "name": "전체 지역명" }`이며 기존 오늘 날씨 regionId와 같다.

## 공개 조회
- `GET /api/v1/regions?query=종로구&page=0`: 앞뒤 공백만 제거한 2~100자 문자열. 내부 공백·문장 순서 그대로 전체 지역명에 포함되는 결과를 찾는다. `%`, `_`는 검색 문자다. 페이지는 0부터, 고정 20개. 전체 이름 정확 일치 우선, 전체 이름·ID 오름차순. data=`{items: [지역 객체], page: 0, hasNext: false}`. 결과 없음은 빈 배열이며 오류가 아니다.
- `GET /api/v1/regions/{regionId}`: 지역 객체. 존재하지 않으면 404.
- `POST /api/v1/regions/resolve`: `{latitude: 37.5841, longitude: 126.9707}`. WGS84 위도 -90~90, 경도 -180~180, 유한한 숫자. data는 지역 객체. GPS를 URL·DB에 저장하지 않는다. 카카오 H 코드만 사용한다.

## 개인 지역 (Bearer 필수)
- `GET /api/v1/me/regions`: data=`{items: [지역 객체], defaultLocation: {type: "current_location", region: null}}`.
- `PUT /api/v1/me/regions/{regionId}`: 최대 10개. 새 지역은 끝에 추가하며 중복이면 기존 순서 유지. data는 최신 개인 지역 목록.
- `DELETE /api/v1/me/regions/{regionId}`: 없는 저장 항목 삭제도 성공한다. 기본 지역을 삭제하면 현재 위치로 복귀한다. 현재 표시 날씨는 앱에서 유지한다. data는 최신 목록.
- `PUT /api/v1/me/regions/order`: `{regionIds: ["id2", "id1"]}`. 현재 저장 집합 전체를 중복 없이 정확히 보내야 한다. data는 최신 목록.
- `PUT /api/v1/me/default-location`: `{type: "current_location", regionId: null}` 또는 `{type: "saved_region", regionId: "저장한 ID"}`. data는 최신 목록.

저장 지역을 기본으로 설정한 응답의 defaultLocation은 `{type: "saved_region", region: {id, name}}`이다. 마지막 화면에서 선택한 지역과 다음 실행 기본 지역은 별개다. 현재 위치는 저장 개수에 포함하지 않는다. 동시 요청은 사용자별로 직렬화하고 최종 성공한 변경을 반영한다. 순서 변경 중 목록이 달라지면 409 후 다시 조회한다.

## 오류
- 400 INVALID_REQUEST: 검색·페이지·좌표·기본 모드 형식 오류.
- 401 AUTH001: 인증 실패.
- 404 REGION_NOT_FOUND: 내부 지역 없음.
- 422 UNSUPPORTED_REGION: 지원 중지 지역.
- 422 REGION001: 좌표에 행정동 결과 없음(해외·해상 포함).
- 503 REGION002: 카카오 행정동 코드를 내부 DB에서 찾지 못함.
- 503 REGION003: 카카오 통신·인증·형식 오류.
- 409 REGION004: 저장 지역 10개 초과.
- 409 REGION005: 순서 변경의 집합 불일치·중복.
- 409 REGION006: 기본으로 설정할 지역이 저장 목록에 없음.

[지역 요구사항](../specs/location-selection/requirements.md), [로그인 계약](auth-api.md)을 따른다.
