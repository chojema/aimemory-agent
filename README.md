# AIMemory Agent Template

`AIMemory Agent Template`은 Codex, Claude Code, Antigravity CLI(구 Gemini CLI), OpenCode 같은 여러 코딩 에이전트가 같은 프로젝트 폴더에서 작업 맥락을 공유하고, 작업을 이어받을 수 있도록 돕는 파일 기반 협업 메모리 템플릿입니다.

이 템플릿은 별도의 서버, 데이터베이스, 플러그인 없이 프로젝트 폴더 안의 Markdown 파일만으로 작동합니다.

## 핵심 개념

여러 에이전트를 번갈아 사용하다 보면 다음 문제가 자주 생깁니다.

- 이전 에이전트가 무엇을 했는지 새 에이전트가 모름
- 같은 프로젝트를 다시 분석하느라 컨텍스트를 많이 사용함
- 토큰 한도에 도달하면 작업 상태가 끊김
- Codex, Claude, Antigravity(Gemini), OpenCode 사이에서 수동으로 설명을 복사해 붙여넣어야 함
- 작업 중간 결정, 변경 파일, 남은 작업이 흩어짐

`AIMemory`는 이 문제를 줄이기 위해 프로젝트 안에 공통 작업 기억을 둡니다.

```text
AIMemory/
├─ PROTOCOL.md
├─ INDEX.md
├─ PROJECT_OVERVIEW.md
├─ work.log
├─ archive/
├─ cold/
└─ handoffs/
```

에이전트는 작업 전에 `AIMemory`를 읽고, 작업 후 `work.log`에 기록하며, 다른 에이전트가 이어받아야 할 때 `handoffs/`에 인계 파일을 남깁니다.

## 포함된 파일 구조

```text
aimemory-agent-template/
├─ aimemory-agent-setup.md
├─ AGENTS.md
├─ CLAUDE.md
├─ GEMINI.md
├─ opencode.md
└─ AIMemory/
   ├─ PROTOCOL.md
   ├─ INDEX.md
   ├─ PROJECT_OVERVIEW.md
   ├─ work.log
   ├─ archive/
   │  └─ .gitkeep
   ├─ cold/
   │  └─ .gitkeep
   └─ handoffs/
      └─ .gitkeep
```

## 각 파일의 역할

| 파일 또는 폴더 | 역할 |
|---|---|
| `aimemory-agent-setup.md` | 기존 프로젝트에 AIMemory 구조를 안전하게 병합 설치하기 위한 설치 프롬프트 |
| `AGENTS.md` | Codex와 AGENTS 호환 에이전트가 읽는 프로젝트 지침 파일 |
| `CLAUDE.md` | Claude Code가 읽는 프로젝트 지침 파일 |
| `GEMINI.md` | Antigravity CLI가 읽는 프로젝트 지침 파일. Gemini CLI와 동일 |
| `opencode.md` | OpenCode가 읽는 프로젝트 지침 파일 |
| `AIMemory/PROTOCOL.md` | 모든 에이전트가 따를 공통 작업 규칙 |
| `AIMemory/PROJECT_OVERVIEW.md` | 새 에이전트가 프로젝트를 빠르게 이해하기 위한 요약 |
| `AIMemory/work.log` | 최근 작업 기록을 남기는 append-only 로그 |
| `AIMemory/INDEX.md` | 과거 로그, 주제, 핸드오프 파일을 찾기 위한 색인 |
| `AIMemory/archive/` | 오래된 상세 작업 로그 보관 |
| `AIMemory/cold/` | 장기 요약, 월별 digest 보관 |
| `AIMemory/handoffs/` | 다른 에이전트에게 넘길 인계 파일 보관 |

## 쿼리 모드와 태스크 모드

v4부터 에이전트는 요청을 받을 때 먼저 **쿼리 모드**인지 **태스크 모드**인지를 판단합니다.

### 쿼리 모드

사용자가 정보만 요청하는 경우입니다. 예를 들면:

- "핸드오프 파일이 몇 개 있어?"
- "PROJECT_OVERVIEW.md에 스택이 뭐라고 쓰여 있어?"
- "현재 상태가 어때?"
- "이 접근 방식이 괜찮을 것 같아?"

쿼리 모드에서 에이전트는 다음처럼 동작합니다.

