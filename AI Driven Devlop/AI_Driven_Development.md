# AI 주도 개발(AI-Driven Development) 실무 개념 가이드

> 이 문서는 특정 도구 사용법이 아니라, AI 주도 개발이라는 개발 방식의 개념, 등장 배경, 구성 요소, 운영 방식, 협업 기준을 이해하기 위한 학습 자료입니다.  
> 기준 독자는 Java/Spring 백엔드 개발자이며, Codex, Claude, Gemini 같은 AI 모델과 Codex 계열 CLI/IDE 도구, Oh My Codex 같은 자동화 도구를 접했지만 전체 구조가 아직 완전히 정리되지 않은 개발자를 상정합니다.

---

## 목차

1. AI 주도 개발이란 무엇인가
2. 왜 AI 주도 개발이 등장했는가
3. 기존 개발 방식과 AI 주도 개발의 차이
4. AI 주도 개발의 전체 구조
5. Model, Agent, Orchestrator, Skill, Workflow의 차이
6. Context Engineering
7. Spec-Driven Development
8. Harness Engineering
9. Feedback Loop
10. Agent Orchestration
11. Codex 계열 도구와 Oh My Codex의 위치
12. AI 주도 개발에서 프로젝트 문서는 어떻게 관리해야 하는가
13. ERD, API 명세, 보안 정보는 어디까지 공유해야 하는가
14. Spring 백엔드 개발자 관점에서의 적용 방식
15. 흔한 오해와 실패 패턴
16. 단계별 도입 전략
17. 최종 정리

---

# 1. AI 주도 개발이란 무엇인가

AI 주도 개발은 단순히 AI에게 코드를 작성하게 하는 방식이 아니다.  
많은 사람이 처음 AI 코딩 도구를 접하면 “이제 AI가 코드를 대신 짜주는구나”라고 이해한다. 하지만 실제로 실무에서 AI를 제대로 사용해 보면, AI에게 코드를 직접 맡기는 것만으로는 충분하지 않다는 사실을 금방 알게 된다.

AI는 코드를 생성할 수 있다. 테스트 코드를 만들 수도 있고, 기존 코드를 읽고 리팩터링 제안을 할 수도 있다. 그러나 AI는 기본적으로 프로젝트의 맥락, 비즈니스 규칙, 운영 정책, 팀의 코딩 컨벤션, 장애 대응 기준, 보안 요구사항을 스스로 완전히 이해하지 못한다. AI가 좋은 결과를 내기 위해서는 개발자가 AI가 일할 수 있는 환경과 기준을 먼저 만들어야 한다.

따라서 AI 주도 개발은 다음과 같이 정의할 수 있다.

```text
AI 주도 개발이란,
개발자가 요구사항, 프로젝트 맥락, 검증 기준, 반복 흐름을 구조화하고,
AI가 그 구조 안에서 구현·검증·수정을 수행하도록 만드는 개발 방식이다.
```

이 정의에서 중요한 것은 “AI가 개발한다”가 아니라 “개발자가 AI가 일할 수 있는 시스템을 설계한다”는 점이다.  
즉, 개발자의 역할이 단순 코드 작성자에서 개발 프로세스 설계자로 이동한다.

기존 개발에서 개발자는 직접 코드를 작성하고, 직접 테스트를 실행하고, 직접 실패 원인을 분석하고, 직접 수정했다. AI 주도 개발에서는 이 반복 작업 중 상당 부분을 AI와 자동화 도구에 위임한다. 하지만 무엇을 만들어야 하는지, 어떤 결과가 올바른지, 어떤 규칙을 지켜야 하는지는 여전히 개발자가 정의해야 한다.

AI 주도 개발의 핵심은 다음 문장으로 요약할 수 있다.

```text
AI 주도 개발은 코드 자동 생성 기술이 아니라,
좋은 입력과 검증 시스템을 설계하는 개발 방법론이다.
```

---

# 2. 왜 AI 주도 개발이 등장했는가

AI 주도 개발이 등장한 배경을 이해하려면 먼저 AI 코딩 도구가 어떻게 발전했는지 살펴봐야 한다.

초기 AI 코딩 도구는 주로 자동완성 도구에 가까웠다. 개발자가 코드를 작성하면 다음 줄을 예측하거나, 반복적인 boilerplate 코드를 자동으로 채워주는 정도였다. 이 단계에서는 AI가 개발 프로세스 전체를 바꾸기보다는 개발자의 타이핑 속도를 높이는 보조 도구에 가까웠다.

이후 GPT, Codex, Claude, Gemini 같은 대형 언어 모델이 발전하면서 AI는 단순 자동완성을 넘어 자연어 요청을 코드로 변환할 수 있게 되었다. 예를 들어 “회원가입 API를 만들어줘”라고 요청하면 Controller, DTO, Service, Repository, 테스트 코드까지 한 번에 생성하는 것이 가능해졌다.

하지만 여기서 중요한 문제가 발생했다. AI가 코드를 만들 수는 있지만, 그 코드가 우리 프로젝트에 맞는지는 별개의 문제였다.

예를 들어 Spring 프로젝트에서 다음과 같은 규칙이 있다고 하자.

- 모든 API 응답은 `ResponseDto<T>`로 감싼다.
- Entity를 Controller 응답으로 직접 반환하지 않는다.
- 인증은 JWT 기반이며, `SecurityContextHolder`에 커스텀 Principal을 저장한다.
- 예외 응답은 공통 `ErrorCode` enum을 사용한다.
- Service 계층에서는 트랜잭션 경계를 명확히 한다.
- DB 변경은 반드시 마이그레이션 스크립트와 함께 진행한다.

AI는 이런 규칙을 알려주지 않으면 일반적인 Spring 예제를 기반으로 코드를 생성한다. 그 결과 프로젝트의 기존 구조와 맞지 않는 코드가 생성된다. 코드 자체는 컴파일될 수 있지만, 팀의 설계 원칙이나 운영 정책과 맞지 않게 된다.

이 문제가 반복되면서 개발자들은 깨달았다.

```text
AI에게 일을 시키려면,
AI가 따라야 할 맥락과 기준을 먼저 만들어야 한다.
```

이 사고에서 나온 개념이 Context Engineering, Spec-Driven Development, Harness Engineering, Feedback Loop, Agent Orchestration이다.

즉, AI 주도 개발은 AI가 똑똑해졌기 때문에 자연스럽게 완성된 것이 아니라, AI의 한계를 보완하기 위해 개발 프로세스를 재구성하면서 등장한 방식이다.

---

# 3. 기존 개발 방식과 AI 주도 개발의 차이

기존 개발 방식은 대략 다음과 같은 흐름을 가진다.

```text
요구사항 확인
→ 설계
→ 코드 작성
→ 테스트 작성
→ 테스트 실행
→ 실패 수정
→ 리뷰
→ 배포
```

이 흐름에서 개발자는 대부분의 작업을 직접 수행한다. 요구사항을 읽고, 설계를 고민하고, 코드를 작성하고, 테스트를 돌리고, 실패를 분석한다. 개발자의 경험과 판단이 매우 중요하다.

AI 주도 개발도 본질적으로 이 흐름을 버리지 않는다. 오히려 기존 개발 흐름을 더 명확하게 분리한다.

```text
Context 작성
→ Spec 작성
→ AI 실행
→ Harness 검증
→ Feedback Loop
→ 리뷰 및 승인
```

차이는 “누가 무엇을 하느냐”에 있다.

기존 개발에서 개발자는 직접 코드를 작성하는 데 많은 시간을 쓴다. AI 주도 개발에서 개발자는 AI가 코드를 작성할 수 있도록 맥락과 기준을 제공하고, 결과를 검증하는 시스템을 만든다. 반복적인 구현과 수정은 AI에게 위임할 수 있다.

이를 역할 변화로 표현하면 다음과 같다.

```text
기존 개발자:
요구사항 분석자 + 설계자 + 코드 작성자 + 테스트 작성자 + 디버거

AI 주도 개발자:
요구사항 구조화 담당자 + Context 설계자 + Spec 작성자 + Harness 설계자 + 결과 검증자
```

즉, AI 주도 개발에서 개발자의 중요성이 줄어드는 것이 아니다. 오히려 개발자가 “무엇이 올바른 개발인가”를 더 명확하게 정의해야 한다. AI는 지시된 범위 안에서 빠르게 실행할 수 있지만, 지시의 품질이 낮으면 잘못된 결과도 빠르게 만들어낸다.

AI 주도 개발의 위험은 AI가 코드를 못 짜는 데 있지 않다. AI가 틀린 방향으로 매우 빠르게 많은 코드를 생성할 수 있다는 데 있다. 그래서 Context, Spec, Harness가 중요해진다.

---

# 4. AI 주도 개발의 전체 구조

AI 주도 개발은 다음 구조로 이해하면 가장 명확하다.

```text
Context → Spec → AI Execution → Harness → Feedback Loop
```

각 단계는 서로 다른 역할을 가진다.

## 4.1 Context

Context는 AI가 프로젝트 전체를 이해하기 위한 배경 정보다.  
프로젝트 구조, 아키텍처 규칙, API 응답 형식, 인증 방식, DB 구조, 예외 처리 정책, 코딩 컨벤션 등이 여기에 포함된다.

