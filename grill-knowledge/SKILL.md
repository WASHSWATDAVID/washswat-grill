---
name: grill-knowledge
description: "데이비드의 계획/결정/문서를 집요하게 인터뷰하면서 CONTEXT.md(용어 사전), docs/adr/(의사결정 기록), STATUS.md(휘발성 상태), Iron Law(강제 룰) 4개 위치에 박는 스킬. 두 모드로 운영 — (1) 일상 자동 트리거(MT-1~5) + 배치 모드 (2) 명시적 심층 인터뷰. \"이 계획 검증해줘\", \"결정하기 전에 점검해줘\", \"이 안건 grilling해줘\", \"스트레스 테스트해줘\", \"용어 정리해줘\", \"내가 빠뜨린 거 없는지 봐줘\", \"박제 후보 정리해줘\", \"harness 점검해줘\" 같은 요청에 반드시 이 스킬을 사용할 것. grill-* 패밀리: grill-setup(폴더 초기화), grill-intake(harness 배치 결정), grill-sync(즉시 사실 업데이트)."
---

# Grill Knowledge

데이비드의 계획·결정·문서를 끝장날 때까지 인터뷰하고, 그 자리에서 4개 위치에 박는다.

**핵심 위치** (grill-setup이 스캐폴딩하는 4개 위치와 동일):
- `CONTEXT.md` — 시간 안 타는 도메인 사실, 용어 사전
- `docs/adr/` — 결정성 변경 (세 조건 통과 시만)
- `STATUS.md` — 휘발성 상태 (날짜 박힘)
- `CLAUDE.md`의 `## Iron Laws` 섹션 — 강제 룰 (1~2주 검증 후 승격)

**히스토리를 쌓는 프로젝트면 5번째 위치**: grill-setup에서 "히스토리를 쌓는다"고 정한 경우, 사건·지표·결정의 누적 기록은 그때 고른 위치(루트 `history/<YYYY-MM>.md` / 서브 그룹 안 `<group>/history/` / `docs/knowledge/` 밑)에 월별로 append한다. 어디인지는 `docs/agents/domain.md`에 적혀 있다.

**질문은 한 번에 하나만** 한다. 사용자의 답을 받은 뒤 다음 질문으로 넘어간다. 절대 한꺼번에 던지지 말 것.

**쉬운말 원칙** (grill-setup과 동일): 데이비드에게 물을 때 파일명(`campaign-history.md`)·영어 약어(ROAS, ADR)·내부 용어(컨텍스트/ingest/harness)를 그대로 쓰지 말고 풀어서 말한다. 파일명·필드명은 내부적으로만 쓴다.

질문이 기존 문서·노션·메일에서 답을 얻을 수 있는 거면, 사용자에게 묻지 말고 직접 탐색한다.

---

## grill-* 패밀리 연동

이 스킬은 다음 확장 스킬들과 함께 동작한다:

| 스킬 | 역할 | 언제 호출 |
|---|---|---|
| `grill-setup` | 폴더 구조 초기화 | 새 프로젝트 시작, 구조 없을 때 |
| `grill-intake` | 신규 작업/프로젝트 harness 배치 결정 | "이거 어디 넣지?" 상황 |
| `grill-sync` | 중요 사실 변경 즉시 반영 | MT-5 또는 고중요도 MT-2 감지 시 자동 위임 |

**위임 규칙**: 아래 "즉시 인터럽트 조건"에 해당하면 큐에 쌓지 말고 `grill-sync`로 즉시 위임한다.

---

## 세션 시작 자동 점검

세션 시작 시 (새 대화가 열리거나 첫 번째 harness 작업 시작 시):

1. `docs/agents/domain.md` 확인 — grill-setup이 정한 도메인·그룹 축·히스토리 위치·데이터 소스를 먼저 읽어 이 프로젝트 구조를 파악한다 (없으면 grill-setup 미실행 — 패턴 인식으로 대체).
2. `comms/_archive/_pending_capture_*.md` 또는 `.pending/_capture_*.md` 존재 여부 확인
3. 있으면 즉시 펼침 제안: "이전 세션에서 미처리된 박제 후보 N건이 있습니다. 지금 처리할까요?"
4. STATUS.md의 "미해결 사항" 확인 — cross-cutting 이슈가 있으면 언급
5. `domain.md`의 `## 데이터 소스`에 "초기 ingest 예정: [소스]"가 남아 있으면 제안: "셋업 때 미뤄둔 [소스] 자료 가져오기가 있습니다. 지금 가져올까요?" (수락 시 grill-setup 섹션 E의 ingest 절차로 실행)

---

## 두 운영 모드

