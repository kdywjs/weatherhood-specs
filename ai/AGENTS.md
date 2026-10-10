# Weatherhood 작업 공간 안내

이 파일의 원본은 `weatherhood-specs/ai/AGENTS.md`이며 통합 작업 공간 루트의 `AGENTS.md`는 사본입니다. 경로는 통합 작업 공간 루트를 기준으로 합니다.

## 레포와 문서

- `weatherhood-specs/README.md`: 문서 위치와 관리 책임을 먼저 확인합니다.
- `weatherhood-specs/docs/`: 제품과 시스템의 공통 맥락입니다.
- `weatherhood-specs/specs/<기능>/`: 해당 기능의 요구사항·설계·작업입니다.
- `weatherhood-specs/contracts/`: 앱·서버 공통 인터페이스의 원본입니다.
- `weatherhood-specs/rules/`: 작업에 관련된 팀 공통 규칙을 읽고 따릅니다.
- 작업 등록, 진행상황 갱신 및 일정 관리 시 `weatherhood-specs/rules/task-management.md`를 읽고 따릅니다.
- `weatherhood-specs/ai/skills/`: 관련 스킬이 있으면 해당 지침을 확인합니다.
- 이슈·PR 작성 시 `weatherhood-specs/ai/skills/write-issue-pr/SKILL.md`를 따릅니다.
- 스펙 원본과 루트 사본의 `AGENTS.md`를 동기화할 때는 `weatherhood-specs/ai/skills/sync-agents/SKILL.md`를 따릅니다.
- `weatherhood-app/`, `weatherhood-server/`: 작업 대상 레포의 `AGENTS.md`가 있으면 함께 읽습니다. 내부 구현 문서는 해당 코드 레포에서 관리합니다.

## 스펙 합의 후 구현

- 새로운 기능·문서·규칙·템플릿·스킬 등을 추가하거나 변경하자는 제안은 먼저 스펙을 함께 정하자는 뜻으로 해석합니다. 제안만으로 바로 구현하거나 파일·GitHub 설정을 변경하지 않습니다.
- 구현 전에 무엇을 만들지, 왜 필요한지, 제약, 범위와 범위 밖인 것, 예외 상황을 사용자와 함께 명확히 합니다. 필요한 내용만 간결하게 정리하며, 미정인 사항을 임의로 확정하지 않습니다.
- 스펙에 합의하고 사용자가 구현을 요청한 뒤 작업합니다. 이미 합의한 범위에 명시적인 구현 요청이 있으면 같은 확인을 반복하지 않습니다.

## 현재 단계

문서 구조만 마련한 단계입니다. 없는 규칙·스펙을 합의된 정책으로 간주하지 않습니다. 개발 절차·테스트·승인·자동 동기화 정책은 추후 정의합니다. Git 작업은 변경 대상 레포별로 수행합니다.