Context는 “이 프로젝트에서는 어떻게 개발해야 하는가”에 대한 답을 제공한다.

## 4.2 Spec

Spec은 특정 기능이 어떻게 동작해야 하는지를 정의한다.  
예를 들어 회원가입 API라면 요청 값, 검증 규칙, 비즈니스 로직, 응답 형식, 실패 케이스, 테스트 케이스가 Spec에 포함된다.

Spec은 “이번 기능에서 무엇이 정답인가”에 대한 답을 제공한다.

## 4.3 AI Execution

AI Execution은 실제로 AI가 작업을 수행하는 단계다.  
여기서 Codex, Claude, Gemini 같은 모델 또는 이들을 활용한 Agent가 코드를 작성하고, 파일을 수정하고, 테스트를 생성한다.

AI Execution은 “정의된 기준에 따라 실제 작업을 수행하는 단계”다.

## 4.4 Harness

Harness는 AI가 만든 결과가 올바른지 검증하는 시스템이다.  
JUnit 테스트, 통합 테스트, 빌드 스크립트, API 테스트, lint, 정적 분석 등이 Harness에 해당한다.

Harness는 “이 결과가 맞는가”를 판단한다.

## 4.5 Feedback Loop

Feedback Loop는 Harness 결과를 기반으로 AI가 다시 수정하도록 만드는 반복 구조다.  
테스트가 실패하면 실패 로그를 AI에게 전달하고, AI가 수정하고, 다시 테스트를 실행한다.

Feedback Loop는 “틀렸으면 다시 고쳐라”라는 반복 흐름이다.

이 다섯 가지를 한 문장으로 정리하면 다음과 같다.

```text
Context는 방향을 정하고,
Spec은 목표를 정하고,
AI는 실행하고,
Harness는 검증하고,
Feedback Loop는 개선한다.
```

---

# 5. Model, Agent, Orchestrator, Skill, Workflow의 차이

AI 주도 개발을 이해할 때 가장 많이 헷갈리는 용어가 Model, Agent, Orchestrator, Skill, Workflow다. 이 용어들은 서로 비슷해 보이지만 역할이 다르다.

## 5.1 Model

Model은 AI의 두뇌다.  
Codex, Claude, Gemini, GPT 같은 것이 Model이다.

Model은 입력을 받아 출력을 생성한다. 코드도 만들 수 있고, 설명도 할 수 있고, 문서도 작성할 수 있다. 하지만 Model 자체는 도구를 실행하거나 파일을 수정하는 능력을 항상 가지는 것은 아니다. 그런 행동 능력은 보통 Agent나 CLI 도구가 Model 위에 붙여 제공한다.

정리하면 다음과 같다.

```text
Model = 생각하고 생성하는 두뇌
```

## 5.2 Agent

Agent는 Model에 역할과 도구 사용 능력을 부여한 실행 단위다.  
예를 들어 같은 Claude 모델을 사용하더라도, 어떤 Agent는 Planner 역할을 하고, 어떤 Agent는 Coder 역할을 하고, 어떤 Agent는 Reviewer 역할을 할 수 있다.

즉, Agent는 모델 그 자체가 아니라 “역할을 가진 AI 실행자”다.

예를 들어 다음과 같은 Agent를 구성할 수 있다.

- Planner Agent: 요구사항을 분석하고 구현 계획을 만든다.
- Coder Agent: 실제 코드를 작성한다.
- Reviewer Agent: 코드 품질과 아키텍처 위반 여부를 검토한다.
- Test Agent: 테스트 케이스를 만들고 실행한다.
- Fixer Agent: 실패 로그를 보고 수정한다.

정리하면 다음과 같다.

```text
Agent = 특정 역할을 부여받은 AI 작업자
```

## 5.3 Orchestrator

Orchestrator는 여러 Agent를 어떤 순서로 실행할지 조율하는 관리자다.  
여러 Agent가 있다고 해서 자동으로 협업하는 것은 아니다. 누군가는 어떤 작업에 어떤 Agent를 호출할지, 결과를 다음 Agent에게 어떻게 넘길지, 실패하면 다시 누구에게 맡길지를 결정해야 한다.

이 역할을 하는 것이 Orchestrator다.

예를 들어 회원가입 기능 개발 요청이 들어오면 Orchestrator는 다음과 같이 흐름을 구성할 수 있다.

```text
Planner Agent 실행
→ Coder Agent 실행
→ Test Agent 실행
→ 실패 시 Fixer Agent 실행
→ Reviewer Agent 실행
```

정리하면 다음과 같다.

```text
Orchestrator = Agent들을 조합하고 실행 흐름을 제어하는 관리자
```

## 5.4 Skill

Skill은 특정 작업을 잘 수행하기 위한 지침 또는 도구 묶음이다.  
예를 들어 “Spring API 구현 Skill”, “JUnit 테스트 작성 Skill”, “QueryDSL 최적화 Skill” 같은 식으로 만들 수 있다.

Skill은 Agent와 비슷해 보이지만 약간 다르다. Agent가 “작업자”라면 Skill은 “작업자가 사용할 수 있는 전문 능력”에 가깝다.

예를 들어 하나의 Agent가 작업 중에 다음 Skill을 사용할 수 있다.

- Spring API 구현 Skill
- Spring Security 분석 Skill
- JPA N+1 점검 Skill
- 테스트 케이스 생성 Skill

정리하면 다음과 같다.

```text
Skill = 특정 작업을 수행하기 위한 전문 지침 또는 도구
```

## 5.5 Workflow

Workflow는 전체 작업 흐름이다.  
어떤 순서로 계획하고, 구현하고, 테스트하고, 수정할지 정의한다.

예를 들어 다음과 같은 Workflow가 있을 수 있다.

```text
Spec 읽기
→ 영향 범위 분석
→ 구현 계획 작성
→ 코드 수정
→ 테스트 생성
→ 테스트 실행
→ 실패 수정
→ 최종 요약
```

정리하면 다음과 같다.

```text
Workflow = 작업을 진행하는 절차
```

이 용어들을 모두 연결하면 다음과 같다.

```text
Model은 두뇌다.
Agent는 역할을 가진 작업자다.
Skill은 작업자가 사용할 수 있는 전문 능력이다.
Workflow는 작업 절차다.
Orchestrator는 전체 흐름을 조율한다.
```

## 5.6 Codex는 Model인가, Agent 도구인가

AI 주도 개발을 이해할 때 자주 생기는 오해가 있다.  
Codex, Claude Code, Cursor, Oh My Codex 같은 이름을 모두 같은 층위의 개념으로 보는 것이다.

하지만 실제로는 구분해야 한다.

```text
Model = 텍스트와 코드를 생성하는 두뇌
Agent = Model에 역할과 도구 사용 능력을 붙인 실행자
CLI/IDE 도구 = Agent를 실행하고 파일·명령·워크플로우와 연결하는 환경
```

즉, Codex라는 이름이 어떤 문맥에서는 모델을 의미할 수 있지만, 실제 개발 환경에서 우리가 사용하는 Codex 계열 도구는 단순한 모델 그 자체라기보다 다음 기능을 묶은 실행 환경에 가깝다.

- 프로젝트 파일 읽기
- 파일 수정
- 터미널 명령 실행
- 테스트 실행
- 실패 로그 분석
- 여러 단계의 작업 흐름 수행
- Agent, Skill, Prompt, Workflow 설정 로딩

따라서 AI 주도 개발 관점에서는 다음처럼 이해하는 것이 좋다.

```text
LLM은 두뇌다.
Codex 계열 개발 도구는 그 두뇌를 프로젝트 안에서 일하게 만드는 실행 환경이다.
```

이 구분이 중요한 이유는, 도구를 설치했다고 해서 자동으로 AI 주도 개발이 되는 것이 아니기 때문이다.  
도구는 파일을 수정하고 명령을 실행할 수 있게 해주지만, 무엇이 올바른 수정인지, 어떤 규칙을 지켜야 하는지, 어떤 검증을 통과해야 하는지는 개발자가 Context, Spec, Harness로 제공해야 한다.

## 5.7 Agent는 실제로 무엇으로 구성되는가

Agent는 단순히 “AI 한 명”이라는 추상적인 표현으로만 이해하면 부족하다.  
실무적으로 Agent는 보통 다음 요소들의 조합으로 구성된다.

```text
Agent = Model + Role + Instruction + Tools + Context + Input/Output Contract
```

각 요소의 의미는 다음과 같다.

### Model

Agent가 사용할 AI 모델이다.  
예를 들어 GPT, Claude, Gemini 같은 모델이 여기에 해당한다.

### Role

Agent가 맡는 역할이다.

예시는 다음과 같다.

- Planner
- Coder
- Reviewer
- Tester
- Fixer
- Document Writer

역할이 명확할수록 Agent의 판단 범위가 줄어들고 결과가 안정된다.

### Instruction

Agent가 따라야 할 행동 지침이다.  
예를 들어 Coder Agent라면 다음과 같은 지침을 가질 수 있다.

