# skill-scout

> 필요한 Claude Code 스킬을 검색, 설치, 실행까지 한 번에.

[English](README.md)

**skill-scout**는 Claude Code 플러그인 생태계에서 필요한 스킬을 자동으로 찾아 설치하고 바로 실행해주는 메타 스킬입니다.

6개 마켓플레이스, 70개 이상의 스킬을 검색하고, GitHub와 웹까지 탐색합니다.

## 왜 필요한가?

Claude Code에는 다양한 마켓플레이스와 스킬이 존재하지만, 사용자가 직접 탐색하고 설치해야 하는 번거로움이 있습니다. skill-scout는 이 과정을 한 단계로 줄여줍니다.

```
"PDF 스킬 찾아줘" → 검색 → 추천 → 설치 → 실행
```

## 동작 방식

```
사용자 요청
    │
    ▼
┌─────────────────────────────────┐
│  1. 검색 (3계층 병렬 탐색)       │
│  ├─ 로컬 마켓플레이스 (최우선)    │
│  ├─ GitHub (보조)               │
│  └─ 웹 검색 (최후 수단)          │
└──────────────┬──────────────────┘
               │
    ▼
┌─────────────────────────────────┐
│  2. 추천                        │
│  - 관련성 점수 기반 정렬          │
│  - 대화형 선택 테이블            │
└──────────────┬──────────────────┘
               │
    ▼
┌─────────────────────────────────┐
│  3. 설치                        │
│  - 로컬 복사 / git clone /       │
│    웹 다운로드 → .omc/skills/    │
└──────────────┬──────────────────┘
               │
    ▼
┌─────────────────────────────────┐
│  4. 실행                        │
│  - 설치된 스킬 자동 실행          │
└─────────────────────────────────┘
```

## 지원 소스

| 소스 | 방식 |
|---|---|
| [awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | 로컬 스캔 |
| [oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) | 로컬 스캔 |
| [claude-plugins-official](https://github.com/anthropics/claude-plugins-official) | 로컬 스캔 |
| [claude-code-plugins](https://github.com/anthropics/claude-code) | 로컬 스캔 |
| [claude-code-workflows](https://github.com/wshobson/agents) | 로컬 스캔 |
| [taskmaster](https://github.com/eyaltoledano/claude-task-master) | 로컬 스캔 |
| GitHub 저장소 | API 검색 |
| 웹 | 웹 검색 |

## 설치

```bash
claude plugin marketplace add ez2k/skill-scout
claude plugin install skill-scout
```

## 사용법

### 슬래시 명령

```
/skill-scout:scout PDF
/skill-scout:scout 코드 리뷰
```

### 자연어

```
"PDF 스킬 찾아줘"
"코드 리뷰 스킬 필요해"
"테스트용 스킬 있어?"
"DB 마이그레이션 스킬 설치해줘"
```

## 프로젝트 구조

```
skill-scout/
├── .claude-plugin/
│   └── plugin.json          # 플러그인 매니페스트
└── skills/
    └── scout/
        └── SKILL.md         # 스킬 정의
```

## 라이선스

MIT