1. 질문에 답하는 데 직접 필요한 파일만 읽는다.
2. 전체 세션 시작 루틴(PROTOCOL.md, PROJECT_OVERVIEW.md, work.log 순 읽기)을 실행하지 않는다.
3. 답변을 보고하고 멈춘다.
4. 다음 액션을 예단하지 않는다. 사용자가 결정하도록 기다린다.

### 상태·의견 질문 규칙

사용자가 현황, 상태, 진행 상황, 의견, 평가 등을 물으면:

- 관찰한 사실이나 자신의 의견을 답한다.
- 멈춘다. 다음 논리적 행동으로 자동 진입하지 않는다.
- 사용자가 계속 작업하기를 원한다고 가정하지 않는다.
- 사용자로부터 명시적인 다음 지시가 있을 때까지 기다린다.

이 규칙은 에이전트가 상태 질문에 답한 뒤 허가받지 않은 파일 변경이나 작업을 자동으로 시작하는 패턴을 방지합니다.

### 태스크 모드

사용자가 파일 생성, 수정, 삭제, 이동, 실행 등을 요청하는 경우입니다. 이 경우에만 전체 세션 시작 루틴을 실행합니다.

---

## 사용 방법 1. 새 프로젝트에서 바로 사용하기

새 프로젝트에서는 템플릿 전체를 복사하거나 zip 파일을 풀어 사용합니다.

예를 들어 새 프로젝트 폴더를 만들고 템플릿을 적용합니다.

```bash
mkdir dice-game
cd dice-game
unzip ~/ai-prompts/aimemory-agent-template.zip
```

또는 GitHub Codespaces, 로컬 저장소, NAS, 클라우드 드라이브 등에 보관한 템플릿 내용을 새 프로젝트 루트에 복사합니다.

적용 후 구조는 다음과 같아야 합니다.

```text
dice-game/
├─ AGENTS.md
├─ CLAUDE.md
├─ GEMINI.md
├─ opencode.md
└─ AIMemory/
   ├─ PROTOCOL.md
   ├─ INDEX.md
   ├─ PROJECT_OVERVIEW.md
   ├─ work.log
   ├─ archive/
   ├─ cold/
   └─ handoffs/
```

그다음 Codex, Claude Code, Antigravity CLI, OpenCode 중 하나를 프로젝트 루트에서 실행하고 다음처럼 요청합니다.

```text
이 프로젝트의 AIMemory 구조를 확인하고 PROJECT_OVERVIEW.md를 현재 프로젝트 기준으로 초기화하라.
```

이제 일반 작업을 요청하면 됩니다.

```text
간단한 주사위 게임을 만들어줘. 끝나면 Claude에게 넘겨라.
```

루트의 `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `opencode.md`가 `AIMemory/PROTOCOL.md`를 참조하므로, 에이전트는 작업 전후로 AIMemory를 활용하도록 안내됩니다.

## 사용 방법 2. 기존 프로젝트에 안전하게 적용하기

기존 프로젝트에는 템플릿 전체를 그대로 풀지 않는 편이 안전합니다.

기존 프로젝트에는 이미 `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `opencode.md` 또는 다른 설정 파일이 있을 수 있기 때문입니다. 이 경우에는 zip 파일에서 `aimemory-agent-setup.md`만 꺼내 사용합니다.

예시는 다음과 같습니다.

```bash
unzip aimemory-agent-template.zip "aimemory-agent-template/aimemory-agent-setup.md"
```

기존 프로젝트 루트로 `aimemory-agent-setup.md`만 복사한 뒤, 에이전트에게 다음처럼 요청합니다.

```text
aimemory-agent-setup.md를 읽고, 기존 파일을 덮어쓰지 말고 보존하면서 AIMemory 구조와 지침 블록만 병합하라.
```

이 방식은 기존 파일을 보존하면서 필요한 AIMemory 구조와 관리 블록만 추가하는 데 적합합니다.

## 기본 작업 흐름

AIMemory를 적용한 프로젝트에서는 다음 흐름으로 작업합니다.

```text
1. Codex가 기능 구현
2. Codex가 work.log에 작업 기록
3. Codex가 Claude용 handoff 파일 생성
4. Claude가 AIMemory와 handoff 파일을 읽고 보완
5. Claude가 work.log에 결과 기록
6. Antigravity가 UX 문구나 설명을 검토
7. Antigravity가 work.log와 필요한 handoff를 기록
```

사용자는 매번 이전 작업 내용을 길게 설명할 필요가 없습니다.

예를 들어 Codex에서 먼저 요청합니다.

