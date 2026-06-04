---
name: grill-setup
description: "데이비드의 프로젝트 폴더에 CLAUDE.md, CONTEXT.md, docs/agents/, docs/adr/, docs/knowledge/ 구조를 세팅하는 스킬. 새 프로젝트를 시작할 때 또는 기존 프로젝트를 체계화하고 싶을 때 사용. \"프로젝트 폴더 세팅해줘\", \"CLAUDE.md 세팅해줘\", \"knowledge 구조 만들어줘\", \"이 프로젝트 정리해줘\", \"지식 구조 잡아줘\" 같은 요청에 반드시 이 스킬을 사용할 것. grill-* 패밀리: grill-knowledge(심층 인터뷰), grill-intake(harness 배치 결정), grill-sync(즉시 사실 업데이트)."
---

# Grill Setup

데이비드의 일반 업무 프로젝트(코딩이 아닌 — 딜, 법무, 운영, 리서치 등)를 위한 지식 구조를 스캐폴딩한다. 다른 스킬들과 미래의 자기 자신이 일관되게 참조할 수 있도록 세 가지를 세팅한다:

- **Todo / 작업 트래커**: 이 프로젝트의 할 일은 어디에서 관리하는가
- **Priority / Status 어휘**: 어떤 우선순위/상태 단어를 쓰는가
- **도메인 문서 레이아웃**: `CONTEXT.md` 하나(단일)인가, 멀티 컨텍스트 레이아웃인가

이건 결정론적 스크립트가 아니라 **대화 기반 스킬**이다. 탐색 → 발견한 것 제시 → 사용자 확인 → 쓰기, 이 순서로 진행한다.

---

## Process

### 1. Explore

현재 폴더 상태를 파악한다. 가정하지 말고 실제로 읽어볼 것:

- `CLAUDE.md` — 이미 존재하는가? 어떤 내용이 들어있는가? `## Agent skills` 섹션이 이미 있는가?
- `CONTEXT.md` — 존재하는가?
- `CONTEXT-MAP.md` — 존재하면 이미 멀티 컨텍스트로 운영 중이라는 신호
- `docs/agents/` — 이 스킬의 이전 산출물이 있는가?
- `docs/adr/` — 의사결정 기록이 쌓여 있는가?
- `docs/knowledge/` — 참조용 지식 자료가 있는가?
- 폴더 내 하위 프로젝트(예: `wwg-deal/`) — 자기 자신의 `CLAUDE.md`나 `CONTEXT.md`를 갖고 있는가?

### 2. Present findings and ask

발견한 것을 짧게 요약해서 사용자에게 보여준다. 그 다음 **세 가지 결정을 한 번에 하나씩** 사용자와 함께 한다. 세 개를 한꺼번에 던지지 말 것 — 사용자가 답한 뒤에 다음으로 넘어간다.

각 섹션은 짧은 설명(이게 뭐고, 왜 필요하고, 선택에 따라 뭐가 달라지는지)으로 시작한다. 그 다음에 선택지와 기본값을 제시한다.

사용자가 이 용어들을 모른다고 가정한다.

---

**섹션 A — Todo / 작업 트래커**

> **설명**: "작업 트래커"는 이 프로젝트의 할 일이 어디에 있는지를 의미한다. 미래의 다른 스킬들 (예: "오늘 할 일 보여줘", "이 메일에서 액션 아이템 등록해줘") 이 어디를 봐야 할지 알아야 한다. 데이비드는 보통 Notion을 쓰지만, 프로젝트마다 다를 수 있다.

기본 자세: 데이비드의 메인 Todo DB가 Notion에 있으므로 그걸 기본값으로 제안한다. 다른 옵션:

- **Notion (David's Todo List)** — `https://www.notion.so/35dd4a5a601f805daca1eb39ca2c8f1c` 의 통합 DB에서 관리. Data Source: `collection://35dd4a5a-601f-8053-b432-000b0bf54abb`
- **로컬 markdown** — 이 폴더 안의 `tasks/` 또는 `inbox.md`에서 관리 (작은 프로젝트나 Notion과 분리하고 싶을 때)
- **별도 Notion DB** — 이 프로젝트 전용의 다른 Notion DB가 있다면 URL과 Data Source ID를 받는다
- **기타** (Linear, Asana, 메일 inbox 등) — 사용자가 워크플로를 한 문단으로 설명; 스킬이 freeform prose로 기록

---

**섹션 B — Priority / Status 어휘**

> **설명**: 데이비드의 Notion Todo DB는 `Priority`(High/Medium/Low)와 `Status`(Not started/In progress/Done) 필드를 쓴다. 하지만 프로젝트마다 다른 어휘가 필요할 수 있다 (예: 법무 케이스는 "검토중/회신대기/종료", 딜은 "탐색/협상/실사/클로징"). 미래의 다른 스킬이 라벨을 잘못 만들지 않도록 여기 매핑해둔다.

기본값 (Notion Todo와 동일):

- Priority: `High` (마감 임박/타인 대기/딜·계약·법무 영향), `Medium` (중요하지만 며칠 여유), `Low` (참고용)
- Status: `Not started`, `In progress`, `Done`

사용자가 오버라이드하고 싶은 게 있는지 묻는다. 기본이 맞으면 그대로 둔다. 프로젝트별 특수 상태(예: WWG 딜에 "인사이트 회신대기" 같은 게 자주 나오면)가 있으면 추가로 기록.

---

**섹션 C — 도메인 문서 레이아웃**

> **설명**: 어떤 스킬들은 `CONTEXT.md`를 읽어서 이 프로젝트의 용어를 학습하고, `docs/adr/`에서 과거 결정들을 본다. 이 프로젝트가 단일 컨텍스트(예: 단순 Todo 관리)인지, 여러 컨텍스트가 섞인 큰 프로젝트(예: WWG 딜 안에 법무/재무/협상/PR 트랙)인지에 따라 위치가 달라진다.

- **단일 컨텍스트** — 루트에 `CONTEXT.md` 하나 + `docs/adr/` 하나. 대부분의 프로젝트가 이쪽.
- **멀티 컨텍스트** — 루트에 `CONTEXT-MAP.md`가 각 서브 컨텍스트의 `CONTEXT.md` 위치를 가리킴. 예시 구조:

```
/
├── CONTEXT-MAP.md
├── docs/adr/                  ← 프로젝트 전체 결정
├── legal/
│   ├── CONTEXT.md
│   └── docs/adr/              ← 법무 트랙 결정
├── finance/
│   ├── CONTEXT.md
│   └── docs/adr/
└── negotiation/
    ├── CONTEXT.md
    └── docs/adr/
```

확신이 안 서면 단일 컨텍스트로 시작하라고 권한다. 나중에 멀티로 마이그레이션할 수 있다.

---

### 3. Confirm and edit

다음 초안을 사용자에게 보여주고, 쓰기 전에 편집할 기회를 준다:

- `CLAUDE.md`에 추가할 `## Agent skills` 블록
- `docs/agents/todo-tracker.md`, `docs/agents/priorities.md`, `docs/agents/domain.md` 의 내용
- (단일 컨텍스트면) 빈 `CONTEXT.md` 골격
- (멀티 컨텍스트면) `CONTEXT-MAP.md` 골격 + 각 서브 컨텍스트의 `CONTEXT.md` 자리

각 파일을 한 번에 하나씩 보여주고 OK를 받는다.

### 4. Write

**파일 선택 규칙**:

- `CLAUDE.md`가 이미 존재하면 그걸 편집한다
- 없으면 사용자에게 새로 만들지 묻는다 — 임의로 만들지 말 것
- 기존 `## Agent skills` 블록이 있으면 **in-place로 업데이트**, 중복 추가 금지
- 사용자가 편집한 다른 섹션은 절대 건드리지 말 것

`CLAUDE.md`에 추가할(또는 업데이트할) 블록 형식:

```markdown
## Agent skills

### Todo / 작업 트래커

[한 줄 요약 — 어디서 관리하는지]. 자세한 규칙은 `docs/agents/todo-tracker.md` 참조.

### Priority / Status 어휘

[한 줄 요약 — 기본 어휘 또는 커스텀]. 자세한 매핑은 `docs/agents/priorities.md` 참조.

### 도메인 문서 레이아웃

[한 줄 요약 — "단일 컨텍스트" 또는 "멀티 컨텍스트"]. 자세한 규칙은 `docs/agents/domain.md` 참조.
```

그 다음 `docs/agents/` 폴더에 세 개의 docs 파일을 작성한다. 빈 골격이 아니라 사용자의 답변을 반영한 실제 내용을 채워서 쓴다. 각 파일은 아래 템플릿을 참고:

#### `docs/agents/todo-tracker.md` 템플릿

```markdown
# Todo / 작업 트래커

이 프로젝트에서 할 일은 **[Notion / 로컬 markdown / 기타]** 에서 관리한다.

## 위치

[URL / 폴더 경로 / 설명]

## 스키마 (Notion인 경우)

| 필드 | 설명 |
|---|---|
| `Project name` | 할 일 제목 |
| `Status` | Not started / In progress / Done |
| `Priority` | High / Medium / Low |
| `Start date` | 시작일 (선택) |
| `End date` | 마감일 (선택) |

## 규칙

- 새 액션 아이템이 발견되면 이 트래커에 등록한다
- 작업 시작 시 → `In progress`
- 완료 시 → `Done` + 페이지에 완료 메모
- Done 항목은 삭제하지 않는다 (히스토리)
```

#### `docs/agents/priorities.md` 템플릿

```markdown
# Priority / Status 어휘

## Priority

- **High**: 마감 임박, 다른 사람이 기다림, 딜/계약/법무 영향
- **Medium**: 중요하지만 며칠 여유 있음
- **Low**: 참고용, 나중에 검토해도 됨

## Status

- **Not started**: 아직 시작 전
- **In progress**: 현재 작업 중
- **Done**: 완료

## 프로젝트 특수 어휘

[프로젝트마다 자주 쓰이는 상태가 있으면 여기 기록. 예: "인사이트 회신대기", "실사 진행중" 등]
```

#### `docs/agents/domain.md` 템플릿

```markdown
# 도메인 문서 레이아웃

이 프로젝트는 **[단일 컨텍스트 / 멀티 컨텍스트]** 다.

## 위치

- 용어 사전: `CONTEXT.md` [또는 멀티의 경우 `CONTEXT-MAP.md`에서 매핑]
- 의사결정 기록: `docs/adr/` [또는 서브 컨텍스트별 `<sub>/docs/adr/`]
- 참조 지식: `docs/knowledge/`

## 읽기 규칙

다른 스킬이 이 프로젝트에서 작업할 때:

1. 먼저 `CONTEXT.md`를 읽어 용어를 확인한다
2. 의사결정이 필요한 사안은 `docs/adr/`에서 과거 결정을 본다
3. 인물/회사/법무 용어 등 사전성 자료는 `docs/knowledge/`에서 찾는다

## 쓰기 규칙

- 새로운 용어가 등장하면 `CONTEXT.md`에 추가 (grill-knowledge 스킬이 자동 수행)
- 되돌리기 어려운 결정은 `docs/adr/`에 ADR로 기록 (grill-knowledge 스킬이 자동 수행)
- 인물/회사 정보 등 누적성 자료는 `docs/knowledge/`에 추가
```

또한 다음 빈 골격 파일/폴더를 만든다 (없으면):

- `CONTEXT.md` — 빈 용어 사전 (아래 골격 사용)
- `docs/adr/.gitkeep` — 빈 폴더 마커
- `docs/knowledge/.gitkeep` — 빈 폴더 마커

`CONTEXT.md` 빈 골격:

```markdown
# Context

이 프로젝트에서 사용하는 용어와 개념의 사전. `grill-knowledge` 스킬을 사용하면 대화 중 발견된 새 용어가 여기 자동으로 추가된다.

## 용어

(아직 비어 있음 — 첫 grilling 세션에서 채워짐)
```

### 5. Done

세팅 완료를 사용자에게 알린다. 다음을 짧게 안내:

- 어떤 파일들이 생성/수정되었는지 목록
- `docs/agents/*.md` 는 직접 편집해도 된다는 점
- 다음에 `grill-knowledge` 스킬을 쓰면 `CONTEXT.md`와 `docs/adr/` 가 자동으로 채워진다는 점
- 새 작업이 생기면 `grill-intake`로 harness 배치를 먼저 결정하라는 점
- 트래커를 바꾸거나 처음부터 다시 하고 싶을 때만 이 스킬을 다시 실행하면 된다는 점

---

## 멀티 컨텍스트 추가 안내

사용자가 섹션 C에서 멀티 컨텍스트를 선택한 경우, `CONTEXT-MAP.md`도 생성한다:

```markdown
# Context Map

이 프로젝트는 여러 컨텍스트로 구성된다. 각 서브 컨텍스트는 자체 `CONTEXT.md`와 `docs/adr/`를 가진다.

| 컨텍스트 | 위치 | 설명 |
|---|---|---|
| [name1] | `<path>/CONTEXT.md` | [한 줄 설명] |
| [name2] | `<path>/CONTEXT.md` | [한 줄 설명] |

## 전체 프로젝트 결정

프로젝트 전체에 적용되는 결정은 루트의 `docs/adr/`에 기록한다.
```

서브 컨텍스트 폴더마다 빈 `CONTEXT.md` + `docs/adr/` 골격을 만든다.

---

## harness 모델과 연동 (grill-intake)

grill-setup이 완료된 후 새 작업이 계속 생기면:

- 새 작업 → `grill-intake`로 harness 배치 결정
- 새 harness 신설이 결정되면 → grill-setup을 해당 harness 폴더에서 다시 실행
- 기존 harness sub-epic이면 → grill-setup 불필요, grill-knowledge로 직접 박제