```text
- 기존 아키텍처를 변경하지 않는다.
- Entity를 Controller 응답으로 직접 반환하지 않는다.
- 기존 ErrorCode가 있으면 재사용한다.
- 변경 후 테스트를 실행한다.
```

### Tools

Agent가 사용할 수 있는 도구다.  
파일 읽기, 파일 수정, 검색, 터미널 실행, 테스트 실행 등이 포함될 수 있다.

### Context

Agent가 프로젝트를 이해하기 위해 참조하는 배경 정보다.  
AGENTS.md, docs/context, API 규칙, DB 구조, 테스트 규칙 등이 여기에 해당한다.

### Input/Output Contract

Agent가 어떤 입력을 받고 어떤 출력을 내야 하는지에 대한 약속이다.  
예를 들어 Planner Agent는 코드를 수정하지 않고 계획만 출력해야 하고, Reviewer Agent는 수정이 아니라 리뷰 결과만 출력해야 할 수 있다.

정리하면 Agent는 단순히 모델을 호출하는 것이 아니라, 모델에게 역할·규칙·도구·문맥·입출력 기준을 부여한 실행 단위다.

## 5.8 Orchestrator도 Agent인가

Orchestrator는 Agent일 수도 있고, 아닐 수도 있다.  
중요한 것은 이름이 아니라 역할이다.

Orchestrator의 본질은 다음과 같다.

```text
여러 Agent 또는 작업 단계를 어떤 순서로 실행할지 결정하고,
각 단계의 결과를 다음 단계로 넘기며,
실패 시 재시도 또는 중단 여부를 판단하는 제어자
```

Orchestrator가 단순한 스크립트나 설정 파일일 수도 있다.

```text
plan → implement → test → fix → review
```

이 흐름을 고정된 순서대로 실행한다면 Orchestrator는 단순 Workflow 엔진에 가깝다.

반면 Orchestrator 자체가 AI 모델을 사용해서 다음 행동을 판단한다면, Orchestrator도 하나의 Agent처럼 볼 수 있다.

예를 들어 다음과 같은 판단을 AI가 수행한다면 Orchestrator Agent라고 부를 수 있다.

- 이 작업은 Planner가 먼저 필요한가?
- 바로 Coder에게 넘겨도 되는가?
- 테스트 실패가 단순 컴파일 에러인가, 설계 오류인가?
- Reviewer에게 넘길 시점인가?
- 사람의 승인이 필요한가?

따라서 정확한 이해는 다음과 같다.

```text
Orchestrator는 역할 이름이다.
그 역할을 규칙 기반 코드가 수행하면 Workflow Engine이고,
AI가 판단하면서 수행하면 Orchestrator Agent에 가깝다.
```

## 5.9 AGENTS.md와 SKILL.md의 차이

AI 개발 도구를 사용하다 보면 `AGENTS.md`, `SKILL.md`, agent 설정 파일, prompt 파일 같은 것들을 보게 된다.  
이들은 모두 AI에게 지침을 주는 문서처럼 보이지만 역할이 다르다.

### AGENTS.md

`AGENTS.md`는 보통 프로젝트 전체에서 AI가 따라야 할 공통 행동 규칙이다.

예시는 다음과 같다.

```text
- 이 프로젝트는 Spring Boot 기반이다.
- 모든 API 응답은 공통 ResponseDto를 사용한다.
- Entity를 Controller에서 직접 반환하지 않는다.
- 테스트는 ./gradlew test로 실행한다.
- 보안 관련 코드는 변경 전 반드시 영향 범위를 설명한다.
```

즉, AGENTS.md는 특정 기능 구현법이라기보다 AI 작업자의 기본 근무 규칙에 가깝다.

```text
AGENTS.md = 프로젝트 안에서 AI가 지켜야 할 공통 규칙
```

### SKILL.md

`SKILL.md`는 특정 작업을 잘 수행하기 위한 전문 지침이다.

예시는 다음과 같다.

```text
Spring API Skill
- Controller, Service, Repository 계층을 구분한다.
- Request/Response DTO를 분리한다.
- Validation 실패는 GlobalExceptionHandler에서 처리한다.

Testing Skill
- 성공 케이스와 실패 케이스를 모두 작성한다.
- 기존 테스트 스타일을 따른다.
- 외부 API는 Mock 처리한다.
```

즉, Skill은 작업자가 특정 업무를 수행할 때 참고하는 매뉴얼이다.

```text
SKILL.md = 특정 유형의 작업을 수행하기 위한 전문 지침
```

### 차이 정리

| 구분 | AGENTS.md | SKILL.md |
|---|---|---|
| 범위 | 프로젝트 전체 | 특정 작업 유형 |
| 역할 | 공통 행동 규칙 | 전문 작업 지침 |
| 비유 | 회사 규칙 | 직무 매뉴얼 |
| 예시 | 응답 형식, 테스트 명령, 보안 원칙 | API 구현법, 테스트 작성법, 리팩터링 절차 |

따라서 AGENTS.md와 SKILL.md는 경쟁 관계가 아니라 계층 관계로 이해하는 것이 좋다.

```text
AGENTS.md가 전체 규칙을 정하고,
SKILL.md가 특정 작업 수행 방식을 보강한다.
```

---

# 6. Context Engineering

Context Engineering은 AI가 프로젝트를 제대로 이해하고 일할 수 있도록 필요한 정보를 설계하는 작업이다.

많은 사람이 처음에는 “프롬프트를 잘 쓰면 AI가 잘하겠지”라고 생각한다. 그러나 실무 프로젝트에서는 단일 프롬프트보다 Context가 훨씬 중요하다. 프롬프트는 한 번의 요청이고, Context는 프로젝트 전체의 지속적인 기준이다.

## 6.1 Context는 왜 필요한가

AI는 기본적으로 프로젝트의 전체 사정을 모른다.  
AI는 현재 요청과 제공된 파일, 문서, 대화 내용을 기반으로 응답한다. 만약 프로젝트의 규칙이 명시되어 있지 않으면 AI는 일반적인 관습을 따른다.

문제는 일반적인 관습이 우리 프로젝트의 규칙과 다를 수 있다는 것이다.

예를 들어 우리 프로젝트에서는 모든 응답을 다음과 같이 통일한다고 하자.

```text
ResponseDto<T>
```

하지만 AI에게 이 사실을 알려주지 않으면 AI는 다음처럼 응답 DTO를 직접 반환하거나, 임의의 응답 클래스를 만들 수 있다.

또 다른 예로 우리 프로젝트에서는 Service 계층에서 Entity를 외부에 노출하지 않도록 규칙을 정했는데, AI가 Entity를 그대로 Controller에서 반환할 수도 있다.

이런 문제는 AI가 멍청해서 생기는 것이 아니다. AI에게 필요한 프로젝트 맥락이 제공되지 않았기 때문에 생긴다.

## 6.2 Context에 포함되어야 하는 것

Context는 단순 설명이 아니라 AI가 개발할 때 지켜야 할 기준이어야 한다.  
보통 다음 항목이 포함된다.

첫째, 프로젝트의 기술 스택이다.  
Java 버전, Spring Boot 버전, DB 종류, Redis 사용 여부, JPA/QueryDSL 사용 여부, 테스트 프레임워크 등을 명시한다.

둘째, 아키텍처 규칙이다.  
Controller, Service, Repository의 책임 분리, UseCase 사용 여부, DTO와 Entity 분리 기준, 패키지 구조 규칙 등을 명시한다.

셋째, API 규칙이다.  
응답 포맷, 에러 포맷, HTTP status 사용 기준, validation 처리 방식, Swagger 문서화 규칙 등이 포함된다.

넷째, 인증과 보안 규칙이다.  
JWT 검증 위치, 인증 사용자 정보 전달 방식, SecurityContext 사용 방식, role/permission 처리 기준 등을 포함한다.

다섯째, DB 구조와 도메인 규칙이다.  
ERD, 주요 테이블 설명, 관계, enum 값, 논리 삭제 여부, 인덱스 전략 등이 포함될 수 있다.

여섯째, 테스트 규칙이다.  
단위 테스트와 통합 테스트의 기준, MockMvc 사용 여부, 테스트 데이터 구성 방식, 반드시 통과해야 하는 명령어 등을 명시한다.

## 6.3 Context는 코드 패키지가 아니라 문서 자산이다

Context는 Java 패키지로 둘 대상이 아니다.  
`src/main/java/context` 같은 패키지를 만들어 넣는 것이 아니라, 프로젝트 루트의 문서 디렉터리에 두는 것이 좋다.

예를 들면 다음과 같은 구조가 적절하다.

```text
project-root/
├── AGENTS.md
├── docs/
│   ├── context/
│   │   ├── architecture.md
│   │   ├── api-rules.md
│   │   ├── database.md
│   │   ├── security.md
│   │   └── testing-rules.md
│   └── specs/
│       ├── signup.md
│       └── account-lock.md
├── harness/
│   ├── README.md
│   ├── test.sh
│   └── build.sh
└── src/
```