```text
간단한 주사위 게임을 만들어줘. 끝나면 Claude에게 넘겨라.
```

Codex는 작업 후 다음과 같은 정보를 남깁니다.

```text
AIMemory/work.log
AIMemory/handoffs/handoff_dice-game.<model-id>.md
```

이후 Claude Code에서 다음처럼 이어받을 수 있습니다.

```text
AIMemory를 확인하고 Codex가 남긴 주사위 게임 작업을 이어서 보완하라.
```

## 주사위 게임 예시 시나리오

새 프로젝트 `dice-game`에서 Codex, Claude Code, Antigravity CLI, OpenCode를 같은 폴더로 엽니다.

먼저 Codex에서 다음처럼 요청합니다.

```text
브라우저에서 실행되는 간단한 주사위 게임을 만들어줘. 끝나면 Claude에게 넘겨라.
```

Codex는 예를 들어 다음 파일을 만들 수 있습니다.

```text
index.html
style.css
game.js
```

그리고 `AIMemory/work.log`에 구현 내용과 남은 작업을 기록하고, 필요하면 `AIMemory/handoffs/`에 Claude가 이어받을 핸드오프 파일을 남깁니다.

다음으로 Claude Code에서 다음처럼 요청합니다.

```text
AIMemory를 확인하고 Codex가 만든 주사위 게임을 보완하라.
```

Claude는 Codex가 남긴 기록을 읽고, UI 개선, 점수판 보완, 예외 처리, 코드 정리 등을 수행할 수 있습니다.

마지막으로 Antigravity CLI에서 다음처럼 요청할 수 있습니다.

```text
AIMemory를 확인하고 Claude가 보완한 주사위 게임의 사용자 안내 문구와 UX 설명을 개선하라.
```

이 과정에서 각 에이전트는 같은 프로젝트 폴더 안의 `AIMemory/`를 통해 작업 상태를 공유합니다.

## 체크포인트와 컨텍스트 압박 관리

이 템플릿은 긴 작업 중간에 상태를 저장하도록 설계되어 있습니다.

에이전트는 다음 상황에서 `CHECKPOINT` 이벤트를 `AIMemory/work.log`에 남기도록 안내됩니다.

- 여러 파일을 수정한 경우
- 작업 단계가 길어진 경우
- 구현, 테스트, 리팩터링, 문서화가 이어지는 경우
- 대화 맥락이 길어져 후속 작업이 어려워질 수 있는 경우
- 다른 에이전트가 이어받을 가능성이 있는 경우

예시 로그는 다음과 같습니다.

```text
### 2026-06-28 16:20 | claude-opus-4-8 | CHECKPOINT
Summary: 주사위 게임의 기본 구조, 점수 시스템, 주사위 굴리기 로직을 구현함.
Changed files: index.html, style.css, game.js
Current focus: 승패 판정과 재시작 흐름 보완 중.
Remaining: 게임 설명 문구, 애니메이션 효과, 코드 정리.
Risks: 작업 맥락이 길어지고 있어 다음 작업 전 handoff 작성 권장.
```

컨텍스트가 길어졌다고 판단하면 에이전트는 다른 에이전트나 새 세션으로 넘길 수 있도록 handoff 파일 생성을 권고할 수 있습니다.

## 종료 전 상태 저장

작업을 끝내기 전에 현재 상태를 저장해 두면, 다른 에이전트나 새 세션에서 이어받기 쉽습니다.

종료 전에는 다음처럼 요청합니다.

```text
종료 전 AIMemory에 현재 상태를 저장하고, 다른 에이전트가 이어받을 수 있도록 정리하라.
```

에이전트는 이 요청을 받으면 다음 작업을 수행하는 것이 좋습니다.

```text
1. 현재 작업 상태 요약
2. 완료된 작업 기록
3. 변경 파일 목록 기록
4. 남은 작업 기록
5. 위험 요소와 미확정 사항 기록
6. work.log에 CHECKPOINT, WORK_END 또는 HANDOFF 기록
7. 필요하면 handoff 파일 생성
```

정리 후 실제 종료 명령을 실행하면 됩니다.

```text
/quit
```

## 메모리 계층 구조

AIMemory는 세 단계 메모리 구조를 사용합니다.

