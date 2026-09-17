# feature-doc

구현이 끝난 backend feature/API를 현재 repository 코드 기준으로 다시 확인하고, 한국어 문서와 self-contained HTML diagram을 생성하는 Codex 전역 Skill입니다.

최근 작업 세션의 context는 범위를 찾는 힌트로만 사용합니다. 최종 문서는 반드시 현재 코드, `git diff`, OpenAPI, DTO/schema, validation, middleware/auth, Service/Repository, DB schema/migration, queue/worker, external SDK/API, env 사용처, tests를 실제 확인한 뒤 작성합니다.

## 제공하는 것

- 짧은 호출: `/feature-doc` 또는 `$feature-doc`
- 기능 단위 문서 폴더: `docs/features/<feature-name>/`
- API contract, Request/Response, 조건별 payload, Error, 내부 동작, infrastructure/external service, 주요 규칙, 관련 코드
- `architecture.html`
- 의미 있는 API별 `<api-name>-sequence.html`
- 복잡한 데이터 이동이 있을 때만 `data-flow.html`
- 생성된 모든 파일의 절대 경로를 마지막에 출력

코드에서 확인되지 않은 내용은 추측하지 않습니다. 확인되지 않은 값은 `코드에서 확인되지 않음`으로 표시합니다.

## 전역 설치

### 직접 복사

이 레포를 clone한 뒤 Skill 폴더를 Codex 전역 Skill 경로에 복사합니다.

```bash
git clone https://github.com/<your-account>/feature-doc.git
mkdir -p ~/.codex/skills
cp -R feature-doc ~/.codex/skills/feature-doc
```

이미 같은 이름의 Skill이 있다면 복사 전에 기존 폴더를 확인하고, 필요한 경우 백업한 뒤 교체하세요.

### `$CODEX_HOME`을 사용하는 환경

`CODEX_HOME`이 설정된 환경에서는 다음 경로에 설치합니다.

```bash
mkdir -p "$CODEX_HOME/skills"
cp -R feature-doc "$CODEX_HOME/skills/feature-doc"
```

설치 후 Codex를 새로 열거나 Skill 목록을 새로고침하면 `feature-doc`을 사용할 수 있습니다.

## 호출 방법

작업을 수행한 세션에서 다음처럼 호출합니다.

```text
/feature-doc
```

특정 범위를 알려주고 싶을 때:

```text
/feature-doc webhook
/feature-doc 결제 API
```

`$feature-doc` 형식으로 직접 호출할 수도 있습니다.

```text
$feature-doc
$feature-doc 이번에 구현한 주문 취소 API만 문서화해줘
```

## 생성되는 구조

```text
docs/features/<feature-name>/
├── README.md
├── architecture.html
├── <api-name>-sequence.html
└── data-flow.html                 # 복잡한 데이터 이동이 있을 때만
```

API마다 폴더를 과도하게 나누지 않고, 하나의 feature 폴더 안에서 관련 API를 함께 설명합니다. 단순한 field 목록은 table과 JSON example로 작성하고, diagram이 더 이해하기 쉬운 관계만 HTML diagram으로 생성합니다.

## 문서 언어

기본 언어는 한국어입니다. API, Request, Response, payload, Controller, Service, Repository, validation, middleware, worker, queue, migration, webhook, architecture, sequence, SDK, schema처럼 개발자가 익숙한 용어는 영어를 유지합니다. class, method, field, enum, table, service, queue, worker 이름은 실제 코드에 정의된 이름을 그대로 사용합니다.

## Diagram 동작

`diagram-design` Skill이 설치되어 있으면 architecture, sequence, data-flow diagram을 self-contained HTML로 생성합니다. diagram은 실제 코드와 설정으로 확인되는 node, arrow, branch만 사용하며 complexity가 높으면 overview/detail로 나눕니다.

`diagram-design` Skill이 설치되어 있지 않으면 이를 명확히 알리고 `README.md`를 포함한 text documentation은 계속 생성합니다. 이 경우 존재하지 않는 diagram HTML을 만들거나 생성됐다고 보고하지 않습니다.

## 레포에 적용할 때 확인할 것

1. Codex 전역 경로에 `feature-doc` 폴더를 복사합니다.
2. backend 기능 구현을 완료한 세션에서 `/feature-doc`을 호출합니다.
3. 생성된 `docs/features/<feature-name>/README.md`와 diagram 링크를 검토합니다.
4. `확인되지 않은 내용`과 API contract의 조건부 field를 실제 코드와 대조합니다.

## 파일 구조

```text
feature-doc/
├── SKILL.md              # Codex가 실행할 핵심 지침
├── agents/
│   └── openai.yaml       # Codex UI metadata와 default prompt
└── README.md             # 설치와 사용 방법
```

## 검증

Skill 구조 검증은 Codex의 `skill-creator` validator로 실행할 수 있습니다.

```bash
python3 /Users/jimin/.codex/skills/.system/skill-creator/scripts/quick_validate.py ./feature-doc
```