여기서 `AGENTS.md`는 AI가 가장 먼저 읽어야 하는 공통 지침 역할을 한다.  
`docs/context`는 프로젝트 전체 규칙을 담는다.  
`docs/specs`는 기능별 세부 스펙을 담는다.  
`harness`는 검증 명령이나 테스트 실행 환경을 담는다.

## 6.4 좋은 Context의 특징

좋은 Context는 길다고 좋은 것이 아니다.  
중요한 것은 명확하고, 일관되고, 실행 가능한 기준이어야 한다는 점이다.

좋은 Context는 다음 특징을 가진다.

- AI가 어떤 결정을 해야 할 때 기준으로 삼을 수 있다.
- 팀원들이 같은 규칙을 공유할 수 있다.
- 기능 개발 시 반복적으로 참조할 수 있다.
- 민감정보를 포함하지 않는다.
- 오래된 내용이 방치되지 않는다.

반대로 나쁜 Context는 다음과 같다.

- 추상적인 말만 있고 실제 규칙이 없다.
- 서로 다른 문서가 충돌한다.
- 현재 코드와 맞지 않는다.
- 특정 개발자의 로컬에만 존재한다.
- 운영 secret이나 실제 개인정보가 포함되어 있다.

## 6.5 Context의 핵심 철학

Context Engineering의 핵심은 AI에게 더 많은 자유를 주는 것이 아니라, AI의 행동 범위를 제한하는 것이다.

AI는 가능성이 넓을수록 다양한 코드를 만들어낸다. 하지만 실무 프로젝트에서는 다양성이 항상 좋은 것이 아니다. 오히려 일관성이 중요하다. Context는 AI가 프로젝트의 기존 방식에서 벗어나지 않도록 울타리를 만든다.

즉, Context는 AI에게 다음을 알려주는 문서다.

```text
이 프로젝트에서는 이렇게 생각하고,
이렇게 설계하고,
이렇게 구현해야 한다.
```

---

# 7. Spec-Driven Development

Spec-Driven Development는 기능을 구현하기 전에 그 기능의 동작 기준을 명확히 정의하는 개발 방식이다.

AI 주도 개발에서 Spec이 중요한 이유는 AI가 “무엇이 맞는 결과인지”를 스스로 판단할 수 없기 때문이다. AI는 요청을 바탕으로 그럴듯한 결과를 만들 수 있지만, 그 결과가 실제 비즈니스 요구사항에 맞는지는 별개의 문제다.

## 7.1 Spec은 무엇인가

Spec은 특정 기능의 정답 기준이다.  
예를 들어 회원가입 API를 만든다면 Spec에는 다음 질문에 대한 답이 있어야 한다.

- 어떤 URL로 요청하는가?
- 어떤 HTTP method를 사용하는가?
- 요청 body에는 어떤 값이 들어가는가?
- 각 값의 validation 규칙은 무엇인가?
- 중복 이메일이면 어떻게 처리하는가?
- 비밀번호는 어떻게 암호화하는가?
- 성공 응답은 어떤 형태인가?
- 실패 응답은 어떤 에러 코드를 사용하는가?
- 인증이 필요한 API인가?
- 어떤 테스트 케이스가 반드시 있어야 하는가?

Spec은 “어떻게 코드를 작성할 것인가”보다 “어떤 결과가 올바른가”에 초점을 둔다.

## 7.2 회원가입 API Spec 예시

다음은 회원가입 API를 대상으로 한 Spec의 예시다.  
이 예시는 코드가 아니라 기능의 동작 기준을 설명하는 문서다.

```markdown
# 회원가입 API Spec

## 1. 목적

사용자가 이메일과 비밀번호를 사용하여 신규 계정을 생성할 수 있도록 한다.  
회원가입이 완료되면 사용자 계정이 생성되고, 비밀번호는 평문이 아닌 암호화된 값으로 저장되어야 한다.

## 2. Endpoint

POST /api/users/signup

## 3. 인증 여부

인증이 필요하지 않은 public API이다.  
이미 로그인한 사용자만 사용할 수 있는 API가 아니다.

## 4. Request Body

- email: string, required
- password: string, required
- name: string, required

## 5. Validation Rules

email:
- 필수값이다.
- 이메일 형식이어야 한다.
- 이미 가입된 이메일이면 실패한다.

password:
- 필수값이다.
- 최소 8자 이상이어야 한다.
- 저장 시 반드시 암호화되어야 한다.

name:
- 필수값이다.
- 공백 문자열은 허용하지 않는다.

## 6. Business Flow

1. 요청 body를 검증한다.
2. 이메일 중복 여부를 확인한다.
3. 비밀번호를 BCrypt로 암호화한다.
4. 사용자 Entity를 생성한다.
5. 사용자 정보를 DB에 저장한다.
6. 생성된 사용자 ID를 응답한다.

## 7. Success Response

HTTP Status: 201 Created

Response Body:
{
  "code": "SUCCESS",
  "message": "회원가입이 완료되었습니다.",
  "data": {
    "userId": 1
  }
}

## 8. Error Cases

### 이메일 중복

HTTP Status: 409 Conflict

{
  "code": "EMAIL_DUPLICATED",
  "message": "이미 가입된 이메일입니다."
}

### validation 실패

HTTP Status: 400 Bad Request

{
  "code": "INVALID_REQUEST",
  "message": "요청 값이 올바르지 않습니다."
}

## 9. Side Effects

- users 테이블에 row가 생성된다.
- password 컬럼에는 암호화된 값이 저장된다.
- 평문 password는 로그에 남기지 않는다.

## 10. Test Cases

- 정상 회원가입 성공
- 이메일 중복 시 실패
- 이메일 형식이 올바르지 않으면 실패
- 비밀번호가 8자 미만이면 실패
- 이름이 비어 있으면 실패
- 저장된 비밀번호가 평문이 아닌지 검증
```

이 정도 Spec이 있으면 AI는 단순히 “회원가입 API 만들어줘”라는 요청을 받았을 때보다 훨씬 안정적인 결과를 만들 수 있다.

## 7.3 Spec과 Context의 차이

Context와 Spec은 자주 헷갈린다.  
둘 다 AI에게 주는 문서이기 때문이다. 하지만 역할은 다르다.

Context는 프로젝트 전체에 적용되는 규칙이다.  
Spec은 특정 기능에 적용되는 요구사항이다.

예를 들어 다음은 Context에 들어갈 내용이다.

```text
모든 API 응답은 ResponseDto<T>를 사용한다.
Entity를 Controller에서 직접 반환하지 않는다.
예외는 GlobalExceptionHandler에서 처리한다.
```

반면 다음은 Spec에 들어갈 내용이다.

```text
회원가입 API는 email, password, name을 받는다.
email은 중복될 수 없다.
비밀번호는 BCrypt로 암호화한다.
```

정리하면 다음과 같다.

```text
Context = 이 프로젝트에서는 항상 이렇게 한다.
Spec = 이번 기능은 이렇게 동작해야 한다.
```

## 7.4 Spec 변경은 어떻게 관리하는가

Spec은 항상 최신 상태를 유지하는 것이 중요하다.  
기능이 변경되면 기존 `signup.md`를 그대로 방치하고 `signup-v2.md`를 계속 만드는 방식은 권장하지 않는다. AI가 어떤 문서를 기준으로 해야 하는지 혼란스러워지기 때문이다.

일반적으로는 다음 방식이 좋다.

```text
docs/specs/signup.md        # 항상 최신 Spec
docs/specs/history/signup-history.md  # 변경 이력
```

`signup.md`는 항상 현재 기준의 정답 문서여야 한다.  
변경 이력은 별도 history 문서에 기록한다.

단, API 버전 자체가 공존하는 경우는 예외다.  
예를 들어 `/api/v1/signup`과 `/api/v2/signup`이 동시에 운영된다면 `signup-v1.md`, `signup-v2.md`처럼 분리할 수 있다.

핵심 원칙은 다음이다.

```text
AI가 기준으로 삼는 Spec은 하나여야 한다.
변경 이력은 따로 관리한다.
```

---

# 8. Harness Engineering

Harness Engineering은 AI가 만든 결과가 올바른지 검증하는 시스템을 설계하는 작업이다.

AI 주도 개발에서 Harness는 매우 중요하다.  
AI는 틀린 코드를 만들 수 있다. 더 위험한 것은 틀렸지만 그럴듯한 코드를 만들 수 있다는 점이다. 사람이 보기 전까지 문제를 발견하지 못할 수도 있다.

Harness는 이런 문제를 줄이기 위한 자동 검증 장치다.

## 8.1 Harness란 무엇인가

Harness는 원래 테스트 환경이나 장비를 의미하는 표현이다.  
소프트웨어 개발에서는 특정 코드나 시스템을 실행하고 검증하기 위한 장치를 의미한다.

AI 주도 개발에서 Harness는 다음을 포함할 수 있다.

- 단위 테스트
- 통합 테스트
- API 테스트
- 빌드 명령
- lint
- 정적 분석
- 테스트 데이터 초기화 스크립트
- Docker Compose 기반 실행 환경
- CI 파이프라인

즉, Harness는 단순히 테스트 코드 하나를 말하는 것이 아니다.  
AI가 만든 결과를 자동으로 검증할 수 있는 전체 환경을 의미한다.

