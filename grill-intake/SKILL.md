---
name: grill-intake
description: "새 작업/프로젝트가 생겼을 때 harness 배치를 결정하는 스킬. \"이거 어디 넣지?\", \"새 프로젝트 생겼어\", \"이 작업 어느 harness야?\", \"sub-epic으로 넣어야 하나 새 harness 만들어야 하나\" 같은 요청에 반드시 이 스킬을 사용할 것. grill-* 패밀리: grill-setup(폴더 초기화), grill-knowledge(심층 인터뷰), grill-sync(즉시 사실 업데이트)."
---

# Grill Intake

새 작업 또는 프로젝트가 생겼을 때, **기존 그룹에 묶을지 vs. 새 그룹(harness)을 만들지** 결정하는 스킬.

질문은 한 번에 하나만 한다. 사용자의 답을 받은 뒤 다음으로 넘어간다.

**쉬운말 원칙** (grill-* 패밀리 공통): 데이비드에게 물을 때 "harness / sub-epic / Iron Laws / 카운터파티" 같은 내부 용어를 그대로 쓰지 말고 풀어서 말한다 — "기존 그룹 안에 넣을까요, 새 그룹으로 뺄까요?", "꼭 지켜야 할 별도 규칙이 필요한가요?" 등. 내부 용어는 파일·구조 결정에만 쓴다.

---

## Process

### 1. 루트 CLAUDE.md 스캔

작업 시작 전 루트 `CLAUDE.md`를 읽어 현재 harness 인덱스를 파악한다.

- 어떤 harness가 있는가?
- 각 harness의 책임 영역은?
- 현재 sub-epic 구조가 있는 harness는?

파일이 없으면: "루트 CLAUDE.md가 없습니다. grill-setup으로 기본 구조를 먼저 만드는 것을 권장합니다."

### 2. 새 작업 설명 받기

사용자에게 새 작업/프로젝트를 한두 문장으로 설명해달라고 요청한다.

> "어떤 작업인지 한두 문장으로 설명해주세요. (예: 어떤 카운터파티와, 어떤 목적으로, 언제까지)"

### 3. 배치 결정 질문 (최대 4개, 답 받은 뒤 순차 진행)

#### Q1. 기존 harness 책임과 겹치는가?

스캔한 harness 인덱스를 제시하고:

> "이 작업이 아래 harness 중 어느 것의 책임 영역과 가장 가깝나요?
> [harness 목록]
> 없으면 '없음'이라고 해주세요."

- 하나가 명확히 겹침 → Q2로 이동
- 없음 또는 두 개 이상 걸침 → 새 harness 신설 후보로 분류, Q3으로 이동

#### Q2. (기존 harness 겹침 시) 독립 운영이 필요한가?

> "이 작업이 [겹치는 그룹]의 범위 안에서 처리되나요, 아니면 별도의 상대방·계약·꼭 지켜야 할 규칙(Iron Laws)이 필요한가요?"

- 범위 안에서 처리 → sub-epic 추천
- 독립 필요 → 새 harness 신설 추천

#### Q3. (새 harness 후보 시) 지속성 확인

> "이 작업은 일회성인가요, 아니면 앞으로 계속 쌓이는 트랙인가요?"

- 일회성 → 기존 harness 중 가장 가까운 곳에 임시 sub-epic
- 지속성 있음 → 새 harness 신설 확정

#### Q4. (새 harness 신설 확정 시) 이름 결정

> "이 harness의 이름을 뭐로 할까요? (예: `dd-lawl`, `wwg-rfi`, `pr-comms`) 짧고 기억하기 쉬운 영문 소문자 권장."

---

## 4. 배치 추천 출력

질문이 끝나면 명확한 추천을 출력한다.

### Case A: 기존 harness sub-epic

```
📌 배치 추천: [harness명] / sub-epic

이유: [한두 문장]

다음 액션:
1. [harness]/CLAUDE.md 에 sub-epic 항목 추가
2. 필요 시 [harness]/CONTEXT.md 에 관련 용어 추가
3. [harness]/STATUS.md 에 진행 상태 초기화

지금 바로 추가할까요?
```

### Case B: 새 harness 신설

```
📌 배치 추천: 새 harness 신설 → harnesses/[이름]/

이유: [한두 문장]

다음 액션:
1. harnesses/[이름]/ 폴더 생성
2. **grill-setup으로 전체 구조 초기화** (CLAUDE.md의 `## Agent skills`/`## Iron Laws`, CONTEXT.md, STATUS.md, docs/agents/·adr/·knowledge/, 도메인 레시피 파일 등 — 개별 나열 대신 grill-setup이 일괄 생성)
3. 루트 CLAUDE.md Harness 인덱스에 추가

grill-setup으로 폴더 구조를 자동 생성할까요?
```

> 초기화 파일 목록은 grill-setup이 단일 출처(source of truth)다. 여기서 일부만 나열해 grill-setup과 어긋나지 않게, 항상 grill-setup에 위임한다.

---

## 5. 실행

사용자가 확인하면:

**Case A (sub-epic 추가):**
- 해당 harness의 `CLAUDE.md`를 열어 sub-epic 섹션에 추가
- 추가 형식:

```markdown
### [sub-epic 이름]
| 책임 | [한 줄 설명] |
| 상태 | 시작 전 |
| 시작일 | YYYY-MM-DD |
```

**Case B (새 harness 신설):**
- `grill-setup` 스킬로 위임하여 폴더 구조 생성
- 생성 후 루트 `CLAUDE.md`의 Harness 인덱스 테이블에 새 행 추가:

```markdown
| `[이름]/` | [책임 한 줄] | `harnesses/[이름]/CLAUDE.md` |
```

- 루트 `STATUS.md`의 전사 dashboard에 새 harness 행 추가

---

## 6. grill-knowledge 핸드오프

배치가 결정되면:

> "배치 완료. 이 작업의 핵심 용어나 결정 사항을 초기 정리할까요? (grill-knowledge 심층 인터뷰 시작)"

사용자가 원하면 grill-knowledge 모드 2(심층 인터뷰)로 전환. 새 harness를 grill-setup으로 만든 경우, grill-knowledge는 `docs/agents/domain.md`(도메인·그룹 축·히스토리 위치·데이터 소스)를 먼저 읽고, 셋업 때 미뤄둔 "초기 ingest 예정"이 있으면 실행을 제안한다.

---

## 결정 매트릭스 (Claude 내부 판단 기준)

| 조건 | 추천 |
|---|---|
| 기존 harness 책임과 명확히 겹침 + 독립 Iron Laws 불필요 + 일회성 아님 | 기존 harness sub-epic |
| 기존 harness 책임과 명확히 겹침 + 독립 Iron Laws 불필요 + 일회성 | 기존 harness 임시 sub-epic (만료 일자 명시) |
| 기존 harness 없음 또는 두 개 이상 걸침 | 새 harness 신설 |
| 기존 harness 겹침 + 독립 카운터파티/계약 존재 | 새 harness 신설 |
| 기존 harness 겹침 + 독립 Iron Laws 필요 | 새 harness 신설 |
| 규모가 매우 작고 단발성 (3회 이하 작업) | 기존 가장 가까운 harness의 임시 항목 |