### 모드 1 — 일상 자동 트리거 (배치 모드)

데이비드가 명시적으로 호출하지 않아도, **매 turn 입력을 5개 트리거(MT-1~5)로 자체 점검**하고 박제 후보를 누적 큐에 쌓는다. 작업 자연 종료 시점에 한 번에 펼친다.

#### 5개 트리거 — 감지 패턴 포함

| 트리거 | 감지 패턴 (예시) | 박제 위치 | 우선순위 |
|---|---|---|---|
| **MT-1 결정** | "합의했어", "확정", "결정됨", "이렇게 가자", "앞으로는", "기준으로", "X로 가기로", "~하기로 했어" | `docs/adr/` (세 조건 게이트 통과 시) | 고중요도 → grill-sync 위임 |
| **MT-2 도메인 사실** | 새 인물/조직/용어 등장 (기존 CONTEXT.md에 없음), 외부 자료 정독 후 새 사실, 기존 사실과 모순되는 발언 | `CONTEXT.md` 또는 `knowledge/` | 모순 발생 시 → grill-sync 위임 |
| **MT-3 상태 갱신** | 숫자 변동 (단가/주식수/금액/일자), "X 완료", "Y 단계 진입", "~로 바뀌었어" | `STATUS.md` | 일반 → 큐 누적 |
| **MT-4 룰 후보** | "절대", "반드시", "금지", "...하면 안 돼", "앞으로는 항상", "이건 룰이야" | ADR 초안 → 1~2주 검증 후 Iron Law 승격 | 일반 → 큐 누적 |
| **MT-5 per-holder** (shareholders 전용) | 주주 이름 + "참여", "불참", "철회", "바꿨어", 금액 변동 | per-holder/<주주>.md + 매트릭스 + STATUS.md 3곳 | **항상 → grill-sync 즉시 위임** |

#### 즉시 인터럽트 조건 (큐 대신 grill-sync로 위임)

다음 조건 중 하나라도 해당하면 **큐에 쌓지 말고 즉시 grill-sync를 발동**한다:

- **MT-5 전부**: 주주 입장 변경은 항상 즉시
- **MT-2 모순**: 기존 CONTEXT.md 내용과 직접 충돌하는 발언
- **MT-2 금액/지분 변동**: 숫자가 명시된 중요 사실 변경
- **MT-1 번복**: 이전에 결정된 사항이 뒤집히는 경우

#### 큐 운영

매 turn 트리거 점검 → 즉시 인터럽트 조건 아니면 큐에 추가 (즉시 출력 안 함, 토큰 보존).

**펼침 신호**:
- 명시적 종료: "끝", "정리", "오늘 그만", "마무리", "이상", "다음 작업"
- 명시적 호출: "박제 후보 정리해줘", "grill-knowledge 돌려", "오늘 정리해줘"

**펼침 시 표준 출력**:

```
오늘 작업 중 N건을 박제 후보로 식별했습니다.

| # | 트리거 | 후보 | 위치 |
|---|---|---|---|
| 1 | MT-1 (결정) | ES Investor 평균가 2,155원 합의 | shareholders/docs/adr/0007-... |
| 2 | MT-3 (상태) | WWG 마진 +297,923주 | shareholders/STATUS.md + 루트 STATUS.md |
| 3 | MT-4 (룰 후보) | "권도균 그룹은 100원 단위로 깎이지 않는다" | shareholders/CLAUDE.md (Iron Law 승격은 1~2주 후) |

처리 옵션:
- 일괄 박제 (모두 진행)
- 선택 박제 (번호 명시: "1,3만")
- 모두 보류 (다음 세션으로)
```

**큐 휘발 방지**: 세션 종료 시 큐가 비어있지 않으면 `comms/_archive/_pending_capture_YYYYMMDD.md`로 임시 저장. 다음 세션 시작 시 Claude가 이 파일 존재 여부를 점검 → 있으면 큐 복원 후 펼침 제안.

### 모드 2 — 명시적 심층 인터뷰

데이비드가 명시 호출 ("grilling해줘", "이 계획 검증해줘", "X harness 점검해줘") 시 발동. 전체 harness 또는 특정 주제에 대해 끝장날 때까지 인터뷰.

심층 인터뷰는 모드 1의 배치 모드와 달리 **즉시 인터뷰 진행** + 발견 즉시 박제 (큐 누적 안 함).

---

## Domain awareness — 어디를 봐야 하는가

인터뷰 시작 전 다음을 탐색해서 도메인을 파악한다.

### 0순위 — `docs/agents/domain.md`부터 읽기