## 8.2 Harness와 Feedback Loop의 차이

Harness와 Feedback Loop는 연결되어 있지만 같은 개념은 아니다.

Harness는 판정기다.  
Feedback Loop는 판정 결과를 바탕으로 다시 시도하는 흐름이다.

예를 들어 다음 명령이 있다고 하자.

```bash
./gradlew test
```

이 명령은 Harness에 해당한다.  
테스트를 실행하고 성공/실패를 알려준다.

반면 다음 흐름은 Feedback Loop다.

```text
코드 생성
→ ./gradlew test 실행
→ 실패
→ 실패 로그를 AI에게 전달
→ 코드 수정
→ 다시 ./gradlew test 실행
```

즉, Harness는 “맞는지 검사하는 장치”이고, Feedback Loop는 “틀리면 다시 고치게 하는 반복 구조”다.

## 8.3 좋은 Harness의 조건

좋은 Harness는 다음 조건을 만족해야 한다.

첫째, 자동 실행 가능해야 한다.  
사람이 수동으로 여러 단계를 눌러야 한다면 AI가 반복 수행하기 어렵다.

둘째, 성공과 실패가 명확해야 한다.  
테스트 결과가 애매하면 AI가 무엇을 고쳐야 하는지 판단하기 어렵다.

셋째, 실패 로그가 충분히 구체적이어야 한다.  
단순히 “테스트 실패”가 아니라 어떤 테스트가 왜 실패했는지 알 수 있어야 한다.

넷째, 너무 느리지 않아야 한다.  
Feedback Loop는 반복이 핵심이기 때문에 Harness가 너무 느리면 개발 속도가 떨어진다.

다섯째, 중요한 비즈니스 규칙을 검증해야 한다.  
단순히 컴파일만 되는지는 부족하다. Spec에서 정의한 핵심 동작이 테스트로 검증되어야 한다.

## 8.4 Spring 프로젝트에서의 Harness 예시

Spring 백엔드 프로젝트에서는 보통 다음과 같은 Harness를 구성할 수 있다.

```text
harness/
├── README.md
├── test.sh
├── build.sh
└── api-test.http
```

`test.sh`는 전체 테스트를 실행한다.

```bash
./gradlew test
```

`build.sh`는 빌드까지 확인한다.

```bash
./gradlew clean build
```

`api-test.http`는 IntelliJ HTTP Client나 REST Client에서 사용할 수 있는 API 호출 예시를 담을 수 있다.

하지만 중요한 것은 파일 구조가 아니라, AI가 “변경 후 반드시 무엇을 실행해야 하는지”를 알 수 있어야 한다는 점이다. 그래서 `AGENTS.md`나 Context 문서에 Harness 실행 규칙을 명시하는 것이 좋다.

예를 들어 다음과 같이 적을 수 있다.

```markdown
## Harness

기능 변경 후 반드시 다음 명령을 실행한다.

1. ./harness/test.sh
2. ./harness/build.sh

테스트 실패 시 실패 로그를 분석하고, 수정 후 다시 실행한다.
```

## 8.5 Harness의 핵심 철학

Harness Engineering의 핵심은 AI를 신뢰하지 않는 데 있다.  
AI를 불신한다는 의미가 아니라, 소프트웨어 개발에서는 결과를 검증해야 한다는 뜻이다.

좋은 개발팀은 사람 개발자가 만든 코드도 테스트한다.  
그렇다면 AI가 만든 코드는 더욱 명확한 검증 기준이 필요하다.

따라서 Harness는 AI 개발에서 선택이 아니라 필수다.

```text
AI가 코드를 만들 수 있다.
하지만 그 코드가 맞는지는 Harness가 판단해야 한다.
```

## 8.6 Lint, Formatter, Test는 왜 Harness인가

Harness를 테스트 코드로만 이해하면 범위가 좁아진다.  
AI 주도 개발에서 Harness는 AI가 만든 결과를 자동으로 판정하는 모든 장치를 의미한다.

따라서 다음은 모두 Harness에 포함될 수 있다.

- Unit Test
- Integration Test
- API Test
- Build
- Lint
- Formatter Check
- Static Analysis
- Type Check
- Security Scan
- Docker Compose 기반 실행 검증
- CI Pipeline

예를 들어 lint는 코드 스타일과 잠재 오류를 자동으로 잡아준다.  
formatter는 코드 형식을 일관되게 맞춘다.  
test는 기능이 기대대로 동작하는지 검증한다.  
build는 전체 프로젝트가 컴파일 가능한지 확인한다.

즉, 이들은 모두 AI에게 다음과 같은 피드백을 제공한다.

```text
네가 만든 결과는 아직 프로젝트 기준을 통과하지 못했다.
어디가 실패했는지 로그를 보고 수정해라.
```

이 관점에서 lint와 formatter는 단순 코드 스타일 도구가 아니라 AI가 벗어나면 안 되는 레일이다.

## 8.7 Harness는 테스트 코드만 의미하지 않는다

Harness의 핵심은 “검증 가능성”이다.  
테스트 코드가 가장 대표적인 Harness이지만, 테스트 코드만 Harness인 것은 아니다.

AI가 만든 변경을 검증할 수 있다면 그것은 Harness가 될 수 있다.

예를 들어 다음 질문에 자동으로 답할 수 있어야 한다.

```text
- 컴파일이 되는가?
- 테스트가 통과하는가?
- API 응답 형식이 맞는가?
- lint 규칙을 통과하는가?
- DB 마이그레이션이 정상 실행되는가?
- 컨테이너가 정상 기동되는가?
- 보안 스캔에 걸리는 secret이 없는가?
```

AI 주도 개발이 위험해지는 순간은 AI가 코드를 많이 만들 때가 아니다.  
AI가 만든 코드를 검증하지 않고 받아들이는 순간이다.

따라서 Harness는 AI 주도 개발에서 브레이크와 계기판 역할을 한다.

```text
브레이크 = 잘못된 변경을 멈춘다.
계기판 = 어디가 잘못되었는지 알려준다.
```

---

# 9. Feedback Loop

Feedback Loop는 AI 주도 개발의 생산성을 크게 높이는 핵심 구조다.

AI는 한 번에 완벽한 결과를 만들지 못한다.  
하지만 실패를 보고 수정하는 작업은 매우 잘할 수 있다. 특히 테스트 실패 로그, 컴파일 에러, lint 오류처럼 구체적인 피드백이 주어지면 AI는 이를 기반으로 코드를 수정할 수 있다.

## 9.1 Feedback Loop의 기본 구조

Feedback Loop는 다음과 같은 흐름이다.

```text
Generate
→ Evaluate
→ Feedback
→ Fix
→ Repeat
```

이를 개발 상황에 맞게 표현하면 다음과 같다.

```text
AI가 코드 생성
→ 테스트 실행
→ 실패 로그 수집
→ AI가 원인 분석
→ 코드 수정
→ 다시 테스트 실행
```

이 구조가 반복되면 AI는 점점 정답에 가까운 결과를 만들 수 있다.

## 9.2 Feedback Loop가 중요한 이유

AI는 처음부터 모든 맥락을 완벽히 반영하지 못할 수 있다.  
예를 들어 테스트를 빠뜨리거나, 메서드명을 잘못 사용하거나, 기존 ErrorCode를 사용하지 않고 새로 만들 수도 있다.

하지만 Harness가 실패를 알려주고, 그 실패 정보를 AI에게 다시 제공하면 AI는 수정할 수 있다.

즉, AI 주도 개발의 핵심은 “AI가 한 번에 맞히는 것”이 아니라 “틀렸을 때 빠르게 고치는 것”이다.

## 9.3 Feedback Loop와 사람의 역할

Feedback Loop가 있다고 해서 사람이 필요 없어지는 것은 아니다.  
사람은 여전히 다음을 확인해야 한다.

- AI가 Spec을 잘못 해석하지 않았는가?
- 테스트는 진짜 의미 있는가?
- 보안상 위험한 코드는 없는가?
- 트랜잭션 경계가 적절한가?
- DB 변경이 안전한가?
- 운영 중인 API 호환성을 깨지 않는가?

AI와 Harness는 많은 기계적 검증을 처리할 수 있지만, 제품 방향과 설계 판단은 여전히 개발자의 영역이다.

## 9.4 Feedback Loop의 한계

Feedback Loop는 테스트가 있는 범위 안에서만 강력하다.  
테스트가 부실하면 AI는 테스트를 통과하는 잘못된 코드를 만들 수도 있다.

이를 “테스트에 과적합된 코드”라고 볼 수 있다.  
AI가 실제 요구사항을 만족하지 않아도 테스트만 통과하면 성공으로 판단될 수 있다.

따라서 Feedback Loop의 품질은 Harness의 품질에 의존한다.

```text
좋은 Harness 없이 좋은 Feedback Loop는 없다.
```

---

# 10. Agent Orchestration

Agent Orchestration은 여러 Agent를 역할별로 나누고, 이들을 순서에 맞게 실행하는 구조다.

