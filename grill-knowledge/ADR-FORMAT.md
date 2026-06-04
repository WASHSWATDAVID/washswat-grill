# ADR (Architecture Decision Record) 포맷

ADR은 **되돌리기 어려운** 결정을 박제하는 문서다. 미래의 데이비드(또는 협업자)가 "왜 이렇게 했지?"라고 의아해할 만한 결정이 대상.

코딩 프로젝트에서 빌려온 개념이지만, 일반 업무에서도 동일하게 유용하다 — 딜 구조 선택, 법무 자문 선정, 협상 전략 결정 등.

## 파일 이름 규칙

```
docs/adr/<순번>-<짧은-제목-kebab-case>.md
```

예시:
- `docs/adr/0001-notion-as-single-source-of-truth.md`
- `docs/adr/0002-md-over-docx.md`
- `docs/adr/0003-wwg-deal-structure-as-merger.md`

순번은 4자리, 0부터 패딩. 한 번 매긴 번호는 절대 재사용/재배치하지 않는다 — 역사이므로.

## 기본 구조

```markdown
# ADR-NNNN: [결정 제목]

- **Status**: Proposed / Accepted / Superseded by ADR-XXXX / Deprecated
- **Date**: YYYY-MM-DD
- **Deciders**: 데이비드 [, 다른 관계자]

## Context

이 결정이 필요해진 배경. 무엇이 문제였고, 왜 지금 결정해야 했는지. 3-7문장.

## Decision

내린 결정 자체. 짧고 명확하게. "우리는 X를 한다."

## Alternatives Considered

진지하게 고려했던 대안들. 각 대안마다:
- **[대안 이름]**: 한 문장 설명
  - 장점: ...
  - 단점: ...
  - **기각 이유**: ...

## Consequences

이 결정으로 인해 따라오는 결과들. 좋은 것과 나쁜 것 모두.

- 좋음: ...
- 나쁨/위험: ...
- 후속 액션 필요: ...

## References

관련 노션 페이지, 메일 스레드, 회의록 등 외부 자료 링크.
```

## 실제 예시 — `0001-notion-as-single-source-of-truth.md`

```markdown
# ADR-0001: Todo의 단일 출처는 Notion이다

- **Status**: Accepted
- **Date**: 2026-05-15
- **Deciders**: 데이비드

## Context

데이비드의 할 일은 Notion, 메일 inbox, Slack 미해결, 머릿속, 종이 메모 등 여러 곳에 분산돼 있었다. 어디를 봐야 "지금 내가 해야 할 일"이 다 보이는지 불명확했고, 같은 작업이 중복 등록되거나 누락되는 일이 반복됐다.

## Decision

**Notion의 "David's Todo List" DB가 유일한 정답(single source of truth)**이다.

- URL: https://www.notion.so/35dd4a5a601f805daca1eb39ca2c8f1c
- Data Source: `collection://35dd4a5a-601f-8053-b432-000b0bf54abb`

다른 모든 소스(메일, Slack, 회의 등)에서 발견된 액션 아이템은 즉시 이 DB에 등록한다.

## Alternatives Considered

- **Asana 통합**: 팀원과 공유에 더 적합
  - 장점: 공유, 권한 관리
  - 단점: 1인 워크플로엔 무거움, 메모/문서와 분리됨
  - **기각 이유**: 데이비드는 솔로 워크플로, 문서/메모와 같은 도구 안에 있는 게 더 중요

- **메일 inbox를 SSOT로**: 어차피 액션은 메일에서 시작
  - 장점: 별도 시스템 불필요
  - 단점: 메일은 검색만 잘 되고 상태 관리/우선순위 부재
  - **기각 이유**: 상태 추적 불가능

## Consequences

- **좋음**: 어디서 할 일을 봐야 할지 명확. "오늘 할 일" 스킬이 한 곳만 보면 됨
- **좋음**: Done 항목이 검색 가능한 히스토리로 남음
- **위험**: Notion이 장애나면 작업 마비 — 백업 전략 필요
- **후속 액션**: 세션 시작 시 항상 이 DB를 먼저 확인하는 룰을 CLAUDE.md에 명시 (완료)

## References

- 노션 DB: https://www.notion.so/35dd4a5a601f805daca1eb39ca2c8f1c
- 관련 CLAUDE.md 섹션: "1. Todo 소스 — Notion이 유일한 정답"
```

## 작성 시 주의

1. **ADR은 한 번 작성되면 거의 수정하지 않는다** — 역사를 기록한 것이기 때문
2. 결정이 바뀌면 **새 ADR을 작성**하고, 원래 ADR의 Status를 "Superseded by ADR-XXXX"로 업데이트
3. 결정이 폐기되면 Status를 "Deprecated"로
4. ADR은 "지금 무엇이 진실인가"가 아니라 "왜 이 결정을 내렸는가"를 기록한다 — 후자가 더 가치 있음

## ADR을 *쓰지 말아야* 할 때

- 작은 결정 (어떤 폰트를 쓸지 등)
- 명백한 결정 (마감이 정해진 일은 그 날짜에 끝낸다)
- 임시 결정 (다음 주에 다시 보자는 것)
- 외부 강제 결정 (법으로 정해져서 어쩔 수 없는 것 — 기록 가치 없음)

세 조건(되돌리기 어려움 + 컨텍스트 없으면 놀라움 + 진짜 트레이드오프)을 다시 확인하라. 셋 다 맞으면 쓴다. 하나라도 빠지면 노션 메모로 충분하다.