grill-setup을 거친 프로젝트는 `docs/agents/domain.md`에 도메인 종류·그룹 축(단일/트랙별/채널별/시간별)·히스토리 위치·데이터 소스가 이미 박혀 있다. **패턴을 추측하기 전에 이 파일을 먼저 읽어** 구조를 확정한다. 있으면 아래 패턴 인식은 생략 가능. 없으면 grill-setup 미실행으로 보고 패턴 인식으로 넘어간다.

### 파일 구조 — 3가지 패턴 인식

#### 패턴 A: 단일 컨텍스트 (소규모 프로젝트)

```
/
├── CLAUDE.md
├── CONTEXT.md
├── STATUS.md
├── docs/
│   ├── agents/
│   ├── adr/
│   └── knowledge/
```

#### 패턴 B: 멀티 컨텍스트 (CONTEXT-MAP.md 기반, legacy)

```
/
├── CONTEXT-MAP.md     ← 각 서브 컨텍스트 위치 명시
├── legal/CONTEXT.md
├── finance/CONTEXT.md
```

작업 주제가 어느 서브 컨텍스트에 속하는지 판단하고 해당 폴더의 CONTEXT.md/docs/adr/를 본다.

#### 패턴 C: 비대칭 harness 모델 (wwg-deal, 신규)

```
/
├── CLAUDE.md           ← 루트 운영 규칙 + harness 인덱스
├── STATUS.md           ← 전사 dashboard
├── knowledge/          ← 전사 도메인 사전
├── docs/agents/        ← 전사 운영 규칙
└── harnesses/
    ├── <트랙1>/
    │   ├── CLAUDE.md   ← 트랙 운영 + Iron Law + MT 트리거
    │   ├── CONTEXT.md
    │   ├── STATUS.md
    │   └── docs/adr/
    └── <트랙2>/...
```

harness 모델에서는 **루트 CLAUDE.md 먼저 → harness 인덱스에서 해당 트랙 식별 → 그 harness의 CLAUDE.md/CONTEXT.md/STATUS.md** 순으로 탐색.

shareholders harness만의 특수성: `per-holder/<주주>.md` + `CONTEXT.md` 마스터 매트릭스 + `STATUS.md` 3곳 동기화 필수 (IL-3).

### 파일이 없으면 — 게으르게 만든다

`CONTEXT.md`가 없으면 **첫 번째 용어가 해결되는 순간** 만든다. 미리 빈 파일을 만들지 말 것.
`docs/adr/`가 없으면 **첫 번째 ADR이 필요한 순간** 만든다.
`STATUS.md`가 없으면 **첫 번째 휘발성 상태가 식별되는 순간** 만든다.

### 외부 소스 — Notion, 메일, Slack

코딩 프로젝트와 달리 데이비드의 일반 업무 컨텍스트는 외부 소스에도 분산돼 있다. 인터뷰 중 사용자가 어떤 사람·딜·사안을 언급하면:

- Notion에 관련 페이지가 있는지 확인 (Todo, 회의록, 문서)
- 최근 메일·Slack에 관련 스레드가 있는지 확인
- 프로젝트의 `docs/knowledge/` 또는 `knowledge/`에 인물·회사 사전이 있는지 확인
- harness 모델이면 해당 harness의 `comms/` 인덱스 확인

확인한 정보를 인터뷰 답변에 녹여 사용자에게 다시 제시한다 — "노션의 X 페이지에서 보니 Y라고 돼 있는데, 지금 말한 것과 다른 것 같다. 어느 쪽이 맞나?"

---

## 위치 결정 트리 (Claude 자동 판정)

박제할 정보가 어디로 가는지 자동 분류. 애매할 때만 묻는다.

```
사실/사건의 성격:
├─ 시간 안 타는 도메인 사실?
│   ├─ 한 harness/컨텍스트 전용 → harness/CONTEXT.md
│   └─ cross-harness → 루트 knowledge/<파일>.md
│
├─ 결정성 변경 (세 조건 게이트 통과)?
│   └─ harness/docs/adr/XXXX-제목.md
│   * 세 조건 중 하나라도 빠지면 → STATUS 또는 CONTEXT로 강등
│
├─ 휘발성 상태 (날짜 박힘)?
│   ├─ 한 harness 상태 → harness/STATUS.md
│   └─ cross-cutting → 루트 STATUS.md
│
├─ 지나간 사건/지표 기록 (히스토리를 쌓는 프로젝트)?
│   └─ domain.md에 적힌 히스토리 위치의 해당 월 파일에 append
│       (루트 history/<YYYY-MM>.md / <group>/history/<YYYY-MM>.md / docs/knowledge/history*)
│   * STATUS.md(지금 상태)와 구분: 히스토리는 "그때 있었던 일"을 날짜별로 누적, 과거 항목은 보존
│
├─ 룰 후보 (반복 가능한 강제 규칙)?
│   ├─ 1회성 표명 → ADR 초안만
│   └─ 2회 이상 반복 또는 위반 발생 → Iron Laws 승격
│
└─ 주주별 정보 (shareholders harness 전용)?
    └─ per-holder/<주주>.md + CONTEXT.md 매트릭스 + STATUS.md 3곳
```