AI에게 “회원가입 기능 만들어줘”라고 한 번에 맡길 수도 있다. 하지만 복잡한 작업일수록 한 Agent가 모든 일을 처리하는 것보다 역할을 나누는 편이 더 안정적이다.

## 10.1 왜 Agent를 나누는가

소프트웨어 개발은 하나의 작업처럼 보이지만 실제로는 여러 종류의 사고가 섞여 있다.

예를 들어 회원가입 기능을 만든다고 해도 다음 작업이 필요하다.

- 요구사항 분석
- API 설계
- Entity/DTO 설계
- 비즈니스 로직 구현
- 예외 처리
- 테스트 작성
- 보안 검토
- 코드 리뷰

사람 개발팀에서도 한 사람이 모든 역할을 할 수 있지만, 역할을 분리하면 더 안정적인 결과를 얻을 수 있다. AI도 마찬가지다.

## 10.2 대표적인 Agent 역할

Planner Agent는 요구사항을 분석하고 구현 계획을 만든다.  
이 Agent는 코드를 바로 쓰기보다 영향 범위, 필요한 클래스, 변경 순서를 정리한다.

Coder Agent는 실제 코드를 작성한다.  
Planner가 만든 계획을 기반으로 Controller, Service, DTO, Repository 등을 수정한다.

Reviewer Agent는 코드 품질을 검토한다.  
아키텍처 위반, 보안 문제, 테스트 누락, 불필요한 복잡성 등을 점검한다.

Tester Agent는 테스트 케이스를 만든다.  
Spec을 기반으로 성공 케이스와 실패 케이스를 테스트로 표현한다.

Fixer Agent는 실패 로그를 보고 코드를 수정한다.  
컴파일 에러, 테스트 실패, validation 누락 등을 해결한다.

## 10.3 Orchestration의 핵심

Agent Orchestration의 핵심은 Agent를 많이 만드는 것이 아니다.  
중요한 것은 어떤 순서로, 어떤 기준으로, 어떤 결과를 다음 단계에 넘길 것인가이다.

좋은 Orchestration은 다음과 같은 흐름을 가진다.

```text
Spec 읽기
→ Planner가 계획 작성
→ 사람이 계획 승인
→ Coder가 구현
→ Tester가 테스트 작성
→ Harness 실행
→ 실패 시 Fixer가 수정
→ Reviewer가 최종 검토
```

복잡한 작업에서는 “바로 구현”보다 “계획 먼저”가 중요하다.  
AI에게 바로 코드를 쓰게 하면 전체 구조를 잘못 잡은 상태로 많은 파일을 수정할 수 있다. 반면 Planner가 먼저 영향 범위를 정리하고, 사람이 이를 확인한 뒤 구현하면 위험이 줄어든다.

## 10.4 Agent를 많이 만드는 것보다 중요한 것

Agent를 많이 만든다고 AI 주도 개발이 좋아지는 것은 아니다.  
오히려 역할이 불명확한 Agent가 많아지면 결과가 더 혼란스러워질 수 있다.

중요한 것은 Agent의 개수가 아니라 다음 기준이다.

```text
- 역할이 명확한가?
- 입력이 명확한가?
- 출력이 명확한가?
- 다음 단계로 넘길 결과물이 명확한가?
- 실패했을 때 누가 수정하는지 명확한가?
- 사람이 승인해야 하는 지점이 명확한가?
```

예를 들어 Planner와 Coder의 역할이 분리되어 있지 않으면 AI가 계획을 세우기도 전에 바로 코드를 수정할 수 있다.  
Reviewer와 Fixer의 역할이 섞이면 검토해야 할 Agent가 임의로 코드를 바꾸면서 책임 경계가 흐려질 수 있다.

따라서 Agent 설계의 핵심은 역할 분리다.

```text
좋은 Agent 설계 = 많은 Agent가 아니라 책임이 선명한 Agent
```

## 10.5 Agent 설계에서 역할·입력·출력·검증 기준

Agent를 설계할 때는 최소한 다음 네 가지를 정의해야 한다.

### 1. Role

이 Agent가 어떤 역할인지 정의한다.

예시는 다음과 같다.

```text
Planner Agent는 구현 계획만 작성한다.
Coder Agent는 승인된 계획에 따라 코드만 수정한다.
Reviewer Agent는 코드 변경의 위험과 규칙 위반만 검토한다.
```

### 2. Input

Agent가 어떤 입력을 받아야 하는지 정의한다.

예시는 다음과 같다.

```text
Planner Input:
- 기능 Spec
- 관련 Context 문서
- 현재 프로젝트 구조

Coder Input:
- 승인된 Plan
- 관련 파일 목록
- 구현 제한 사항

Reviewer Input:
- 변경 diff
- Spec
- 프로젝트 규칙
```

### 3. Output

Agent가 어떤 결과를 내야 하는지 정의한다.

예시는 다음과 같다.

```text
Planner Output:
- 변경 대상 파일
- 구현 순서
- 위험 요소
- 테스트 전략

Coder Output:
- 수정된 코드
- 변경 요약
- 실행한 Harness 결과

Reviewer Output:
- 통과 여부
- 위반 사항
- 수정 권고
```

### 4. Validation

Agent 결과를 어떻게 검증할지 정의한다.

예시는 다음과 같다.

```text
- Plan은 사람이 승인한다.
- Code는 테스트와 빌드로 검증한다.
- Review는 체크리스트로 검증한다.
```

Agent 설계는 결국 작은 개발 프로세스를 설계하는 일이다.

---

# 11. Codex 계열 도구와 Oh My Codex의 위치

Oh My Codex 같은 도구는 AI 주도 개발 자체가 아니다.  
AI 주도 개발을 편하게 수행하도록 도와주는 자동화 도구에 가깝다.

비유하자면 다음과 같다.

```text
AI 주도 개발 = 개발 방법론
Oh My Codex = 그 방법론을 자동화하는 도구
```

## 11.1 Oh My Codex가 해주는 일

Oh My Codex 같은 도구는 보통 다음 영역을 자동화한다.

- Agent 실행
- Workflow 실행
- 반복 루프 관리
- 실패 시 재시도
- 여러 작업자 병렬 실행
- 프롬프트 템플릿 제공
- 작업 흐름 표준화

예를 들어 사람이 수동으로 다음과 같이 할 수 있다.

```text
1. 계획 세워줘
2. 이 계획으로 코드 작성해줘
3. 테스트 작성해줘
4. 테스트 실행해줘
5. 실패 로그 보고 수정해줘
```

Oh My Codex 같은 도구는 이 흐름을 하나의 명령으로 자동화한다.

## 11.2 Oh My Codex가 해주지 않는 일

중요한 것은 Oh My Codex가 모든 것을 대신하지 않는다는 점이다.

Oh My Codex는 Context를 대신 작성해주지 않는다.  
Spec을 대신 정의해주지 않는다.  
비즈니스적으로 무엇이 맞는지 판단해주지 않는다.  
보안상 허용되는 변경인지 최종 책임져주지 않는다.  
테스트가 의미 있는지 판단하는 것도 결국 개발자의 몫이다.

도구는 실행을 자동화하지만 기준은 개발자가 제공해야 한다.

```text
도구는 속도를 높인다.
Context, Spec, Harness는 방향과 안전성을 제공한다.
```

## 11.3 도구가 강력해질수록 기준 문서가 중요해진다

도구가 파일을 직접 수정하고 명령어를 실행할 수 있게 되면 생산성은 크게 올라간다.  
하지만 동시에 잘못된 변경도 더 빠르게 커질 수 있다.

따라서 AI 개발 도구가 강력해질수록 다음 문서가 더 중요해진다.

- `AGENTS.md`
- `docs/context/*`
- `docs/specs/*`
- `harness/*`
- 테스트 코드
- 변경 이력 문서

도구가 강력할수록 “무엇을 해도 되는지”와 “무엇을 하면 안 되는지”를 더 명확히 해야 한다.

## 11.4 Codex 계열 도구를 이해하는 정확한 관점

Codex 계열 도구나 Oh My Codex 같은 자동화 도구를 이해할 때 중요한 점은, 이것들이 AI 주도 개발의 본질이 아니라 실행 환경이라는 점이다.

도구는 다음을 도와준다.

```text
- 파일을 읽는다.
- 파일을 수정한다.
- 명령어를 실행한다.
- 테스트를 돌린다.
- 실패 로그를 다시 모델에게 전달한다.
- Agent와 Skill 설정을 로딩한다.
- 반복 작업을 자동화한다.
```

하지만 도구가 자동으로 해결하지 못하는 것도 있다.

```text
- 우리 서비스의 비즈니스 규칙
- 좋은 아키텍처 판단
- 운영 환경의 제약
- 보안상 허용되지 않는 변경
- 무엇이 제품적으로 맞는 결과인지
- 어떤 테스트가 의미 있는지
```

따라서 정확한 관점은 다음과 같다.

```text
Codex 계열 도구 = AI 주도 개발을 실행하기 위한 자동화 런타임
AI 주도 개발 능력 = Context, Spec, Harness, Workflow를 설계하는 능력
```

## 11.5 도구 설치와 AI 주도 개발 능력은 다르다

