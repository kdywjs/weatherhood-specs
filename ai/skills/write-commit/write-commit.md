# 커밋 작성 스킬

## 목적과 기준

Weatherhood의 커밋 양식을 스킬에 담아 매번 규칙 파일을 다시 읽지 않고 메시지를 작성하거나 요청한 변경을 커밋한다.

- 작성일: 2026-10-11 (Asia/Seoul)
- 기준 규칙: [rules/commit.md](../../../rules/commit.md)
- 기준 커밋: [d66db4ea633e9b2812d644ace994f1dafc00338d](https://github.com/kdywjs/weatherhood-specs/commit/d66db4ea633e9b2812d644ace994f1dafc00338d)

Conventional Commits 형식과 한글 제목을 사용하며 scope·본문·이슈 참조는 선택이다. 규칙이 바뀌면 별도로 스킬을 갱신하고 위 기준 정보도 수정한다. 자동으로 최신 규칙을 반영하지 않는다.

## 설치와 사용

이 폴더는 팀 공유 원본이다. 사용하는 AI 도구의 스킬 경로에 폴더를 설치하거나 [SKILL.md](SKILL.md)를 읽도록 요청한다. 현재 통합 작업 공간에서는 `.agents/skills/write-commit`을 이 폴더에 연결해 사용한다. 원본 수정이 로컬 연결에도 반영된다.

- “`$write-commit`으로 현재 변경의 커밋 메시지만 작성해줘.”
- “`$write-commit`으로 커밋 규칙 변경만 커밋해줘.”
- “`$write-commit`으로 이번 변경을 커밋하고 푸시해줘.”

메시지만 요청하면 커밋하지 않는다. 커밋 시 관련 변경만 포함하고, 푸시는 명시적으로 요청할 때만 수행한다. 실제 실행 절차는 `SKILL.md`에 있다.
