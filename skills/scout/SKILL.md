---
name: scout
description: >
  Scout finds and installs Claude Code skills from local marketplaces, GitHub, and the web.
  Ask it to find any skill you need — it searches 6+ marketplaces and 70+ skills, recommends the best match,
  installs it, and runs it immediately.
  Use when: "스킬 찾아줘", "find skill", "install skill", "skill search",
  "스킬 설치", "스킬 검색", "~하는 스킬 있어?", "need a skill for",
  or when a capability is needed but not currently available.
---

# Skill Finder & Installer

사용자가 필요한 스킬을 **검색 → 추천 → 설치 → 실행**까지 자동으로 처리하는 메타 스킬.

## 동작 흐름

사용자 요청에서 키워드를 추출한 뒤 아래 4단계를 순서대로 수행한다.

---

## 1단계: 검색 (Search)

3계층 병렬 탐색을 수행한다. 우선순위: 로컬 마켓플레이스 > GitHub > 웹 검색.

### (a) 로컬 마켓플레이스 스캔 [최우선]

아래 경로를 모두 탐색한다:

| 마켓플레이스 | 경로 |
|---|---|
| awesome-claude-skills | `~/.claude/plugins/marketplaces/awesome-claude-skills/` |
| oh-my-claudecode | `~/.claude/plugins/marketplaces/omc/skills/` |
| claude-plugins-official | `~/.claude/plugins/marketplaces/claude-plugins-official/` |
| claude-code-plugins | `~/.claude/plugins/marketplaces/claude-code-plugins/` |
| claude-code-workflows | `~/.claude/plugins/marketplaces/claude-code-workflows/` |
| taskmaster | `~/.claude/plugins/marketplaces/taskmaster/` |

**탐색 방법:**

1. `~/.claude/plugins/known_marketplaces.json` 을 읽어 마켓플레이스 목록을 확인한다.
2. 각 마켓플레이스 디렉토리에서 `skills/` 하위 폴더를 Glob으로 스캔한다:
   - 패턴: `~/.claude/plugins/marketplaces/*/skills/*/SKILL.md`
   - 각 SKILL.md의 YAML frontmatter에서 `name`, `description` 필드를 파싱한다.
3. 플러그인 디렉토리도 탐색한다:
   - 패턴: `~/.claude/plugins/marketplaces/*/plugins/*/`
   - 각 플러그인의 `plugin.json`, `README.md`, 또는 하위 `skills/` 를 확인한다.
4. 이미 설치된 스킬을 확인한다:
   - 프로젝트 범위: `.omc/skills/` 디렉토리
   - 사용자 범위: `~/.claude/skills/omc-learned/` 디렉토리
   - `~/.claude/plugins/installed_plugins.json` 에서 설치된 플러그인 대조

### (b) GitHub 검색 [로컬에서 못 찾을 때]

로컬 마켓플레이스에서 충분한 결과가 없으면 GitHub를 검색한다:

1. `known_marketplaces.json`의 각 repo에서 검색:
   - WebFetch로 `https://api.github.com/search/repositories?q={키워드}+topic:claude-code-skill` 호출
2. 결과에서 스킬/플러그인 후보를 추출한다.

### (c) 웹 검색 [최후 수단]

GitHub에서도 찾지 못하면 웹 검색을 수행한다:

1. WebSearch로 `"claude code skill {키워드}"` 또는 `"claude code plugin {키워드}"` 검색
2. 검색 결과에서 GitHub 저장소나 스킬 관련 페이지를 필터링한다.

---

## 2단계: 추천 (Recommend)

### 매칭 점수 산정

각 후보 스킬에 대해 관련성 점수를 계산한다:

| 매칭 기준 | 점수 |
|---|---|
| 스킬 이름에 키워드 포함 | +10 |
| 설명(description)에 키워드 포함 | +5 |
| 태그/카테고리에 키워드 포함 | +3 |
| 이미 설치됨 | +1 (표시용) |

### 결과 표시

점수 순으로 정렬하여 테이블 형태로 표시한다:

```
검색 결과: "{키워드}"

| # | 이름 | 설명 | 출처 | 설치 상태 |
|---|------|------|------|-----------|
| 1 | pdf-processor | PDF 파일 처리 스킬 | omc | ✅ 설치됨 |
| 2 | pdf-extract | PDF 텍스트 추출 | awesome-claude-skills | ❌ 미설치 |
| 3 | document-ai | 문서 AI 처리 | GitHub | ❌ 미설치 |
```