AI 개발 도구를 설치하면 당장 파일 수정과 코드 생성은 가능하다.  
하지만 그것만으로 AI 주도 개발을 잘한다고 보기는 어렵다.

도구만 설치된 상태는 다음과 같다.

```text
AI는 움직일 수 있다.
하지만 어디로 가야 하는지, 무엇을 지켜야 하는지, 언제 성공인지 모른다.
```

AI 주도 개발이 되려면 최소한 다음이 필요하다.

```text
Context: 프로젝트의 규칙과 구조
Spec: 이번 기능의 정답 기준
Harness: 결과를 판정하는 검증 장치
Workflow: 어떤 순서로 일할지에 대한 절차
Feedback Loop: 실패를 기반으로 다시 수정하는 반복 구조
```

즉, 도구 설치는 출발점일 뿐이다.

```text
도구는 실행력을 제공한다.
Context, Spec, Harness는 방향과 기준을 제공한다.
```

---

# 12. AI 주도 개발에서 프로젝트 문서는 어떻게 관리해야 하는가

AI 주도 개발에서 문서는 단순한 참고 자료가 아니다.  
AI가 프로젝트를 이해하고 작업 기준으로 삼는 실행 자산이다.

따라서 문서는 “사람이 나중에 읽기 위해 남겨두는 것”을 넘어 “AI가 반복적으로 읽고 일관된 결과를 만들기 위해 사용하는 것”으로 관리해야 한다.

## 12.1 문서의 계층

실무에서는 문서를 다음 계층으로 나누는 것이 좋다.

```text
AGENTS.md
→ docs/context
→ docs/specs
→ harness
→ history
```

각 계층의 역할은 다음과 같다.

| 문서 | 역할 |
|---|---|
| `AGENTS.md` | AI가 항상 지켜야 할 최상위 공통 규칙 |
| `docs/context` | 프로젝트 전체 구조와 개발 규칙 |
| `docs/specs` | 기능별 정답 기준 |
| `harness` | 검증 명령과 실행 방법 |
| `history` | 변경 이력과 의사결정 기록 |

## 12.2 문서는 최신성이 중요하다

AI는 오래된 문서와 최신 문서를 구분하지 못할 수 있다.  
문서가 여러 개 충돌하면 AI는 그중 그럴듯한 것을 골라 따를 수 있고, 이때 잘못된 결과가 나온다.

따라서 다음 원칙이 중요하다.

```text
현재 기준 문서는 하나로 유지한다.
과거 기준은 history에 보관한다.
문서와 코드가 달라지면 문서를 먼저 업데이트한다.
```

## 12.3 AI용 문서는 명령형으로 작성하는 것이 좋다

AI에게 제공할 문서는 추상적인 설명보다 명확한 규칙이 좋다.

나쁜 예시는 다음과 같다.

```text
가능하면 깔끔하게 작성한다.
```

좋은 예시는 다음과 같다.

```text
Controller에서는 Entity를 직접 반환하지 않는다.
Response DTO를 사용한다.
비즈니스 예외는 CustomException과 ErrorCode enum으로 표현한다.
```

AI는 명확한 금지와 허용 기준이 있을수록 안정적인 결과를 만든다.

---

# 13. ERD, API 명세, 보안 정보는 어디까지 공유해야 하는가

AI 주도 개발에서 ERD, API 명세, 도메인 규칙은 중요한 Context다.  
하지만 모든 정보를 그대로 공유하면 안 된다. 특히 보안 정보와 실제 개인정보는 반드시 제외해야 한다.

## 13.1 공유해도 되는 정보

다음 정보는 보통 Context로 제공해도 된다.

- 테이블 구조
- 컬럼명
- 관계
- enum 값
- 인덱스 의도
- API endpoint
- Request/Response 구조
- ErrorCode 목록
- 인증 방식의 개념
- 권한 정책의 구조

예를 들어 ERD는 AI가 도메인 관계를 이해하는 데 매우 유용하다.  
사용자, 주문, 결제, 권한, 토큰, 로그 테이블의 관계를 알아야 AI가 Repository나 Query를 잘 작성할 수 있다.

## 13.2 공유하면 안 되는 정보

다음 정보는 Context에 포함하면 안 된다.

- 실제 운영 DB 데이터
- 실제 사용자 개인정보
- 실제 비밀번호
- JWT secret
- API key
- OAuth client secret
- DB 접속 비밀번호
- Redis 비밀번호
- AWS access key
- 운영 서버 IP와 민감 포트 정보

AI에게 필요한 것은 구조이지 비밀값이 아니다.

## 13.3 민감정보는 placeholder로 대체한다

필요하다면 실제 값 대신 placeholder를 사용한다.

```yaml
spring:
  datasource:
    url: jdbc:mysql://<DB_HOST>:3306/<DB_NAME>
    username: <DB_USERNAME>
    password: <DB_PASSWORD>

jwt:
  secret: <JWT_SECRET>
```

이렇게 하면 AI는 설정 구조를 이해할 수 있지만, 실제 secret은 노출되지 않는다.

## 13.4 보안 정보는 원칙 중심으로 공유한다

예를 들어 JWT 인증 구조를 설명할 때 실제 secret을 줄 필요는 없다.  
대신 다음처럼 원칙을 설명하면 된다.

```text
- Access Token과 Refresh Token을 분리한다.
- Refresh Token은 Redis에 저장한다.
- 로그아웃 시 Redis에 저장된 Refresh Token을 삭제한다.
- Access Token blacklist 또는 token version 전략을 사용한다.
- 인증된 사용자 정보는 CustomPrincipal로 관리한다.
```

즉, AI에게는 “어떤 보안 구조를 따라야 하는가”를 알려주고, 실제 비밀값은 제공하지 않는다.

---

# 14. Spring 백엔드 개발자 관점에서의 적용 방식

Spring 백엔드 개발자는 AI 주도 개발을 특히 잘 적용할 수 있다.  
Spring 프로젝트는 계층 구조, DI, 테스트, 빌드, 패키지 구조가 비교적 명확하기 때문에 Context와 Harness를 만들기 좋다.

## 14.1 Spring 프로젝트에서 Context에 넣을 내용

첫째, 계층 책임이다.

```text
Controller는 요청과 응답 변환을 담당한다.
Service는 비즈니스 로직을 담당한다.
Repository는 DB 접근을 담당한다.
Entity는 DB 모델이며 API 응답으로 직접 반환하지 않는다.
```

둘째, DTO 규칙이다.

```text
Request DTO와 Response DTO를 분리한다.
Entity를 외부 응답으로 직접 노출하지 않는다.
```

셋째, 예외 처리 규칙이다.

```text
비즈니스 예외는 CustomException을 사용한다.
ErrorCode enum을 기반으로 응답한다.
ControllerAdvice에서 공통 처리한다.
```

넷째, 인증 규칙이다.

```text
JWT 기반 인증을 사용한다.
인증된 사용자 정보는 CustomPrincipal로 관리한다.
```

다섯째, 트랜잭션 규칙이다.

```text
쓰기 작업은 Service 계층에서 @Transactional을 사용한다.
읽기 작업은 readOnly 트랜잭션을 고려한다.
```

## 14.2 Spring 프로젝트에서 Spec 작성 시 중요한 점

Spring API Spec에서는 단순히 endpoint만 적으면 부족하다.  
다음까지 정의해야 한다.

- validation 실패 시 어떤 예외가 발생하는가
- 인증이 필요한 API인가
- 어떤 role이 접근 가능한가
- 트랜잭션이 필요한가
- 중복 요청은 어떻게 처리하는가
- DB에 어떤 side effect가 생기는가
- 기존 응답 형식과 호환되는가

AI에게 API를 만들게 할 때 이런 내용이 없으면 AI는 일반적인 CRUD API를 만든다.  
하지만 실무 API는 일반 CRUD보다 예외와 정책이 훨씬 중요하다.

## 14.3 Spring 프로젝트에서 Harness 구성

Spring에서는 다음 테스트가 Harness의 핵심이 될 수 있다.

- Service 단위 테스트
- Repository 테스트
- MockMvc 기반 Controller 테스트
- 통합 테스트
- Security 테스트
- build/test Gradle 명령

처음부터 모든 Harness를 완벽하게 만들 필요는 없다.  
작은 기능부터 다음 정도만 있어도 AI 개발 품질이 크게 좋아진다.

```text
./gradlew test
./gradlew clean build
```

그리고 중요한 API부터 테스트 케이스를 추가하면 된다.

---

# 15. 흔한 오해와 실패 패턴

AI 주도 개발을 처음 적용할 때 자주 발생하는 오해와 실패 패턴이 있다.

## 15.1 “AI가 알아서 해줄 것이다”라는 오해

AI는 알아서 프로젝트 규칙을 이해하지 않는다.  
알려준 것과 읽을 수 있는 것 안에서만 판단한다.

Context가 없으면 AI는 일반적인 예제를 기준으로 코드를 만든다.  
Spec이 없으면 AI는 요구사항을 추측한다.  
Harness가 없으면 AI는 자신의 결과가 맞는지 확인하지 못한다.

## 15.2 “프롬프트만 잘 쓰면 된다”는 오해

