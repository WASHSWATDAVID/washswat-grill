# washswat-grill

데이비드(WASHSWAT)의 Claude Cowork용 **grill-*** 스킬 패밀리.

코딩이 아닌 일반 업무 — 딜, 법무, 운영, 리서치, PM — 프로젝트의 지식과 결정을 한 곳에 박아두기 위한 4개 스킬 묶음.

## 스킬

| 스킬 | 역할 |
|---|---|
| [`grill-setup`](./grill-setup/SKILL.md) | 프로젝트 폴더에 `CLAUDE.md`, `CONTEXT.md`, `STATUS.md`, `docs/agents/`, `docs/adr/`, `docs/knowledge/` 구조를 처음 세팅 |
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

## 설치 (Claude Cowork 사용자)

리포를 hidden 폴더로 두고, `~/.claude/skills/grill-*` 4개를 symlink로 건다:

```bash
# 1. 리포를 ~/.claude/skills/ 안 hidden 폴더에 clone
git clone git@github.com:WASHSWATDAVID/washswat-grill.git \
  ~/.claude/skills/.washswat-grill-repo

# 2. 4개 스킬 디렉토리를 symlink로 걸기
cd ~/.claude/skills
for s in grill-intake grill-knowledge grill-setup grill-sync; do
  ln -s .washswat-grill-repo/$s $s
done
```

각 스킬 폴더의 `SKILL.md` 안 frontmatter(`name`, `description`)가 트리거 메타데이터다.

## 변경사항 push 워크플로

> **중요한 함정**: Cowork UI로 스킬을 편집하면 `~/.claude/skills/` 가 아니라 **Cowork 자체 plugin 캐시**에 저장된다 (`~/Library/Application Support/Claude/local-agent-mode-sessions/skills-plugin/<HASH>/<HASH>/skills/grill-*`). 위에서 건 symlink는 우회된다.

이 갭을 메우는 `sync-grill` 스크립트가 [`bin/sync-grill`](./bin/sync-grill) 에 있다. 동작:

1. Cowork 플러그인 루트 아래에서 `grill-setup/SKILL.md` 가 가장 최근에 수정된 경로를 찾아 캐시 위치를 추출 (세션마다 해시가 바뀌므로 동적으로 탐지)
2. 4개 grill-* 디렉토리를 캐시 → 리포로 rsync (`--delete` 없이 — 부분 쓰기 보호)
3. 변경분이 있으면 자동으로 `git add -A && git commit && git push`

### 설치

```bash
# ~/bin/ 이 PATH에 있다고 가정
cp bin/sync-grill ~/bin/sync-grill
chmod +x ~/bin/sync-grill
```

### 사용

```bash
sync-grill                                  # 자동 커밋 메시지로 push
sync-grill "tweak grill-knowledge MT-3"     # 메시지 지정
sync-grill --dry-run                        # diff만 보고 commit/push 안 함
```

Cowork에서 스킬 편집한 뒤 터미널에서 `sync-grill` 한 줄이면 끝.
