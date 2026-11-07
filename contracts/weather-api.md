# 날씨 조회 API 계약

관련 요구사항: [지역별 날씨 데이터](../specs/weather-data/requirements.md). 이 문서는 앱·서버 사이 계약의 원본이다. 기상청 원본 코드와 응답은 서버가 정규화한다. 인증 조회로 공급 범위를 확인하기 전 고정 일수·시간 간격과 모든 필드의 제공을 보장하지 않는다.

## 요청
| 메서드·경로 | 입력 | 목적 |
| --- | --- | --- |
| `GET /v1/weather` | 필수 `lat`, `lon` | 현재 날씨, 현재부터 다음 24시간, 날짜별 요약 |
| `GET /v1/weather/timeline` | 필수 `lat`, `lon`, `date` | 선택 날짜의 실제 제공 가능한 시간별 자료 |

`lat`·`lon`은 유한한 십진수이고 각각 -90~90, -180~180 범위다. `date`는 실제 존재하는 한국 현지 날짜 `YYYY-MM-DD`다. 형식 오류는 400, 형식은 맞지만 지원하지 않는 국내 밖 위치·날짜는 422다. 국내 지원 여부와 날짜 범위는 공급 격자·지역 및 실제 자료 범위로 판정한다.

지역 검색과 저장 지역 관리는 이 API의 책임이 아니다. 로그인은 요구하지 않는다. 인증키는 서버에서만 사용한다.

## 공통 응답
| 필드 | 의미 |
| --- | --- |
| `provider` | `kma` |
| `timezone` | `Asia/Seoul` |
| `grid` | 공급 자료의 기상청 격자 `x`, `y` |
| `fetchedAt` | 포함된 자료를 마지막으로 성공 수집한 시각. HTTP 요청 시각으로 바꿔 적지 않음 |
| `status` | `complete`, `partial`, `stale` |
| `missing` | 누락된 필드·시간 구간과 이유. 정상 시 빈 배열 |
| `coverage` | 실제 제공 범위와 날짜별 시간 탐색 가능 여부. 전 구간이 연속한다고 가정하지 않음 |

모든 시각은 오프셋을 포함한 ISO 8601 문자열이다. `fetchedAt` 하나가 모든 값의 최신성을 뜻하지 않으며 각 값의 출처 정보에 실제 수집 시각도 남긴다.

`status`는 필요한 자료가 모두 있으면 `complete`, 일부만 있으면 `partial`이다. 오래된 자료가 포함되면 `stale`을 우선하며 부분 누락은 `missing`에 유지한다. 최대 오래된 시간은 확정 전이며 그 이전에는 무기한 오래된 응답을 정상으로 취급하지 않는다.

## 요약 응답
- `current`: 현재 기준 자료. `observedAt`과 값별 `provenance`를 제공한다. 메인에서 선택한 시각을 반영하지 않는다.
- `hourly`: 다음 24시간 범위의 제공 가능한 점 자료. 누락 구간은 `missing`에 남긴다.
- `daily`: 실제 제공 가능한 날짜별 요약. 각 행은 `date`, `temperatureMinC`, `temperatureMaxC`, `sky`, `precipitationType`, `timelineAvailable`과 출처를 가진다. 반일 단위만 있으면 오전/오후 구분을 보존하고 단일 대표 날씨를 임의 생성하지 않는다.
- `coverage`: 시간별·일별 제공 범위. 데이터 없는 기간을 7일·10일로 패딩하지 않는다.

## 타임라인 응답과 점 자료
공통 필드에 `date`와 `points`를 추가한다. `points`는 `time` 오름차순이고 같은 대상 시각의 중복 점은 없다. 아래 예시는 가상 수치이며 실제 응답 증적이 아니다.

