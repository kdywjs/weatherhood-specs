# Ponytail 안내

## 목적과 동작

Ponytail은 필요한 일을 끝까지 해결하면서 새 코드와 불필요한 추상화를 줄이도록 AI를 안내하는 스킬이다. 기존 코드, 표준 라이브러리, 플랫폼 기능과 설치된 의존성을 먼저 살펴본다. 오류 처리·보안·접근성은 유지하고 복잡한 로직은 검증하도록 안내한다.

`lite`는 더 작은 대안을 제안하고, 기본값 `full`은 최소한의 완전한 변경을 추구한다. `ultra`는 요청 자체의 필요성도 검토한다. 실제 실행 지침은 [SKILL.md](SKILL.md)에 있다.

## 공유 원본

- 출처: [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail)
- 가져온 커밋: `9b58c1ffb790c075ca32e70a89cf1d80588f4abf`
- 원본 파일: [skills/ponytail/SKILL.md](https://github.com/DietrichGebert/ponytail/blob/9b58c1ffb790c075ca32e70a89cf1d80588f4abf/skills/ponytail/SKILL.md)
- 라이선스: [MIT](LICENSE)

이 폴더에는 기본 스킬 원본을 수정 없이 보관한다. 팀의 공유 자료이며 이 위치에 있는 것만으로 각 AI 도구에 설치되지는 않는다. 원본 업데이트와 프로젝트용 수정은 별도로 검토한다.

## 설치와 사용

### 공유본 사용

사용 중인 AI에게 이 폴더의 `SKILL.md`를 읽고 적용하도록 요청하거나, 사용하는 도구의 스킬 설치 경로에 폴더를 복사한다. 설치 위치와 호출 문법은 도구에 맞춰 정한다. 기존에 같은 이름의 스킬이 있다면 덮어쓰기 전에 비교한다.

예시: “`weatherhood-specs/ai/skills/ponytail/SKILL.md`를 읽고 이번 코드 변경에 적용해줘.”

### 공식 저장소에서 스킬 설치

```sh
npx skills add DietrichGebert/ponytail
```

설치할 AI 도구와 스킬을 선택한다. `--global`을 추가하면 사용자 전역으로 설치한다. 이 방식은 공식 저장소의 스킬을 가져오므로 여기 보관한 커밋과 버전이 다를 수 있다. 기본 스킬만 원한다면 `ponytail`을 선택한다.

### Codex 플러그인 설치

```sh
codex plugin marketplace add DietrichGebert/ponytail
codex plugin add ponytail@ponytail
```

공식 안내에 따라 Codex의 `/hooks`에서 두 생명주기 훅을 검토·신뢰하고 새 스레드를 시작한다. Codex 데스크톱 앱은 재시작한다. 훅을 실행하려면 `node`가 PATH에 있어야 한다.

## 스킬과 플러그인의 차이

| 항목 | 이 폴더의 기본 스킬 | 공식 Codex 플러그인 |
| --- | --- | --- |
| 구성 | 기본 `ponytail` 지침 하나 | 기본 스킬과 review·audit·debt·gain·help 등 추가 스킬 및 훅 |
| 적용 | AI가 스킬을 읽거나 선택했을 때 지침 적용 | 훅 설정 후 자동 활성화와 지침 주입 지원 |
| 모드 | 지침의 lite·full·ultra를 AI에게 요청 | 훅을 통한 모드 관리와 전환 지원 |
| 의존성 | Markdown 스킬을 읽을 수 있는 AI 도구 | Codex 플러그인 지원과 훅 실행용 Node.js |
| 버전 | 이 폴더에 기록한 커밋 | 플러그인으로 설치한 버전 |

스킬 문서의 세션 유지·모드 전환 설명과 플러그인의 실제 훅 동작은 구분한다. Markdown만 복사해도 훅이 설치되는 것은 아니다. Codex CLI·IDE에서 플러그인 스킬은 `$ponytail:ponytail-review`처럼 네임스페이스로 호출한다.

설치 및 기능 차이의 기준: [공식 설치 안내](https://github.com/DietrichGebert/ponytail/blob/9b58c1ffb790c075ca32e70a89cf1d80588f4abf/INSTALL.md), [공식 README](https://github.com/DietrichGebert/ponytail/blob/9b58c1ffb790c075ca32e70a89cf1d80588f4abf/README.md). 다른 AI 도구의 설치 방법은 해당 안내를 참고한다.