### 사용자 선택

AskUserQuestion 도구를 사용하여 사용자에게 설치할 스킬을 선택하도록 요청한다:

```
설치할 스킬 번호를 선택하세요 (여러 개는 쉼표로 구분, 취소는 'q'):
```

- 이미 설치된 스킬을 선택하면 바로 4단계(실행)으로 이동한다.
- 결과가 없으면 "검색 결과가 없습니다. 다른 키워드로 시도해 보세요." 를 안내한다.

---

## 3단계: 설치 (Install)

출처에 따라 설치 방식이 다르다. 기본 설치 위치는 `.omc/skills/` (프로젝트 범위).

### (a) 로컬 마켓플레이스 스킬 (이미 디스크에 있음)

1. 해당 스킬의 SKILL.md 및 관련 파일을 Read 도구로 읽는다.
2. `.omc/skills/{스킬이름}/` 디렉토리를 생성한다.
3. Write 도구로 파일을 복사한다:
   - `SKILL.md` (필수)
   - 같은 디렉토리의 다른 파일들 (있으면 함께 복사)

### (b) 마켓플레이스 플러그인 (플러그인 단위)

플러그인 전체를 설치해야 하는 경우:
1. 해당 플러그인의 구조를 확인한다.
2. 필요한 스킬 파일만 추출하여 `.omc/skills/` 에 설치한다.
3. 또는 플러그인 전체 설치를 안내한다 (사용자 확인 후).

### (c) 원격 스킬 (GitHub/웹)

1. WebFetch로 raw 파일을 다운로드한다:
   - GitHub: `https://raw.githubusercontent.com/{owner}/{repo}/{branch}/skills/{name}/SKILL.md`
2. `.omc/skills/{스킬이름}/` 디렉토리에 Write 도구로 저장한다.
3. git clone이 필요한 경우 Bash로 실행한다:
   ```
   git clone --depth 1 {repo_url} /tmp/skill-install-{name}
   ```
   그 후 필요한 파일만 `.omc/skills/` 에 복사한다.

### 설치 확인

설치 완료 후 Glob으로 `.omc/skills/{스킬이름}/SKILL.md` 존재를 확인한다.

---

## 4단계: 실행 (Use)

설치 완료 후 즉시 해당 스킬을 활용한다:

### 방법 A: Skill 도구로 실행 (우선)

설치된 스킬이 Skill 도구로 호출 가능한 경우:
```
Skill 도구 호출: skill="{스킬이름}", args="{원래 사용자 요청}"
```

### 방법 B: 컨텍스트 주입 (Skill 도구 불가 시)

Skill 도구로 실행할 수 없는 경우:
1. `.omc/skills/{스킬이름}/SKILL.md` 를 Read 도구로 읽는다.
2. SKILL.md의 지시사항을 현재 컨텍스트에 적용한다.
3. 해당 지시사항에 따라 원래 사용자의 작업을 수행한다.

---

## 에러 핸들링

| 상황 | 대응 |
|---|---|
| 검색 결과 없음 | "해당 키워드로 스킬을 찾지 못했습니다. 다른 키워드를 시도하거나, 직접 스킬을 만들어 보세요." |
| 네트워크 오류 (GitHub/웹) | 로컬 마켓플레이스 결과만으로 진행. 오류 사실을 안내. |
| 중복 설치 | "이미 설치된 스킬입니다. 바로 실행하시겠습니까?" |
| 설치 실패 | 오류 원인을 안내하고 수동 설치 방법을 제시. |
| 마켓플레이스 디렉토리 없음 | 해당 마켓플레이스를 건너뛰고 나머지에서 검색. |

---

## 사용 예시

```
사용자: "PDF 스킬 찾아줘"
→ 키워드 "PDF" 추출 → 마켓플레이스 검색 → 결과 테이블 표시 → 선택 → 설치 → 실행

사용자: "코드 리뷰 스킬 필요해"
→ 키워드 "코드 리뷰", "code review" 추출 → 검색 → 추천 → 설치 → 실행

사용자: "install skill for testing"
→ 키워드 "testing" 추출 → 검색 → 추천 → 설치 → 실행

사용자: /ccskill:skill-finder PDF
→ 키워드 "PDF" → 검색 → 추천 → 설치 → 실행
```
