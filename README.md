# washswat-grill

데이비드(WASHSWAT)의 Claude Cowork용 **grill-*** 스킬 패밀리.

코딩이 아닌 일반 업무 — 딜, 법무, 운영, 리서치, PM — 프로젝트의 지식과 결정을 한 곳에 박아두기 위한 4개 스킬 묶음.

## 스킬

| 스킬 | 역할 |
|---|---|
| [`grill-setup`](./grill-setup/SKILL.md) | 프로젝트 폴더에 `CLAUDE.md`, `CONTEXT.md`, `docs/agents/`, `docs/adr/`, `docs/knowledge/` 구조를 처음 세팅 |
| [`grill-intake`](./grill-intake/SKILL.md) | 새 작업/프로젝트가 생겼을 때 — 기존 harness의 sub-epic으로 넣을지, 새 harness를 신설할지 결정 |
| [`grill-knowledge`](./grill-knowledge/SKILL.md) | 계획·결정·문서를 집요하게 인터뷰해 `CONTEXT.md`(용어), `docs/adr/`(결정), `STATUS.md`(휘발성 상태), Iron Law(강제 룰) 4곳에 박음 |
| [`grill-sync`](./grill-sync/SKILL.md) | 세션 중 중요한 사실이 바뀌었을 때 큐에 쌓지 않고 즉시 해당 knowledge 파일을 업데이트 |

## 흐름

```
grill-setup   ──▶ 프로젝트 폴더 스캐폴딩 (1회)
grill-intake  ──▶ 새 작업/프로젝트가 들어왔을 때 어디 넣을지 결정
grill-knowledge ─▶ 그 안에서 일상적으로 grilling + 4개 위치에 박음
grill-sync    ──▶ 중요한 사실이 즉시 바뀌면 큐 건너뛰고 바로 반영
```

## 설치

각 스킬을 Claude Cowork/Code의 스킬 디렉토리 (`~/.claude/skills/`) 아래에 동일한 이름의 디렉토리로 두면 된다:

```bash
git clone git@github.com:WASHSWATDAVID/washswat-grill.git
cp -R washswat-grill/grill-* ~/.claude/skills/
```

각 스킬 폴더의 `SKILL.md` 안 frontmatter(`name`, `description`)가 트리거 메타데이터다.