```json
{
  "provider": "kma",
  "timezone": "Asia/Seoul",
  "grid": { "x": 60, "y": 127 },
  "date": "2026-11-02",
  "fetchedAt": "2026-11-02T15:12:00+09:00",
  "status": "partial",
  "coverage": {
    "date": "2026-11-02",
    "timelineAvailable": true,
    "intervals": [
      {
        "from": "2026-11-02T16:00:00+09:00",
        "to": "2026-11-02T17:00:00+09:00",
        "stepMinutes": 60
      }
    ]
  },
  "missing": [
    {
      "from": "2026-11-02T00:00:00+09:00",
      "to": "2026-11-02T16:00:00+09:00",
      "reason": "source_unavailable"
    },
    {
      "from": "2026-11-02T17:00:00+09:00",
      "to": "2026-11-03T00:00:00+09:00",
      "reason": "source_unavailable"
    }
  ],
  "points": [
    {
      "time": "2026-11-02T16:00:00+09:00",
      "stepMinutes": 60,
      "temperatureC": 12.4,
      "sky": "cloudy",
      "precipitationType": "rain",
      "precipitationProbabilityPercent": 60,
      "windSpeedMps": 3.2,
      "humidityPercent": 75,
      "source": "short_term_forecast",
      "issuedAt": "2026-11-02T14:00:00+09:00"
    }
  ]
}
```

`coverage.intervals`와 `missing`의 구간은 시작 포함·끝 제외다. `time`은 자료의 기준 시각이며 `stepMinutes`는 공급 간격이다. 값이 빠진 구간을 다음 정상 점까지 늘려 표시하지 않는다. 분 단위 눈금은 선택 UI이며 예보 해상도를 높이지 않는다.

| 필드 | 의미·단위 |
| --- | --- |
| `time` | 날씨 대상 시각 |
| `stepMinutes` | 공급 자료의 시간 간격 |
| `temperatureC` | 대상 시각 기온, ℃. 표시 반올림·화씨 변환은 앱 |
| `sky` | `clear`, `partly_cloudy`, `cloudy`, 또는 `null` |
| `precipitationType` | `none`, `rain`, `snow`, `rain_snow`, `drizzle`, `drizzle_snow_flurries`, `snow_flurries`, 또는 `null`. `rain`은 비·소나기를 포함하고 `drizzle_snow_flurries`는 빗방울눈날림 |
| `precipitationProbabilityPercent` | 강수확률, %. 실황에 없는 확률을 0으로 만들지 않음 |
| `windSpeedMps` | 풍속, m/s |
| `humidityPercent` | 상대습도, % |
| `source` | `observation`, `ultra_short_term_forecast`, `short_term_forecast`, `medium_term_forecast`, `mixed` |
| `issuedAt` | 예보 발표 시각. 관측 자료에서는 `null`이고 `observedAt` 사용 |
| `observedAt` | 관측 시각. 예보에서는 `null` |
| `provenance` | 값별 공급 자료·발표/관측·수집 시각. 혼합 출처에서는 필수 |

숫자·상태가 없으면 `null`로 전달하고 `missing`에 필드와 이유를 남긴다. 응답에서 빠진 필드를 0이나 맑음으로 해석하지 않는다. `none`은 강수 현상 없음, `null`은 알 수 없음이다. 기상청 원본의 강수형태 코드가 자료 유형마다 다르므로 자료 유형과 코드의 조합으로 변환한다. 미지원 코드는 알 수 없음으로 유지한다.

혼합 자료는 `source: mixed`, 공통 `issuedAt: null`을 사용하고 `provenance`에 필드별 출처를 제공한다. 현재 기온의 관측 시각과 하늘상태 예보의 대상 시각을 각각 확인할 수 있어야 한다. 정확한 원본 코드 매핑은 인증 조회 및 공식 코드표 확인 후 보완한다.

현재 서버의 공식 코드표 기반 변환은 초단기의 0/1/2/3/4/5/6/7을 각각 `none`/`rain`/`rain_snow`/`snow`/`rain`/`drizzle`/`drizzle_snow_flurries`/`snow_flurries`로 정리한다. 단기는 0~4만 변환한다. 미지원 코드는 `null`이다. 실제 공급 응답은 인증 조회로 추가 검증한다.

## 첫 서버 구현의 범위
Java 21·Spring Boot 4.1.2의 표현·응용·도메인·인프라 4레이어로 구현하며 DTO는 표현 레이어에 둔다. H2 파일 저장소는 로컬 검증용이고 운영 DB는 미정이다. 서버 내부 책임과 실행은 `weatherhood-server/README.md`, `docs/architecture.md`를 따른다.

첫 지원 위치는 공식 행정구역 대표 격자 1,632개이며 전국 육지의 모든 격자를 보장하지 않는다. 타임라인은 오늘부터 실제 제공 범위까지만 지원하고 최대 오늘+4일이다. 최신 단기예보를 정상 수집해 해당 날짜가 범위 밖임을 확인한 경우 422, 공급 조회 실패로 확인할 수 없는 경우 503을 반환한다.

