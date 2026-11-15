# 오늘 날씨 API 계약

[요구사항](../specs/today-weather/requirements.md)과 [설계](../specs/today-weather/design.md)에 대응하는 초기 계약이다. 서버 구현 완료를 뜻하지 않는다. 공급처 표본 조회 결과는 [설계의 참고 및 검증](../specs/today-weather/design.md#참고-및-검증)을 참조한다. 다른 날짜·주간 날씨 계약과 분리한다.

## 요청

`GET /v1/weather/today?regionId={regionId}`

- `regionId`: 지역 검색·현재 위치 변환에서 받은 내부 지역 ID 문자열. 필수·비어 있지 않은 불투명 식별자이며 행정동명·공급 격자·법정동 코드를 대신 보내지 않는다. 구체적인 ID 발급 형식은 지역 계약에서 관리한다.
- 현재 위치도 먼저 행정동으로 변환한 뒤 동일한 ID로 요청한다. 이 API는 GPS 좌표를 받지 않는다.
- 날짜는 요청 진입 시 서버의 `Asia/Seoul` 날짜로 고정한다. 처리 중 자정을 넘어도 응답은 해당 날짜의 24개 슬롯을 유지한다. 클라이언트가 새 날짜가 필요하면 다시 요청한다.
- 날짜·단위·강제 갱신 파라미터는 제공하지 않는다. 서버가 공급 자료별 갱신 여부를 판단한다.
- 로그인 사용자 진입이라는 제품 전제는 유지한다. 인증 헤더·세션 방식과 401/403 상세는 인증 계약에서 정한다. 공급처 키를 앱에서 받거나 응답에 포함하지 않는다.

## 성공 응답

정상·부분 성공 모두 HTTP 200, `application/json`이다. 정의한 키는 항상 존재하며 값이 없으면 null이다. `hourly`는 `"00:00"`부터 `"23:00"`까지 정확히 24개 시간 키를 갖는 객체다. 일출·일몰 필드는 이번 계약에 포함하지 않는다.

| 필드 | 타입·의미 |
| --- | --- |
| `region` | `{id, name}`. 요청한 내부 ID와 상위 행정구역을 포함한 표시명 |
| `date` | 한국 날짜 `YYYY-MM-DD` |
| `timezone` | 고정 `Asia/Seoul` |
| `current` | `{temperatureC, airQuality}`. 최신 실황 기온과 최신 미세먼지 요약. 값별 null 허용 |
| `daily` | `{temperatureMinC, temperatureMaxC}`. 오늘 하루 단기예보 최저·최고, 각각 number 또는 null |
| `hourly` | `HH:mm` 시간 키에 시간별 객체 또는 null을 연결한다. 해당 시간 모든 값이 없으면 `"01:00": null`처럼 표현한다. |

최신 미세먼지 요약과 시간별 미세먼지는 같은 AirQuality 구조를 사용한다. `current`와 `daily` 컨테이너는 유지한다. current의 두 값이 없으면 `{temperatureC: null, airQuality: null}`이다.

알려진 정상값이 하나라도 있으면 HTTP 200으로 확보한 자료를 반환한다. 기온·상태·미세먼지·하루 공통 정보 모두 없으면 503이다. 날짜·지역 등 메타데이터만 존재하는 것은 성공으로 보지 않는다. 부분 성공 여부나 누락 원인은 별도 응답 필드로 전달하지 않는다.

시간 키는 `date`와 `timezone`을 조합해 대상 시각으로 해석한다. 키를 생략하거나 `24:00`을 추가하지 않는다. JSON 객체의 멤버 순서는 계약으로 보장하지 않으며 클라이언트는 시간 키로 접근하고 표시 시 시간순으로 정렬한다. 별도의 `time`·`data` 래퍼는 두지 않는다.

## 시간별 객체

| 필드 | 타입·의미 |
| --- | --- |
| `temperatureC` | number 또는 null, °C |
| `sky` | `clear`, `partly_cloudy`, `cloudy` 또는 null. 각각 맑음·구름 많음·흐림 |
| `precipitationType` | `none`, `rain`, `shower`, `snow`, `rain_snow`, `drizzle`, `drizzle_snow_flurries`, `snow_flurries` 또는 null |
| `precipitationAmount` | 아래 강수량 객체 또는 null |
| `windSpeedMps` | 0 이상 number 또는 null, m/s |
| `humidityPercent` | 0~100 number 또는 null |
| `precipitationProbabilityPercent` | 0~100 number 또는 null |
| `airQuality` | AirQuality 객체 또는 null |

시간 키는 슬롯의 대상 시각이지 모든 항목의 측정 시각이 아니다. 상태값 0·`none`은 정상 데이터다. 알 수 없는 공급 코드는 null로 정규화한다. 과거 강수확률은 예보값으로 취급한다. 보충값의 출처·발표·관측·수집 시각은 서버 내부에 항목별로 보존하며 이번 응답은 이를 모두 나열하지 않는다.

## AirQuality

`{pm10, pm25}`로 구성한다. 각각 `{concentration, grade, periodStart, periodEnd}` 또는 null이며 둘 다 없으면 AirQuality 자체가 null이다.

- `concentration`: 0 이상 number 또는 null, 단위 µg/m³.
- `grade`: `good`, `moderate`, `bad`, `very_bad` 또는 null. 각각 좋음·보통·나쁨·매우 나쁨이며 공급처 1시간 등급을 사용한다. 24시간 등급과 혼용하지 않는다.
- `periodStart`, `periodEnd`: 공급 측정의 집계 구간. 농도나 등급을 제공하려면 구간을 식별할 수 있어야 한다. 농도·등급 모두 없으면 해당 PM 객체는 null이다.
- current는 최신 자료, hourly는 슬롯에 대응하는 자료다. 미래 슬롯에 최신값을 복사하지 않는다. 시간 평균 자료는 집계 종료 시각에 대응하는 슬롯에 연결하고 날짜 경계를 그대로 보존한다. 공급처 24시 표기는 다음 날 00시로 정규화한다.

## 강수량

허용 구조는 두 가지이며 정의하지 않은 필드를 섞지 않는다. 단위는 고정 mm이다.

```json
{"kind":"exact","value":2.0,"unit":"mm","periodStart":"2026-11-15T13:00:00+09:00","periodEnd":"2026-11-15T14:00:00+09:00"}
```

```json
{"kind":"range","lowerBound":0,"upperBound":1,"lowerInclusive":true,"upperInclusive":false,"unit":"mm","periodStart":"2026-11-15T13:00:00+09:00","periodEnd":"2026-11-15T14:00:00+09:00"}
```

- value·lowerBound는 0 이상 유한한 숫자다. upperBound는 lowerBound보다 큰 유한한 숫자 또는 null이다. null 상한은 상한 없음이며 그 경우 upperInclusive는 false다.
- 정확히 0은 exact의 value 0이다. 1mm 미만은 위 예처럼 0 이상 1 미만이다. 별도 less_than·문자열·정성 유형을 허용하지 않는다.
- 수치화 근거 없는 정성값은 객체 전체를 null로 두고 강수 형태는 유지한다.
- 기간은 시작 포함·종료 제외로 정규화하며 시작은 종료보다 앞선다. 관측과 예보의 실제 공급 집계 구간을 보존하고, 슬롯에 맞추려고 구간을 변경하지 않는다. 공급 구간 해석을 검증하지 못한 값은 제공하지 않는다.

## 누락과 이전 자료

- 일부 값이 없으면 해당 필드만 null, 시간별 제공 값이 모두 없으면 해당 시간 키의 값 전체를 null로 둔다. 유효한 0이나 강수 없음은 누락으로 취급하지 않는다.
- `status`, `issues`, `hasStaleData`는 응답에 포함하지 않는다. 누락 원인, 갱신 실패와 이전 자료 사용 여부는 서버 내부에서 관리한다.
- 서버가 요구사항의 허용 한도 내에 있는 이전 자료만 반환한다. 기한을 넘은 값은 다른 유효 자료로 보충하거나 null로 둔다. 클라이언트에 자료 유효성 판단을 맡기지 않는다.
- 내부 공급 URL·원문 오류·키는 포함하지 않는다. 전체 조회 실패에는 아래 공통 오류 응답을 사용한다.

## 오류

```json
{"error":{"code":"WEATHER_UNAVAILABLE","message":"오늘 날씨 정보를 불러올 수 없습니다.","retryAfterSeconds":300}}
```

| HTTP | code | 조건 |
| --- | --- | --- |
| 400 | `INVALID_REQUEST` | regionId 누락·빈 값·중복 또는 요청 형식 오류 |
| 404 | `REGION_NOT_FOUND` | 지역 원본에 없는 ID |
| 422 | `UNSUPPORTED_REGION` | 존재하지만 국내 행정동 체계 지원 대상이 아닌 지역 |
| 503 | `WEATHER_UNAVAILABLE` | 대기 상한 후 사용할 실제 데이터가 전혀 없음. 알려진 국내 지역의 공급 매핑 실패도 이 범주이며 지원 지역 밖으로 위장하지 않음 |
| 500 | `INTERNAL_ERROR` | 내부 처리 실패 |

retryAfterSeconds는 알려진 재시도 대기 시간이 있을 때 남은 초의 올림값이고 그 외에는 null이다. 503에 값이 있으면 Retry-After 헤더도 같은 값으로 보낸다. 예시 300은 고정 응답값이 아니다. 200 응답은 즉시 재요청을 요구하지 않으며 서버 내부 재시도 제한을 적용한다.

## 참고 및 검증

- 초기 요청·응답 계약이며 실제 서버 응답 증적이 아니다. 예시 수치는 가상이다.
- 인증 계약, 공급 코드·집계 구간의 실제 매핑 검증은 구현 선행 작업이다. 절대 시각(`periodStart`, `periodEnd`)은 ISO 8601 오프셋 포함 문자열이며 한국 시각은 +09:00이다. 날짜는 `YYYY-MM-DD`, 시간 키는 `HH:mm`이다.

- [부분 응답 전체 예시](examples/today-weather.partial.json): 24개 슬롯과 null·범위 강수량·최신 요약을 포함한다.
