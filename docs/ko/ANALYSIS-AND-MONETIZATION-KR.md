# claude-skills 전수조사 분석 및 수익화 전략 (한국어 정리)

> 작성일: 2026-10-11
> 대상 저장소: **https://github.com/bmshin94/claude-skills**
> 원본(upstream): **https://github.com/alirezarezvani/claude-skills**
> 라이선스: MIT (© 2025 Alireza Rezvani) — 상업적 이용·수정·재배포 허용
> 조사 방법: 로컬 클론 전수 스캔 (파일 카운트, 도구 실행, 매니페스트 파싱)

---

## 목차

1. [한 줄 정의](#1-한-줄-정의)
2. [실측 수치](#2-실측-수치)
3. [도메인별 분포](#3-도메인별-분포)
4. [스킬 1개의 해부도](#4-스킬-1개의-해부도)
5. [4개 레이어 설계](#5-4개-레이어-설계)
6. [특이한 메타 스킬](#6-특이한-메타-스킬)
7. [쉬운 설명 — 비유 3개](#7-쉬운-설명--비유-3개)
8. [설치 및 사용법](#8-설치-및-사용법)
9. [Skill vs Plugin vs MCP](#9-skill-vs-plugin-vs-mcp)
10. [API 토큰 필요 여부](#10-api-토큰-필요-여부)
11. [AI 에이전트 구축 활용도](#11-ai-에이전트-구축-활용도)
12. [React/PHP 구현 가능성](#12-reactphp-구현-가능성)
13. [유튜브 강의 제작 기획](#13-유튜브-강의-제작-기획)
14. [수익화 아이디어 10개](#14-수익화-아이디어-10개)
15. [실행 로드맵](#15-실행-로드맵)
16. [주의사항](#16-주의사항)

---

## 1. 한 줄 정의

> **"AI에게 입히는 직업별 전문가 복장 388벌 세트"**

실행되는 애플리케이션이 **아니라**, "지식 + 결정론적 도구 꾸러미" 라이브러리다.
사용자가 자신의 AI 워크플로에 추출·배포해서 쓰는 것이 전제다.

---

## 2. 실측 수치

직접 센 값 (저장소 README의 카운터와 일부 어긋남 — 실측값 기준으로 인용할 것).

| 항목 | 개수 | 비고 |
|---|---|---|
| SKILL.md (실제 스킬) | **388** | 멀티플랫폼 미러 폴더 제외 |
| Python 자동화 도구 | **745** | 거의 전부 표준 라이브러리 only |
| 마크다운 문서 | 3,104 | references 지식베이스 포함 |
| 마켓플레이스 플러그인 | **99** | 도메인 번들 + 개별 스킬 |
| 전문가 에이전트 (`cs-*`) | 232 | 페르소나 정의 |
| 슬래시 커맨드 (`/cs:*`) | 286 | 사용자 직접 호출 |
| 전체 용량 | 68MB | 코드보다 **문서가 본체** |
| 라이선스 | MIT | 상업적 이용 전면 허용 |

### 비표준 의존성 (실측)

```
745개 .py 중 네트워크 사용 파일:  10개
비표준 import 집계:  pandas 2회, requests 1회
→ 사실상 전부 stdlib only
```

---

## 3. 도메인별 분포

```
engineering          93개 스킬 / 236개 도구   ← 가장 두꺼움
engineering-team     53 /  90
marketing-skill      49 /  62
c-level-advisor      46 /  59
c-level-agents       22 /   0   (페르소나 전용)
ra-qm-team           19 /  38   (ISO 13485, MDR, FDA, GDPR)
product-team         17 /  22
productivity         12 /  37
research             10 /  25
project-management    9 /  15   (Atlassian MCP 번들)
compliance-os         9 /   4
commercial            8 /  21
business-operations   7 /  18
marketing             7 /  20
agent-launcher        6 /  20
markdown-html         5 /  15
finance               5 /  12
research-ops          5 /  24
business-growth       5 /   9
loop-library          1 /   0
```

### 플러그인 카테고리 (99개)

```
development 42 · productivity 11 · research 10 · leadership 9
marketing 6 · compliance 4 · product 4 · engineering 2 · finance 2
기타 각 1 (design, knowledge, operations, commercial,
           research-ops, documentation, project-management,
           business-growth, agent-development)
```

---

## 4. 스킬 1개의 해부도

`engineering/write-a-skill` 실측 구조:

```
write-a-skill/
├── .claude-plugin/
│   ├── plugin.json            # 설치 매니페스트 (확장 키 금지)
│   └── authoring-notes.json   # 출처/저작권 (Matt Pocock MIT 파생)
├── skills/write-a-skill/
│   ├── SKILL.md               # 본체. AI가 읽는 작업 지침서
│   ├── scripts/               # 결정론적 파이썬 검증기 3개
│   │   ├── skill_description_validator.py
│   │   ├── skill_structure_validator.py
│   │   └── skill_review_checklist_runner.py
│   └── references/            # 전문 지식 문서 4개
├── agents/cs-skill-author.md  # 성격·말투까지 정의된 페르소나
├── commands/cs-write-a-skill.md  # /cs:write-a-skill 호출 정의
└── README.md
```

실제 실행 결과:

```
$ python3 skill_description_validator.py --help
usage: skill_description_validator.py [-h] [--output {text,json}] [path]
  1. Description present
  2. Length <= 1024 characters
  3. Written in third person
  4. Has explicit trigger phrase: "Use when ..."
  5. First sentence describes what the skill does
Deterministic logic. No LLM calls. Stdlib only.
```

→ **LLM 호출 0회. 토큰 소모 없이 즉시 실행되는 진짜 프로그램.**

---

## 5. 4개 레이어 설계

```
① SKILL.md    — 무엇을 어떤 순서로 (AI가 읽음)
② scripts/    — 계산·검증·점수화 (파이썬 실행, 결정론적)
③ references/ — 업계 정설·논문·출처 (근거)
④ assets/     — 바로 쓰는 템플릿 (사용자가 채움)
        ↓                    ↓
   agents/ (성격)      commands/ (호출 버튼)
```

**설계 의도:** "AI가 지어내게 두지 말고, 계산은 파이썬이 하고 AI는 해석만 하라."

저장소 자체 원칙:
- `Algorithm over AI` — 결정론적 분석 > LLM 호출
- **안티패턴 명시**: "LLM calls in scripts (defeats portability and speed)"
- 유일한 공식 예외: `engineering/skillopt-sleep/backend.py` (옵트인, 기본값 `mock`)

### AI 단독 vs 이 레포 방식

| | AI 혼자 | 이 레포 |
|---|---|---|
| 방법 | 머릿속 추론 | `burn_runway_tracker.py` 실행 |
| 결과 | "대략 14개월 정도" | `runway_months: 13.7` |
| 재현성 | 물을 때마다 다름 | **항상 같은 값** |
| 근거 | 없음 | 계산식이 코드에 박혀 있음 |

→ **AI의 최대 약점(숫자 조작)을 구조적으로 제거한 것**이 핵심 가치.

---

## 6. 특이한 메타 스킬

| 스킬 | 하는 일 |
|---|---|
| `engineering/write-a-skill` | **스킬을 만드는 스킬.** 6개 품질검사 자동 실행 |
| `engineering/book-to-skill` | PDF/EPUB/DOCX 책 → 스킬 패키지 자동 변환 (저작권 게이트 내장) |
| `engineering/agent-memory` | Claude Code 훅으로 4단계 기억 사다리 (L0~L3) |
| `engineering/memory-engineering` | "기억이 아니라 **망각**을 설계하라" |
| `engineering/skillopt-sleep` | 밤새 세션 기록 분석 → 스킬 자동 개선 (MS SkillOpt 이식) |
| `engineering/skill-doctor` | 내 AI 사용 기록 채점 → 개선안 제시 |
| `engineering/agent-harness` | 어떤 도메인이든 목표→분해→검증→재시도 루프로 변환 |
| `agent-launcher` | Claude Managed Agent 자체를 만드는 스캐폴더 |
| `engineering/grill-me` | 내 계획을 끝까지 캐묻는 심문관 |
| `productivity/andreessen` | 마크 안드리센 모드 (아부 금지, 반론 먼저) |

### 멀티 플랫폼 미러링

`.codex/ .gemini/ .hermes/ .vibe/` 는 심볼릭 링크 미러.
Claude Code 전용이 아니라 **OpenAI Codex, Gemini CLI, Cursor, Mistral Vibe,
Hermes, OpenClaw 등 13개 에이전트**에서 동일 스킬 사용 가능.

---

## 7. 쉬운 설명 — 비유 3개

### 비유 1: 게임 캐릭터의 직업 전직서

```
기본 Claude  = 레벨 1 초보 (뭐든 조금씩)
   + 스킬 설치 = 전직서
   ↓
"CFO 모드"        = 재무제표 읽고 DCF 돌리고 런웨이 계산
"의료기기 RA 모드" = ISO 13485 / FDA 절차 숙지
"SEO 전문가 모드"  = 사이트맵 분석기 + E-E-A-T 점수기 장착
```

차이점: 일반 전직은 능력치만 오르지만, 이건
**전용 장비(파이썬 도구) + 교과서(references) + 양식지(assets)** 세트.

### 비유 2: 신입에게 주는 업무 인수인계 바인더

```
(A) 나쁜 방법: "알아서 잘 해봐"
    → AI에게 "마케팅 전략 짜줘" = 그럴듯하지만 근거 없는 답

(B) 이 레포 방식: 바인더를 건넴
    1장. 업무 순서    (SKILL.md)
    2장. 계산기       (scripts/)  — 숫자는 기계가 냄
    3장. 왜 이렇게    (references/) — 출처 7개 명시
    4장. 서식         (assets/)   — 빈칸만 채우면 됨
```

### 비유 3: Before / After

```
Before: "블로그 글 써줘"
        → 평범한 글 하나. 다음엔 품질이 다름.

After:  /cs:aeo 호출
        ① E-E-A-T 점수 측정 (0-100, 업종별 기준선)
        ② 부족 항목 지적
        ③ schema.org JSON-LD 자동 삽입
        ④ 인용 추적 원장 기록
        → 매번 같은 품질. 왜 그런지 설명 가능.
```

### 세 줄 요약

1. AI를 "말 잘하는 조수" → **"절차와 계산기를 가진 전문가"** 로 바꾸는 장비 세트
2. 388벌 중 **필요한 건 5~10개.** 나머지는 참고 교과서
3. MIT = **뜯어서 내 제품에 넣고 팔아도 합법**

---

## 8. 설치 및 사용법

### Claude Code (권장)

```bash
# 1단계: 마켓플레이스 등록 (최초 1회)
/plugin marketplace add bmshin94/claude-skills
#   원본: /plugin marketplace add alirezarezvani/claude-skills

# 2단계: 필요한 번들만 설치
/plugin install engineering-advanced-skills@claude-code-skills
/plugin install marketing-skills@claude-code-skills
/plugin install productivity-skills@claude-code-skills

# 3단계: 확인
/plugin list
```

> **경고:** 99개 전부 설치하지 말 것. 컨텍스트가 오염되고 AI가 엉뚱한
> 스킬을 고른다. **3~5개 번들이 적정선.**

### 사용법 3가지

```bash
# ① 자동 발동 (아무것도 안 해도 됨)
"이 PR 리뷰해줘"  →  AI가 알아서 code-review 스킬 로드

# ② 슬래시 커맨드 (가장 확실)
/cs:aeo https://mysite.com
/cs:cfo-review
/cs:grill-me "내 사업계획"
/cs:write-a-skill "유튜브 썸네일 분석기"

# ③ 파이썬 직접 실행 (AI 없이도 됨)
python3 marketing-skill/skills/aeo/scripts/aeo_audit.py --help
python3 finance/.../burn_runway_tracker.py --sample
```

### 다른 에이전트

| 도구 | 설치 명령 |
|---|---|
| OpenAI Codex | `./scripts/codex-install.sh` |
| Gemini CLI | `./scripts/gemini-install.sh` |
| Mistral Vibe | `./scripts/vibe-install.sh` |
| OpenClaw | `clawhub install alirezarezvani/claude-skills` |
| Cursor/Copilot/Windsurf 등 40종 | `npx agent-skills-cli add alirezarezvani/claude-skills` |

### 추천 시작 조합 (개발자)

```bash
/plugin install engineering-advanced-skills@claude-code-skills
/plugin install productivity-skills@claude-code-skills

# 먼저 써볼 것
/cs:grill-me "지금 만들려는 것"    # 계획을 끝까지 캐묻는 스킬
/cs:handoff                        # 세션 종료 전 인수인계 문서 생성
```

---

## 9. Skill vs Plugin vs MCP

| | **Skill** | **Plugin** | **MCP** |
|---|---|---|---|
| 정체 | 마크다운 지침서 | 스킬 배포 포장지 | 외부 서버 연결 규약 |
| 파일 | `SKILL.md` | `plugin.json` | `.mcp.json` |
| 비유 | **책 한 권** | **책 담은 상자** | **도서관 대출증** |
| 실행 | AI가 읽고 따라함 | 설치/업데이트 관리 | 네트워크로 외부 호출 |
| 이 레포 | 388개 ✅ | 99개 ✅ | **2개뿐** ⚠️ |

### 이 레포의 정확한 정체

```
본질 = Skill 라이브러리     (388개 SKILL.md)
포장 = Plugin 마켓플레이스   (99개 plugin.json)
MCP  = 거의 없음            (단 2개)
```

MCP가 있는 곳:

```json
// project-management/.mcp.json — 유일하게 의미있는 MCP
{ "mcpServers": { "atlassian": {
    "type": "sse", "url": "https://mcp.atlassian.com/v1/sse" }}}
// → Jira/Confluence 실시간 연동. OAuth는 Claude Code가 처리.

// 루트 .mcp.json — tessl (평가 도구)
```

**결론:** 이건 MCP 프로젝트가 **아니다.**
MCP = AI를 외부 시스템에 연결 / Skill = AI에게 지식과 절차 주입.
완전히 다른 층위이며, 경쟁이 아니라 **함께 쓰는 것**.

> MCP = 손 (외부에 닿음) / Skill = 뇌 (뭘 할지 앎)

---

## 10. API 토큰 필요 여부

### 결론: **아니요. 거의 전부 불필요.**

실측 검증:

```
$ grep -r "import requests|urllib.request|httpx" --include='*.py' | wc -l
10          ← 745개 중 10개만 네트워크 사용

$ grep "OPENAI_API_KEY|ANTHROPIC_API_KEY" --include='*.py'
(사실상 없음 — skillopt-sleep의 옵트인 백엔드가 유일)
```

| 상황 | 토큰 | 설명 |
|---|---|---|
| 파이썬 도구 직접 실행 | ❌ 불필요 | 완전 오프라인 |
| Claude Code에서 스킬 사용 | ⚠️ Claude 구독만 | 별도 API 키 아님 |
| Jira MCP 연동 | ❌ | OAuth, 키 입력 없음 |
| `skillopt-sleep` 실전 모드 | ✅ | **유일한 예외.** 기본값 `mock`(무과금) |
| `agent-launcher` 실제 런치 | ✅ | BYOK curl 생성. **키를 절대 출력 안 함** |
| SEO/AEO 사이트 분석 | ❌ | 공개 URL 크롤링 |

### 비용 구조

```
설치비     0원  (MIT, GitHub 무료)
도구 실행  0원  (로컬 파이썬)
AI 사용비  Claude 구독료만 (Pro $20/mo 등)
숨은 비용  없음 — 유료 서드파티 의존 금지가 레포 규칙
```

저장소 규칙 원문:
> "No paid/commercial service dependencies. Skills must not require
> paid third-party API keys or commercial services."

---

## 11. AI 에이전트 구축 활용도

### 결론: **이 레포에서 가장 가치 있는 부분이 정확히 이것.**

에이전트 구축 전용 서브 라이브러리가 통째로 들어있다.

### 직접 도움되는 스킬 7종

| 스킬 | 역할 |
|---|---|
| **`engineering/agent-harness`** | ⭐ 핵심. 목표→분해→실행→**기계 검증**→재시도→한계 시 사람 호출. 18개 도메인 매니페스트 동봉 |
| **`agent-launcher`** | Claude Managed Agent 스캐폴더. 18개 생성기, cron 배포 루프, `max_iterations ≤20` 강제 |
| **`engineering/agent-memory`** | 4단계 기억(L0 원본→L1 후보→L2 프로젝트→L3 영구). 승격 게이트: 3세션/2일 이상 반복 필요 |
| **`engineering/memory-engineering`** | 비용 프로파일러 + 망각정책 린터 |
| **`engineering/agenthub`** | 멀티 에이전트 오케스트레이션 |
| **`engineering/write-a-skill`** | 내 에이전트의 스킬을 만드는 메타 스킬 |
| **`loop-library`** | 에이전트 루프 정지조건 패턴집 |

### 그대로 베껴 쓸 수 있는 설계 패턴 5개

```
① 검증 연극 방지 (verification theater)
   loop_controller.py 가 subprocess로 검사를 직접 실행.
   "에이전트가 검증했다고 말만 하는 것"을 구조적으로 차단.

② 종료 조건 강제
   모든 미검증 태스크가 닫히기 전엔 close 거부. max_iterations 상한.

③ 승격은 주장이 아니라 증거로
   agent-memory: 3세션 + 2일 이상 반복 관찰 없으면 기억 승격 불가.

④ 거부가 기본값
   도구가 애매하면 답을 내지 않고 exit 3/4 로 "되묻는 질문" 출력.
   예: goal_compiler.py — 모호한 목표는 exit 3 + forcing questions

⑤ 원자적 상태 쓰기
   os.replace() 로 JSON 상태 저장 — 중간 크래시에도 손상 없음.
```

### 적용 시나리오

```
1. agent-harness 의 loop_controller.py 를 뼈대로 가져옴
2. memory-engineering 의 forgetting_policy_linter.py 로 기억 정책 검증
3. write-a-skill 로 도메인 스킬 작성
4. skill-doctor 로 실사용 로그 채점 → 개선
→ "에이전트 프레임워크 처음부터 설계" 단계를 통째로 건너뜀
```

### 한계 (명확히)

LangGraph / CrewAI 같은 **런타임 프레임워크가 아니다.**
"설계도 + 검증기 모음"이며, 실행 엔진은 Claude Code(또는 직접 만들 코드)가 담당.

---

## 12. React/PHP 구현 가능성

### 결론: 가능. 단 "무엇을" 만드느냐에 따라 3단계.

### 레벨 1 — 쉬움 ⭐ (1~2주): 스킬 카탈로그 웹사이트

```
React/Next.js → 388개 SKILL.md 파싱해서 검색/필터 UI
PHP/Laravel   → 동일. 마크다운 파서만 있으면 됨

구현:
  gray-matter 로 YAML frontmatter 파싱
  → 도메인/태그/난이도 필터
  → 전문 검색 (Algolia / MeiliSearch)
  → "이 스킬 설치하기" 복사 버튼
```

사실상 **정적 사이트 생성.** 데이터는 이미 정제돼 있음.

### 레벨 2 — 중간 ⭐⭐ (1~2개월): 파이썬 도구를 웹 서비스로

```php
// PHP
$out = shell_exec("python3 aeo_audit.py --url "
       . escapeshellarg($url) . " --output json");
return json_decode($out, true);
```

```javascript
// Next.js API Route
export async function POST(req) {
  const { url } = await req.json();
  const { stdout } = await execFile('python3',
    ['tools/aeo_audit.py', '--url', url, '--output', 'json']);
  return Response.json(JSON.parse(stdout));
}
```

왜 쉬운가 (실측 확인):
- 모든 도구가 `--output json` 지원
- 표준 라이브러리 only → **서버리스(Lambda/Vercel) 그대로 배포**
- 네트워크 호출 없음 → 보안 감사 쉬움

> **주의:** `shell_exec` / `execFile` 은 명령어 주입 위험.
> 반드시 인자 배열 방식 + 화이트리스트 검증.

### 레벨 3 — 어려움 ⭐⭐⭐ (3~6개월): 스킬 실행 엔진 재구현

```
필요한 것:
  - Claude/OpenAI API 연동 (여기선 토큰 필요)
  - 스킬 선택 라우터 (description 기반 매칭)
  - 도구 호출 루프 + 상태 관리
  - 컨텍스트 윈도우 관리
→ 사실상 "Claude Code를 다시 만드는 일"
```

### 추천 스택

| 목적 | 추천 |
|---|---|
| 카탈로그/문서 | **Next.js + MDX** (정적 생성, 무료 호스팅) |
| 도구 SaaS | **Next.js API + Python Lambda** 또는 **Laravel + Queue** |
| 국내 서비스 | **Laravel + Vue** (PHP 인력 수급 용이) |
| 빠른 MVP | **Streamlit** (파이썬 그대로, 1일) |

---

## 13. 유튜브 강의 제작 기획

### 적합도: 매우 높음 (오히려 소재 과잉)

```
✅ 시각적 Before/After 명확 (AI 답변 품질 차이를 화면으로)
✅ 에피소드 분할 자연스러움 (388개 = 무한 소재)
✅ 진입장벽 낮음 (설치 2줄, 즉시 결과)
✅ 한국어 콘텐츠 거의 없음  ← 가장 큰 기회
✅ MIT 라이선스 (출처 표기만 하면 자유)
```

### 시즌 1 — "Claude를 전문가로 만들기" 입문 8부작

| 화 | 제목 | 길이 | 훅 |
|---|---|---|---|
| 1 | AI가 숫자를 지어내는 이유와 해결법 | 12분 | "런웨이 물어봤더니 매번 답이 다릅니다" |
| 2 | 스킬 vs 플러그인 vs MCP 완전정리 | 15분 | 3대 혼동 개념 정리 |
| 3 | 설치 5분, 체감 즉시 | 10분 | 실시간 설치 + 비교 데모 |
| 4 | SEO 스킬로 내 사이트 점수 내보기 | 18분 | 실제 사이트 라이브 분석 |
| 5 | AI한테 내 사업계획 털리기 (grill-me) | 14분 | 본인 아이디어 실시간 박살 |
| 6 | CFO 모드 — 재무제표를 AI에게 | 20분 | 숫자 다루는 법 |
| 7 | 나만의 스킬 만들기 (write-a-skill) | 25분 | 시청자 참여형 |
| 8 | 수익화 — 이걸로 뭘 팔 수 있나 | 16분 | 시즌 마무리 + 전환 |

### 시즌 2 — "AI 에이전트 직접 만들기" 심화 6부작

agent-harness / agent-memory / agent-launcher 중심. 개발자 타깃, CPM 높음.

### 단발 바이럴 소재

```
"AI가 '다 했어요'라고 거짓말하는 걸 막는 코드"   (verification theater)
"밤에 혼자 공부하는 AI 세팅"                    (skillopt-sleep)
"내 AI 사용 기록 채점받아봤습니다"               (skill-doctor)
"책 한 권을 AI 스킬로 바꿔봤습니다"              (book-to-skill)
```

### 제작 시 주의사항

```
1. 출처 명시 필수
   - 원작자: Alireza Rezvani / MIT License
   - 파생 스킬은 상위 출처도 (Matt Pocock, Microsoft SkillOpt 등)
   → 영상 설명란 + 화면 1회 노출이면 충분

2. 숫자 검증
   - 레포 README 카운터는 버전별로 어긋남
   - 실측값: 388 스킬 / 745 도구 / 99 플러그인
   - 영상에서 수치를 말할 거면 직접 세보고 말할 것

3. 과장 금지
   ✗ "AI가 10배 똑똑해집니다"
   ✓ "숫자를 지어내지 않게 만듭니다"  (실제로 맞는 말)
```

---

## 14. 수익화 아이디어 10개

> **법적 전제:** MIT 라이선스. 상업적 이용·수정·재배포·판매 전부 합법.
> 조건은 단 하나 — LICENSE 파일과 저작권 표시 포함.
> 파생 스킬은 상위 출처도 표기 (Matt Pocock, Microsoft 등).

### 티어 1 — 지금 당장, 혼자, 저비용

#### 1. 한국어 스킬팩 판매 ⭐ 가장 추천

```
문제: 388개 전부 영어. 한국 시장 콘텐츠 사실상 0.
해법: 한국 시장에 맞는 20~30개만 골라 완전 한국어화 + 현지화

현지화 ≠ 번역:
  ✗ "Delaware C-Corp 설립"  → ✓ "법인 설립 / 벤처기업 인증"
  ✗ "GDPR 준수"            → ✓ "개인정보보호법 + GDPR"
  ✗ "Series A term sheet"  → ✓ "TIPS / 시리즈A 텀시트 (국내 관행)"
  ✗ "IRS tax"              → ✓ "홈택스 / 부가세 신고"
```

| 항목 | 내용 |
|---|---|
| 판매처 | Gumroad / 크몽 / 탈잉 / 자체 Lemon Squeezy |
| 가격 | 기본 ₩29,000 / 프로 ₩89,000 / 평생 ₩149,000 |
| 제작 | 3~4주 (30개 기준) |
| 초기비용 | 사실상 0원 |
| 월매출 | 초기 50~200만, 유튜브 연계 시 500만+ |
| 난이도 | ⭐⭐ |

**차별화:** "번역"이 아니라 **"국내 규제·관행 반영"** 으로 포지셔닝.
이건 번역기로 못 함.

#### 2. 업종 특화 스킬팩 (Vertical Pack)

388개는 너무 넓다. **좁고 깊게.**

```
🏥 의료기기 스타트업 팩
   ra-qm-team(19) + compliance-os(9) 기반
   + 식약처 GMP / 의료기기법 / KFDA 인허가 절차 추가
   → 타깃: 국내 의료기기 스타트업 (컨설팅비 수천만원 시장)
   → 가격: ₩300,000 ~ ₩1,500,000

⚖️ 법무/노무 팩   general-counsel-advisor + 근로기준법/표준계약서
🏭 제조 DX 팩     business-operations(7) + process-mapper
🎓 교육기관 팩     research(10) + syllabus + notebooklm
```

| 항목 | 내용 |
|---|---|
| 가격대 | ₩30만 ~ ₩150만 (B2B) |
| 핵심 | **1개 업종만.** 욕심내면 다 망함 |
| 난이도 | ⭐⭐⭐ (도메인 전문가 필요) |
| 수익성 | 💰💰💰💰 (단가 50배 차이) |

#### 3. 유튜브 + 디지털 상품 퍼널

```
무료 유튜브 (주 1회)
   ↓ 신뢰 축적
무료 리드마그넷 (스킬 10개 한국어 번역본 PDF)
   ↓ 이메일 수집
유료 강의 (₩99,000, 인프런/클래스101/자체)
   ↓
스킬팩 번들 (₩149,000)
   ↓
1:1 컨설팅 (시간당 ₩150,000~)
```

| 항목 | 내용 |
|---|---|
| 선행기간 | 3~6개월 (구독자 축적) |
| 초기비용 | 장비 50~100만원 |
| 수익 | 6개월차 월 100~300만, 1년차 500~1,000만 |
| 난이도 | ⭐⭐ (꾸준함이 전부) |

### 티어 2 — 개발 필요, 중기

#### 4. 단일 도구 SaaS화 ⭐ 개발자 추천

388개 중 "그 자체로 상품이 되는" 도구만 뽑아 웹서비스화.

| 도구 | 서비스 | 월 구독가 |
|---|---|---|
| `aeo_audit.py` | **AI 검색 최적화 점수 측정기** (지금 가장 핫) | ₩19,000 |
| `seo_checker.py` | SEO 자동 진단 | ₩19,000 |
| `contract_risk_scanner.py` | 계약서 독소조항 12종 스캐너 | ₩39,000 |
| `retention_decomposition_analyzer.py` | SaaS 리텐션 분석 | ₩49,000 |
| `delivery_throughput_analyzer.py` | DORA 4대 지표 추적 | ₩29,000 |

```
기술적 유리함 (실측 확인):
  ✅ 모든 도구가 --output json 지원
  ✅ 표준 라이브러리만 → Lambda/Vercel 그대로 배포
  ✅ 네트워크 호출 없음 → 보안 감사 쉬움
  ✅ 결정론적 → 캐싱 가능, 비용 예측 가능
  ✅ LLM 호출 없음 → 원가 거의 0
```

| 항목 | 내용 |
|---|---|
| 개발 | MVP 4~6주 |
| 원가 | 서버 월 5만원 이하 (LLM 안 씀) |
| 가격 | 월 ₩19,000 ~ ₩49,000 |
| BEP | 유료 10명 |
| 난이도 | ⭐⭐⭐ |

> **핵심 인사이트:** 대부분의 AI SaaS는 LLM 호출 원가 때문에 마진이 안 난다.
> **이 도구들은 LLM을 안 쓰므로 마진이 95%+.**

#### 5. 한국형 스킬 마켓플레이스

```
"한국판 ClawHub"
  - 창작자가 스킬 업로드 → 판매, 플랫폼 수수료 20~30%
  - 이 레포 388개를 초기 시드 콘텐츠로 (MIT라 합법)
```

난이도 ⭐⭐⭐⭐⭐ / 리스크: 닭-달걀 문제 / 투자 유치 전제. 개인 비추천.

#### 6. 사내 AI 표준화 컨설팅 ⭐ 단가 최고

```
기업의 진짜 문제:
  "직원마다 AI 쓰는 방식이 다르다"
  "AI 결과물 품질 편차가 크다"
  "규제 산업인데 AI 사용 가이드가 없다"

제공:
  ① 현황 진단 (skill-doctor 활용, 실사용 로그 채점)
  ② 사내 전용 스킬 20~30개 제작 (write-a-skill 활용)
  ③ 팀 교육 2일
  ④ 분기별 유지보수
```

| 항목 | 내용 |
|---|---|
| 단가 | 초기 구축 ₩1,500만 ~ ₩5,000만 |
| 유지보수 | 월 ₩200만 ~ ₩500만 |
| 인원 | 1~2명 |
| 난이도 | ⭐⭐⭐ (영업이 80%) |
| 수익성 | 💰💰💰💰💰 **최고** |

진입: 유튜브/블로그로 권위 확보 → 인바운드 → 첫 레퍼런스 → 확산

### 티어 3 — 장기·고위험

#### 7. 산업별 AI 에이전트 제품화
agent-harness + agent-launcher 를 엔진으로 완제품 에이전트 판매.
예: "의료기기 인허가 문서 자동 검토 에이전트"
단가 ₩3,000만+/연, 난이도 ⭐⭐⭐⭐⭐

#### 8. 오프라인 부트캠프
"AI 에이전트 구축 실무" 2일 집중. 20명 × ₩500,000 = 회당 1,000만원.
월 1회 운영 가능. 난이도 ⭐⭐⭐

#### 9. 뉴스레터 + 멤버십
주간 "AI 스킬 1개 해부" 무료 → 유료 멤버십 월 ₩9,900.
구독자 1,000명 × 5% 전환 = 월 50만원 (안정적 MRR)

#### 10. 책 / 전자책 출간
"AI 에이전트 스킬 설계 실무" — 이 레포 설계 패턴 5개가 챕터 뼈대.
전자책 ₩19,800. 권위 확보 → 컨설팅 단가 상승이 진짜 목적.

### 종합 비교표

| # | 아이디어 | 초기비용 | 기간 | 난이도 | 월 기대수익 | 추천도 |
|---|---|---|---|---|---|---|
| 1 | 한국어 스킬팩 | 0원 | 1개월 | ⭐⭐ | 50~500만 | 🔥🔥🔥🔥🔥 |
| 2 | 업종 특화팩 | 0원 | 2개월 | ⭐⭐⭐ | 300~2,000만 | 🔥🔥🔥🔥🔥 |
| 3 | 유튜브 퍼널 | 100만 | 6개월 | ⭐⭐ | 100~1,000만 | 🔥🔥🔥🔥 |
| 4 | 단일 도구 SaaS | 50만 | 2개월 | ⭐⭐⭐ | 100~800만 | 🔥🔥🔥🔥 |
| 5 | 마켓플레이스 | 수천만 | 1년 | ⭐⭐⭐⭐⭐ | 불확실 | 🔥 |
| 6 | 기업 컨설팅 | 0원 | 3개월 | ⭐⭐⭐ | 500~3,000만 | 🔥🔥🔥🔥🔥 |
| 7 | 산업 에이전트 | 수천만 | 1년 | ⭐⭐⭐⭐⭐ | 고액/저빈도 | 🔥🔥 |
| 8 | 부트캠프 | 200만 | 2개월 | ⭐⭐⭐ | 500~1,000만 | 🔥🔥🔥 |
| 9 | 뉴스레터 멤버십 | 0원 | 6개월 | ⭐⭐ | 30~200만 | 🔥🔥🔥 |
| 10 | 출간 | 0원 | 6개월 | ⭐⭐⭐ | 간접효과 | 🔥🔥🔥 |

---

## 15. 실행 로드맵

```
[0~1개월]  한국어 스킬팩 30개 제작 (#1)
             + 유튜브 채널 개설, 주 1회 업로드 (#3)
                    ↓
[2~3개월]  스킬팩 판매 시작, 피드백 수집
             + 단일 도구 1개 SaaS MVP (#4)
                    ↓
[4~6개월]  유튜브 권위 확보 → 기업 문의 인바운드
             + 업종 1개 선정해 특화팩 (#2)
                    ↓
[6~12개월] 기업 컨설팅 본격화 (#6)  ← 여기가 진짜 돈
             + 부트캠프/출간으로 권위 강화
```

**핵심 전략:** #1·#3 은 **고객 발굴 장치**, #6 이 **수익 엔진**.
388개 전부를 다루려 하지 말고 **"한 업종 + 한 언어"로 좁히는 것**이 승부처.

---

## 16. 주의사항

### 라이선스 준수

```
MIT 라이선스 요구사항 (이것만 지키면 전부 합법):
  ✅ LICENSE 파일 포함
  ✅ 저작권 표시 유지: "Copyright (c) 2025 Alireza Rezvani"
  ✅ 파생 스킬의 상위 출처도 표기
     - Matt Pocock (write-a-skill, caveman, grill-me, handoff)
     - Microsoft Corporation / Yifan Yang (skillopt-sleep)
     - Denver Technologies (skill-doctor)
     - OpenAI (spinning-up-deep-rl)
     - TencentCloud (agent-memory, 개념만)
```

### 기술적 주의

```
1. 설치 과다 금지 — 99개 전부 깔면 컨텍스트 오염. 3~5개 번들 권장.
2. 카운터 불일치 — README/CLAUDE.md 수치가 버전별로 어긋남.
   인용 시 scripts/derive_counters.py 로 직접 확인.
3. 웹 래핑 시 명령어 주입 — shell_exec 대신 인자 배열 + 화이트리스트.
4. skillopt-sleep / agent-launcher 만 실제 API 과금 가능.
   기본값은 둘 다 무과금 모드.
5. 저장소 자체 규칙: scripts/ 에 LLM 호출 추가 금지 (안티패턴).
```

### 사실 확인 메모

이 문서의 모든 수치는 **로컬 클론 전수 스캔으로 직접 측정**한 값이다.
저장소 문서에 적힌 카운터(예: 380 / 727 / 99)와 일부 어긋나는 것은
버전 간 드리프트 때문이며, 실측값이 더 정확하다.

---

## 참고 링크

- 이 포크: https://github.com/bmshin94/claude-skills
- 원본: https://github.com/alirezarezvani/claude-skills
- 라이선스: MIT (https://github.com/alirezarezvani/claude-skills/blob/main/LICENSE)
- 설치 가이드: [INSTALLATION.md](../../INSTALLATION.md)
- 프로젝트 가이드: [CLAUDE.md](../../CLAUDE.md)
- 변경 이력: [CHANGELOG.md](../../CHANGELOG.md)