| 계층 | 위치 | 용도 | 읽는 빈도 |
|---|---|---|---|
| Hot | `AIMemory/work.log` | 최근 작업 기록 | 매 작업 |
| Warm | `AIMemory/archive/` | 오래된 상세 로그 | 필요할 때 |
| Cold | `AIMemory/cold/` | 장기 요약, digest | 명시적으로 필요할 때 |

목표는 에이전트가 매번 모든 과거 기록을 읽지 않도록 하는 것입니다.

최근 기록은 `work.log`에서 확인하고, 오래된 내용은 `INDEX.md`를 통해 필요한 파일만 찾아 읽습니다.

## 권장 운영 방식

새 프로젝트라면 템플릿 전체를 복사해 사용합니다.

```text
새 프로젝트
→ aimemory-agent-template.zip 압축 해제
→ PROJECT_OVERVIEW.md 초기화
→ 작업 시작
```

기존 프로젝트라면 `aimemory-agent-setup.md`만 사용합니다.

```text
기존 프로젝트
→ aimemory-agent-setup.md만 추출
→ 에이전트에게 기존 파일 보존 병합 지시
→ 작업 시작
```

긴 작업 중에는 중간 정리를 요청할 수 있습니다.

```text
현재까지 진행 상황을 AIMemory에 정리하라.
```

다른 에이전트로 넘길 때는 다음처럼 요청합니다.

```text
지금 상태를 Codex가 이어받을 수 있도록 handoff 파일로 정리하라.
```

## 주의 사항

이 템플릿은 강제 실행 프로그램이 아니라 파일 기반 협업 규칙입니다.

따라서 에이전트가 규칙을 지키지 않으면 사용자가 확인해야 할 수 있습니다.

```text
work.log에 기록했는지 확인하라.
```

또는 다음처럼 요청할 수 있습니다.

```text
AIMemory 프로토콜에 따라 이번 작업을 work.log에 기록하고 handoff 파일도 만들어라.
```

또한 `AIMemory/`에는 프로젝트 맥락, 변경 내용, 의사결정, 작업 이력이 들어갈 수 있습니다. 공개 저장소에서 민감한 프로젝트를 다룬다면 `.gitignore`에 추가하는 것을 권장합니다.

예시는 다음과 같습니다.

```gitignore
AIMemory/
```

단, 템플릿 자체를 공개하는 저장소라면 빈 초기 상태의 `AIMemory/`는 포함해도 됩니다. 실제 프로젝트 기록이 들어간 `AIMemory/`를 공개 저장소에 올리지 않도록 주의해야 합니다.

## 이 템플릿이 적합한 경우

이 템플릿은 다음 상황에 적합합니다.

- Codex, Claude Code, Antigravity CLI, OpenCode를 번갈아 사용하는 경우
- 한 프로젝트를 여러 세션에 걸쳐 작업하는 경우
- 토큰 한도 때문에 에이전트를 바꿔야 하는 경우
- 작업 기록, 결정 사항, 핸드오프를 파일로 남기고 싶은 경우
- 새 에이전트가 전체 프로젝트를 처음부터 다시 분석하는 일을 줄이고 싶은 경우
- 코드 구현, 리뷰, 테스트, 문서화를 서로 다른 에이전트에게 나누어 맡기고 싶은 경우

## 이 템플릿이 적합하지 않은 경우

다음 상황에서는 꼭 필요하지 않습니다.

- 한 번만 쓰고 버릴 임시 작업
- 파일 시스템 접근이 없는 채팅형 LLM만 사용하는 경우
- 한 에이전트가 짧은 작업을 단독으로 끝내는 경우
- 프로젝트 기록을 파일로 남기면 안 되는 보안 환경

## 빠른 요약

새 프로젝트에서는 zip을 풉니다.

```bash
unzip aimemory-agent-template.zip
```

기존 프로젝트에서는 setup 문서만 꺼내 적용합니다.

```text
aimemory-agent-setup.md를 읽고, 기존 파일을 보존하면서 AIMemory 구조와 지침 블록만 병합하라.
```

작업 중간에는 체크포인트를 남깁니다.

```text
현재까지 진행 상황을 AIMemory에 정리하라.
```

종료 전에는 상태 저장을 요청합니다.

```text
종료 전 AIMemory에 현재 상태를 저장하고, 다른 에이전트가 이어받을 수 있도록 정리하라.
```

다른 에이전트는 같은 폴더에서 AIMemory를 읽고 이어받습니다.

```text
AIMemory를 확인하고 이전 에이전트가 남긴 작업을 이어서 진행하라.
```