프롬프트는 중요하지만 충분하지 않다.  
실무에서는 프롬프트보다 지속적으로 참조 가능한 Context, Spec, Harness가 더 중요하다.

프롬프트는 순간의 지시다.  
Context는 프로젝트의 기준이다.

## 15.3 “Agent를 많이 만들면 좋아진다”는 오해

Agent가 많다고 좋은 구조가 되는 것은 아니다.  
역할이 겹치고, 입력과 출력이 불명확하면 오히려 복잡성만 증가한다.

중요한 것은 Agent의 수가 아니라 책임의 선명함이다.

## 15.4 “테스트만 통과하면 된다”는 오해

테스트가 부실하면 AI는 테스트만 통과하는 잘못된 코드를 만들 수도 있다.  
Harness는 중요하지만, Harness 자체의 품질도 중요하다.

따라서 사람 개발자는 테스트가 실제 비즈니스 요구사항을 제대로 검증하는지 확인해야 한다.

## 15.5 “도구 설치가 곧 AI 주도 개발이다”라는 오해

Codex 계열 도구나 Oh My Codex를 설치했다고 해서 AI 주도 개발이 완성되는 것은 아니다.

도구 설치는 시작일 뿐이다.  
Context, Spec, Harness가 없으면 도구는 단순 자동 실행기에 불과하다.

## 15.6 AI 도구를 모델로 오해하는 문제

AI 주도 개발을 처음 접할 때 흔한 오해는 “Codex가 곧 AI 모델이다” 또는 “Claude Code가 곧 모델이다”라고 생각하는 것이다.

하지만 실무 관점에서는 다음을 분리해야 한다.

```text
Model: 생성 능력
Agent: 역할을 가진 실행자
Tool/CLI/IDE: 파일과 명령을 다루는 실행 환경
Workflow: 여러 작업 단계를 묶은 흐름
```

이 구분이 흐려지면 다음과 같은 문제가 생긴다.

- 도구만 바꾸면 개발 방식이 좋아질 것이라고 기대한다.
- Agent 설정을 추가하면 Context 문제가 해결될 것이라고 생각한다.
- 테스트가 없는데도 자동화 도구가 알아서 안전하게 개발해줄 것이라고 믿는다.

그러나 AI 주도 개발의 핵심은 도구 이름이 아니라 구조다.

## 15.7 Context 없이 Agent만 늘리는 문제

Agent를 여러 개 두면 그럴듯해 보인다.  
Planner, Coder, Reviewer, Tester가 있으면 마치 AI 개발팀이 생긴 것처럼 느껴진다.

하지만 Context가 없으면 모든 Agent가 같은 문제를 반복한다.

```text
Planner는 잘못된 기준으로 계획한다.
Coder는 프로젝트 규칙과 맞지 않는 코드를 만든다.
Tester는 의미 없는 테스트를 작성한다.
Reviewer는 무엇을 기준으로 리뷰해야 하는지 모른다.
```

즉, Agent는 Context를 대체하지 못한다.

정확한 순서는 다음과 같다.

```text
먼저 Context를 정리한다.
그 다음 Spec을 작성한다.
그 다음 Harness를 만든다.
그 다음 Agent와 Workflow를 자동화한다.
```

Agent 자동화는 기반이 있을 때 강력하다.  
기반이 없으면 잘못된 작업을 더 빠르게 반복할 뿐이다.

## 15.8 Harness 없이 자동화부터 하는 문제

AI 주도 개발에서 가장 위험한 패턴은 검증 장치 없이 자동화부터 도입하는 것이다.

예를 들어 다음 흐름은 위험하다.

```text
AI에게 큰 기능을 맡긴다.
여러 파일을 자동 수정하게 한다.
테스트나 빌드는 실행하지 않는다.
사람이 diff를 대충 보고 병합한다.
```

이 경우 AI는 빠르게 많은 코드를 만들 수 있지만, 그 코드가 맞는지는 확인되지 않는다.

Harness 없이 자동화하면 다음 문제가 생긴다.

- 컴파일 에러가 뒤늦게 발견된다.
- 기존 기능이 깨진다.
- API 응답 형식이 달라진다.
- 보안 규칙을 위반한다.
- 테스트가 없어서 회귀 버그를 놓친다.
- AI가 만든 코드를 사람이 전부 수동 검토해야 한다.

따라서 자동화보다 먼저 준비해야 하는 것은 Harness다.

```text
자동화는 속도를 높인다.
Harness는 안전성을 확보한다.
속도보다 안전성이 먼저다.
```

---

# 16. 단계별 도입 전략

AI 주도 개발은 한 번에 완성하려고 하면 오히려 실패하기 쉽다.  
작게 시작해서 점진적으로 발전시키는 것이 좋다.

## 16.1 1단계: Context 정리

먼저 프로젝트의 기본 규칙을 문서화한다.

- 기술 스택
- 패키지 구조
- 응답 형식
- 예외 처리
- 인증 방식
- 테스트 실행 명령

이 단계만 해도 AI 결과가 훨씬 안정된다.

## 16.2 2단계: 작은 기능 Spec 작성

회원가입, 로그인, 계정 잠금 같은 작은 기능 하나를 선택해 Spec을 작성한다.  
처음부터 모든 기능을 문서화하려고 하지 않는다.

## 16.3 3단계: Harness 연결

`./gradlew test` 같은 기본 검증 명령을 AI에게 명시한다.  
변경 후 반드시 실행하도록 규칙을 둔다.

## 16.4 4단계: 수동 Feedback Loop 경험

처음에는 Oh My Codex 같은 자동화 도구 없이 직접 반복해 보는 것이 좋다.

```text
AI에게 구현 요청
→ 테스트 실행
→ 실패 로그 전달
→ 수정 요청
```

이 과정을 직접 경험하면 AI 개발 구조가 몸에 익는다.

## 16.5 5단계: Workflow 자동화 도입

흐름이 익숙해지면 Oh My Codex 같은 도구로 반복 작업을 자동화한다.  
이때 도구는 이미 이해한 흐름을 편하게 만들어주는 수단이 된다.

## 16.6 6단계: Agent 역할 분리

작업이 커지고 반복이 많아지면 Agent를 역할별로 나눈다.

```text
Planner → Coder → Tester → Fixer → Reviewer
```

처음부터 모든 Agent를 만들 필요는 없다.  
가장 먼저 분리할 만한 것은 보통 Planner와 Coder다.

## 16.7 7단계: 사람 승인 지점 정의

자동화가 강해질수록 사람의 승인 지점을 명확히 해야 한다.

예를 들어 다음 단계에서는 사람 승인을 요구할 수 있다.

- 구현 계획 확정 전
- DB 스키마 변경 전
- 보안 관련 코드 변경 전
- 외부 API 계약 변경 전
- 운영 배포 전

AI 주도 개발은 사람을 제거하는 것이 아니라, 사람이 더 중요한 판단에 집중하도록 만드는 구조다.

---

# 17. 최종 정리

AI 주도 개발은 도구 이름으로 정의되지 않는다.  
Codex를 쓰든, Claude를 쓰든, Gemini를 쓰든 핵심은 같다.

```text
좋은 Context를 제공한다.
명확한 Spec을 작성한다.
신뢰할 수 있는 Harness를 만든다.
실패를 기반으로 Feedback Loop를 돌린다.
필요하면 Agent와 Workflow로 자동화한다.
```

AI 주도 개발의 핵심 공식은 다음과 같다.

```text
AI 결과 품질 = Context × Spec × Harness
```

그리고 생산성은 다음에 의해 결정된다.

```text
AI 생산성 = Workflow 자동화 × Feedback Loop 속도
```

그동안 정리한 내용을 종합하면 AI 주도 개발은 다음 한 문장으로 다시 표현할 수 있다.

```text
AI 주도 개발은 AI에게 코드를 맡기는 기술이 아니라,
AI가 안전하게 일할 수 있는 개발 시스템을 설계하는 능력이다.
```

그리고 그 시스템은 다음 요소로 구성된다.

```text
Context = 프로젝트의 세계관과 제약 조건
Spec = 기능의 정답 기준
Harness = 결과의 판정기
Feedback Loop = 실패 기반 반복 개선 구조
Agent = 역할을 가진 AI 작업자
Skill = 특정 작업을 위한 전문 지침
Workflow = 작업 절차
Orchestrator = 전체 흐름 제어자
Tool = 이 구조를 실행하는 환경
```

최종적으로 개발자가 키워야 하는 능력은 다음이다.

```text
프롬프트를 잘 쓰는 능력에서 끝나지 않는다.
Context를 설계하고,
Spec을 명확히 쓰고,
Harness를 구축하고,
Agent와 Workflow를 필요한 만큼만 자동화하는 능력이 중요하다.
```

AI 주도 개발은 코드 작성 기술이 아니라 개발 시스템 설계 능력이다.  
AI가 빠르게 실행할수록 개발자는 더 명확한 기준을 만들어야 한다.

```text
좋은 AI 개발자는 AI에게 일을 많이 시키는 사람이 아니라,
AI가 틀린 방향으로 가지 않도록 구조를 설계하는 사람이다.
```