점 자료는 기온을 최소 제공 조건으로 삼는다. 핵심 기온 점이 없는 시간은 누락 구간이며 `sky`, `precipitationType`, `windSpeedMps`, `humidityPercent`, `precipitationProbabilityPercent`, `precipitationAmount`의 결측은 값별 `missing`에 남긴다. 연장 구간의 수치 풍속이 없으면 `windSpeedMps: null`과 `windIntensity`의 `light`/`moderate`/`strong`을 제공한다.

요약의 `coverage.date`는 `null`이고 `coverage.intervals`는 실제 시간별 제공 구간이다. 날짜별 최고/최저는 공급처 값만 사용한다. 대표 날씨 집계는 미정이므로 `daily[].sky`·`precipitationType`은 `null`과 `aggregation_not_defined` 누락 이유로 전달하고 `periods`에 시간대별 상태를 보존한다. 다음 24시간의 `hourly`는 현재 시간 구간의 기준 점을 포함하며 점 개수를 고정하지 않는다.

수집 시간 예산·실패 재시도·최근 지역·오래된 자료 허용치는 서버 README와 `application.yml`의 로컬 개발 기본값으로 명시한다. 운영 정책은 실제 제공 지연 검증 후 확정한다. 오래된 예보는 명시적으로 허용하기 전 기본 제공하지 않는다. 첫 구현은 중기예보와 부가 지표 공급 연결을 완료한 것으로 보지 않는다.

## 강수량
강수량은 단순 숫자만으로 전달하지 않는다. 해당 값이 있는 항목에 `precipitationAmount`를 추가하며 `unit: mm`, `periodStart`, `periodEnd`, `kind`를 포함한다. `kind`는 `exact`, `less_than`, `range`, `qualitative`, `unknown`이다.

- `exact`는 `value`를 제공한다. 강수 없음과 수치 0의 원본 의미를 보존한다.
- `less_than`은 `upperBound`를 제공하고 임계값을 관측량처럼 `value`에 넣지 않는다.
- `range`는 `lowerBound`, `upperBound`와 경계 포함 여부를 보존한다.
- `qualitative`는 원본 강도 의미를 정규화한 `intensity`를 제공하며 수치를 만들지 않는다.
- `unknown`은 수치·강도 모두 `null`이고 누락 이유를 남긴다.

강수확률과 강수량은 별개다. 누적·예상 총량은 기준 기간·관측과 예보 중복·누락 처리 규칙을 확정한 뒤 제공하며, 첫 연동에서 임의 합산하지 않는다.

## 오류
오류 응답은 `error.code`, `error.message`와 필요한 경우 `error.retryAfterSeconds`를 가진다. 내부 URL·키·공급처 원문 오류를 그대로 내보내지 않는다.

| HTTP | 코드 | 상황 |
| --- | --- | --- |
| 400 | `INVALID_REQUEST` | 잘못된 좌표 형식·범위, 존재하지 않는 날짜 |
| 422 | `UNSUPPORTED_LOCATION` | 지원 지역 밖 |
| 422 | `UNSUPPORTED_DATE` | 지원 조회 기간 밖 |
| 503 | `WEATHER_UNAVAILABLE` | 사용할 자료가 없거나 공급처 실패로 제공할 수 없음 |
| 500 | `INTERNAL_ERROR` | 서버 내부 오류. 내부 원문·키·URL은 노출하지 않음 |

일부 사용 가능한 자료가 있으면 200과 `partial`·`stale`로 응답한다. 단, 최소 제공 데이터와 오래된 최대 시간은 확정해야 한다. `Retry-After` 헤더와 본문의 재시도 시간이 모두 있으면 일치시킨다.

## 참고 및 검증
- 근거: 기상청 공급처와 요약/날짜별 타임라인 API, 최소 48시간 보존, 부분·오래된·전체 실패 처리를 포함한 합의 설계.
- 인증키 조회 전 확인할 값·기간·조합 정책은 [설계의 미정 항목](../specs/weather-data/design.md)을 따른다.
- 계약 예시는 가상 JSON이며 외부 응답이나 서버 구현 완료를 뜻하지 않는다. OpenAPI와 실제 직렬화 스키마는 기술 스택 결정 및 공급처 검증 후 맞춘다.