**Claude가 묻는 케이스**: 두 harness 걸침, 사실/결정 모호, 새 harness 신설 후보 → 이 경우 `grill-intake` 호출 제안.

---

## During the session — 인터뷰 동안 무엇을 하는가

### 1. 용어 사전과 충돌 검증

사용자가 쓴 용어가 `CONTEXT.md`의 정의와 충돌하면 **즉시** 지적한다.

> "사전에는 '클로징'이 X로 정의돼 있는데, 방금 Y의 의미로 쓰신 것 같다. 어느 쪽이 맞나? 사전을 업데이트할까, 다른 단어를 쓸까?"

### 2. 모호한 언어 날카롭게 다듬기

사용자가 모호하거나 의미가 겹치는 단어를 쓰면 정밀한 단어를 제안한다.

> "'담당자'라고 하셨는데, 의사결정자(decision maker)인지 실무자(operator)인지? 둘은 다른 사람이다."

### 3. 구체적 시나리오로 압박

도메인 관계를 논할 때는 구체적 시나리오로 경계를 강제로 명확화한다.

> "WWG가 빠지면 어떻게 되나? 그래도 딜이 진행되나, 전체가 무산되나?"

### 4. 기존 문서·외부 소스와 교차 참조

사용자 진술이 기존 문서·노션·메일과 일치하는지 확인한다. 모순이 있으면 표면에 드러낸다.

> "방금 'X가 5월 15일에 회신'이라 하셨는데, 노션 페이지엔 5월 18일로 돼 있다. 어느 쪽이 맞나?"

### 5. 그 자리에서 박제 (즉시 기록, 배치 안 함 — 심층 인터뷰 모드에서)

용어·결정·상태가 해결되는 순간 **바로** 해당 위치를 업데이트한다. 자동 트리거 모드에서는 큐에만 쌓고 (즉시 인터럽트 조건 제외), 심층 인터뷰 모드에서는 즉시 박는다.

#### 박제 위치 가이드

- **CONTEXT.md**: 도메인 전문가에게 의미 있는 용어·사실만. 구현 디테일·파일 위치 등은 `docs/agents/` 또는 `docs/knowledge/`로.
- **STATUS.md**: 날짜 박힌 휘발성 — "오늘 X 완료", "이번 주 Y 진행", "현재 마진 +N주" 같이 다음 주면 바뀔 정보 (지금 상태).
- **history 파일** (히스토리를 쌓는 프로젝트): 지나간 사건·지표·결정의 누적 기록. domain.md의 히스토리 위치에서 해당 월 파일에 append, 과거 항목 보존. STATUS는 "지금", history는 "그때"로 구분.
- **docs/adr/**: 세 조건 게이트 통과한 결정만. 작은 결정·명백한 결정·임시 결정은 노션·메모로.
- **Iron Laws (CLAUDE.md `## Iron Laws`)**: 1회성 룰은 안 박음. 2회 이상 반복 또는 위반 발생 시 승격.

> **STATUS.md 섹션 정합 (grill-setup 골격)**: grill-setup이 만든 STATUS.md는 `목표 / 핵심 마감 / 현재 상태 / 다음 액션` 섹션을 가진다. 휘발성 갱신은 `현재 상태` / `다음 액션`에, cross-cutting 이슈는 `미해결 사항`에 박는다. `미해결 사항` 섹션이 없으면 만들어서 박는다.

### 6. ADR 세 조건 게이트

다음 셋이 **모두** 참일 때만 ADR 작성을 제안:

1. **되돌리기 어려움** — 나중에 마음 바꾸는 비용이 크다
2. **컨텍스트 없으면 놀라울 일** — 미래의 누군가가 "왜 이렇게 했지?" 의아해할 것
3. **진짜 트레이드오프의 결과** — 진짜 대안이 있었고, 특정 이유로 한쪽을 골랐다

셋 중 하나라도 빠지면 ADR 안 쓴다. 강등 매핑:
- 트레이드오프 없이 명백 → CONTEXT.md "핵심 사실"로 강등
- 되돌리기 쉬움 → STATUS.md "이번에 X로 결정"으로 강등
- 컨텍스트 없어도 자명 → 박제 안 함

### 7. Iron Law 승격 룰

ADR로 박힌 결정 중 "**반드시·절대·금지**" 표현이 있고 다음 중 하나면 Iron Law 승격 검토:

- 같은 룰이 2회 이상 반복 언급됨
- 룰을 어겼을 때 실제 손해 발생한 위반 사례 있음
- 사용자가 명시적으로 "이건 Iron Law로"라고 지명

승격 시 해당 harness `CLAUDE.md`의 `## Iron Laws` 섹션에 표준 형식으로 박음 + 트리거·체크 질문·위반 시 액션 묶음으로 정의.

### 8. per-holder 동기화 강제 (shareholders 전용, IL-3)

shareholders harness 작업 중 per-holder 갱신 트리거 발생 시 → **즉시 grill-sync 위임** (3곳 동기화 강제):

1. `harnesses/shareholders/per-holder/<주주>.md` — 표준 6개 섹션 포맷
2. `harnesses/shareholders/CONTEXT.md` 마스터 매트릭스 해당 행
3. `harnesses/shareholders/STATUS.md` "구주 확보 현황" 합계

3곳 중 한 곳이라도 누락되면 IL-3 위반 — confirm 받고 진행, 위반 사례 기록.

WWG 최대주주 마진 자동 재계산: 갱신 후 WWG 총 취득 - UTC 합산 = 마진 출력. 마진 +100,000주 미만 시 ⚠️ 경고 + 루트 STATUS.md "미해결 사항"에 자동 추가.

---

## 인터뷰가 끝났을 때

세션 마지막에 다음을 보고한다:

- 추가/수정된 용어 (CONTEXT.md, 몇 개 / 어떤 것)
- 작성된 ADR (몇 개 / 어떤 것)
- 갱신된 STATUS.md 항목
- 승격된 Iron Law (있으면)
- per-holder 동기화 (shareholders 작업 시)
- 미해결로 남긴 질문 (있으면)
- 외부 액션 아이템 — Notion Todo 후보가 있으면 명시

미해결 질문이 있으면 사용자에게 어떻게 처리할지 묻는다:
- 지금 답하고 끝낸다
- 다음 세션으로 미룬다 (이 경우 `docs/adr/draft-<주제>.md` 또는 `comms/_archive/_pending_capture_YYYYMMDD.md`에 임시 저장)
- 그냥 두고 잊는다

---

## harness 모델 특수 가이드

### 어느 harness에 박을지 판단

작업 중인 주제가 어느 harness에 속하는지 먼저 식별. 애매하면 사용자에게 묻는다.

> "이게 wwg-investment 트랙 결정인가, shareholders 트랙 결정인가? 어느 harness의 CONTEXT.md를 업데이트해야 할지 확인하고 싶다."

여러 harness에 걸치는 결정은:
- cross-cutting 사실 → 루트 `knowledge/`
- cross-cutting 상태 → 루트 `STATUS.md` "미해결 사항"
- cross-cutting 결정 → 영향받는 모든 harness의 `docs/adr/`에 cross-reference로 박음 (또는 가장 핵심 harness에 본 ADR + 다른 harness에서 링크)

**새 작업의 harness 귀속이 불명확하면** → `grill-intake` 스킬 호출 제안.

### Iron Law Enforcement와의 정합

grill-knowledge로 박은 결정·룰이 Iron Law로 승격될 때:

1. 해당 harness `CLAUDE.md`의 `## Iron Laws` 섹션에 표준 형식으로 박음
2. 근거 ADR 명시
3. 트리거 / 체크 질문 / 위반 시 액션 정의
4. 위반 사례 누적 표 비워두기

승격 후에는 일상 작업 시 해당 트리거가 발동되면 Claude가 자동으로 AskUserQuestion 발동 — grill-knowledge가 박은 룰이 enforce되는 메커니즘.

### 큐 페어링

배치 모드 큐와 grill-knowledge 심층 인터뷰의 차이:

| 구분 | 배치 모드 | 심층 인터뷰 |
|---|---|---|
| 발동 | 트리거 자동 (MT-1~5) | 사용자 명시 호출 |
| 박제 시점 | 펼침 신호 후 일괄 | 발견 즉시 |
| 깊이 | 트리거 후보만 표면 | 끝장날 때까지 파고듦 |
| 범위 | 그 turn 입력에 한정 | 전체 harness 또는 주제 |
| 사용 빈도 | 매일 | 주 1회 또는 마일스톤 직전 |

운영 패턴: **일상은 배치 모드, 주 1회 또는 큰 결정 전엔 심층 인터뷰.**
