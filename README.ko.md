# ⚖️ oh-my-hermes-for-legal-researcher

[Hermes Agent](https://github.com/NousResearch/hermes-agent)를 위한 미국 법률 리서치 스킬입니다. 법령(statute), 규제(regulation), 판례(case law) 리서치 방법론을 [Anthropic의 claude-for-legal](https://github.com/anthropics/claude-for-legal) 프로젝트로부터 이식(porting)하였습니다.

> 외부 의존성 없이 동작하며, API 키도 필요하지 않습니다. 사용되는 모든 데이터 소스(Federal Register API, 웹 검색)는 무료이자 공개된 자원입니다.

> 🇺🇸 [English](./README.md)

---

# 📌 무엇을 검색할 수 있나

## ✅ 연방 규제(Federal Regulations)
미국 연방정부의 공식 일간 관보인 **Federal Register**를 검색할 수 있습니다.

| 자료 유형 | 범위 | 방식 |
|--------|----------|-----|
| **제안 규칙(Proposed Rules)** | 행정기관이 의견 수렴을 위해 제안한 규칙 | Federal Register API → 구조화된 Regulation Digest |
| **최종 규칙(Final Rules)** | 시행일이 확정된 채택 규칙 | 동일 |
| **고시·공지(Notices)** | 기관 공지, 동의명령, 정보수집 관련 사항 | 동일 |
| **NPRM** (규칙제정예고, Notice of Proposed Rulemaking) | 의견 제출 기한이 포함된 공식 제안 | 동일 |

**지원 기관:** FTC, SEC, CFPB, FCC, EPA, DOJ, DOL, HHS 등 200개 이상의 기관(숫자 ID 조회 방식 지원).

## ✅ 판례(Case Law)
웹 검색을 통해 미국 연방 및 주 법원 판결문을 검색할 수 있습니다.

| 유형 | 범위 | 제한 사항 |
|------|----------|------------|
| **연방 판례(Federal Cases)** | 연방대법원, 연방순회항소법원, 연방지방법원 | 인용에는 `[verify]` 태그가 붙을 수 있으므로 반드시 교차 검증 필요 |
| **주 판례(State Cases)** | 각 주의 공개된 항소심 판결 | 모든 주가 동일한 수준으로 제공되지는 않음 |
| **주요 분야(Key Topic Areas)** | AI와 저작권, 오픈소스와 영업비밀, 프라이버시, 반독점 등 | 원하는 주제를 요청 가능 |

> 🔌 **선택 사항:** [CourtListener API](#courtlistener-api--검증된-인용) 를 연결하면(무료), `[verify]` 태그 대신 검증된 인용 정보를 사용할 수 있습니다.

## ✅ 법령(Statutes)
웹 검색을 통해 미국 연방법전(U.S. Code), 공법(public laws), 법안(bills)을 검색할 수 있습니다.

| 유형 | 범위 | 제한 사항 |
|------|----------|------------|
| **미국 연방법전(U.S. Code, USC)** | 편·조문 단위의 연방법률 | Congress.gov API 키 없이는 전용 API 미지원, 웹 검색 기반 |
| **공법(Public Laws)** | 최근 제정된 법률 | govinfo.gov 제공 |
| **법안(Bills)** | 계류 중인 입법안 | Congress.gov 제공 |

## ✅ 2차 자료(Secondary Sources)
웹 검색을 통해 법학 논문, 법률 해설, 규제 분석 자료 등을 검색할 수 있습니다.

---

## ❌ 지원하지 않는 영역(한계)

| 항목 | 이유 |
|------|-----|
| **미국 외 국가의 법률** | 설계상 미국법 전용 |
| **유료 데이터베이스** (Westlaw, LexisNexis) | 유료 구독 없이 무료·공개 자료만 사용 |
| **주(State)별 성문법 데이터베이스** | 웹 검색으로는 찾을 수 있으나 구조화된 API 없음 |
| **아주 최근의 미공개 판례** | 공개 데이터베이스에 반영되기까지 시간이 필요 |
| **법률 자문 또는 소송 전략 제공** | 모든 출력은 변호사 검토를 전제로 한 초안이라는 점을 전제로 함 |

---

# 🧬 이 포트(port)에 대하여

[Anthropic](https://www.anthropic.com/)의 [claude-for-legal](https://github.com/anthropics/claude-for-legal)는 AI 기반 법률 워크플로우를 위한 종합적인 오픈소스 레퍼런스 구현체입니다. 이 프로젝트는 **80개 이상의 에이전트**와 **11개 이상의 실무 분야 플러그인**으로 구성되어 있습니다.

```text
claude-for-legal/
├── commercial-legal/        # 계약 검토, 갱신, 에스컬레이션
├── corporate-legal/         # M&A 실사, 클로징 체크리스트
├── employment-legal/        # 채용·해고 검토, 근로자 분류
├── privacy-legal/           # DPA, DSAR, PIA
├── product-legal/           # 출시 검토, 마케팅 주장 검증
├── regulatory-legal/        # 규제 피드 감시, 정책 변경 비교
├── ai-governance-legal/     # AI 활용사례 분류, 영향평가
├── ip-legal/                # 상표, FTO, DMCA, OSS
├── litigation-legal/        # Claim chart, 연표, 증언 준비
├── legal-clinic/            # 로스쿨 클리닉 워크플로우
├── law-student/             # 소크라테스식 학습, IRAC, 변호사시험 대비
├── legal-builder-hub/       # 커뮤니티 스킬 탐색 및 설치
│
└── [공통 리서치 방법론 레이어] ← 🎯 본 프로젝트가 이식한 부분
    ├── 출처 태깅 및 인용 검증 패턴
    ├── Reviewer Note 규칙
    ├── 불확실성 공개 원칙
    └── 리서치 루프 (수집 → 종합 → 검증 → 전달)
```

본 프로젝트는 claude-for-legal 전체를 이식한 것이 아니라, 11개 플러그인 전반의 기반이 되는 공통 리서치 방법론 레이어만을 추출하여 Hermes Agent용 스킬로 포팅한 것입니다. 계약 검토, 실사, 노동법, 프라이버시 등 개별 실무 분야 로직은 의도적으로 제외하였습니다.

이 스킬은 다음과 같은 사용자들을 위해 설계되었습니다.

- **법률 연구자**: 법령, 규제, 판례를 탐색하고 구조화하려는 경우
- **법학도**: IRAC 분석, claim chart, 리서치 브리프 작성
- **실무가**: 검증 플래그가 포함된 빠른 초기 리서치 수행
- **AI·정책 연구자**: 규제 프레임워크 탐색 및 Federal Register 업데이트 모니터링

claude-for-legal이 실무 분야별 종합 법률 플랫폼이라면, `oh-my-hermes-for-legal-researcher`는 설정 없이 바로 사용할 수 있는 경량형 미국 법률 리서치 스킬에 가깝습니다.

---

# 📋 claude-for-legal에서 포팅된 요소

## ✅ 포팅됨 — 핵심 리서치 방법론

| 개념 | claude-for-legal 원본 | 본 프로젝트 구현 |
|---------|--------------------------------------|-------------------|
| **모든 인용에 대한 출처 표시** | 모든 플러그인 공통 원칙 | `[Federal Register]`, `[web search — verify]`, `[model knowledge — verify]`, `[user provided]`, `[secondary source]` 태그 사용 |
| **사실과 추론의 분리** | “출처가 말하는 것과 자신의 결론을 구분하라” | 모든 출력에서 출처 내용과 분석 결론을 명시적으로 분리 |
| **침묵 보완 금지(No silent supplement)** | “증거가 빈약하면 extrapolate 하지 말고 needs-verification으로 표시” | 모든 리서치 출력에서 `needs-verification` 플래그 사용 |
| **인용 검증 원칙** | “AI가 생성한 인용은 허구일 수 있으므로 검증 필요” | 모든 출력 상단에 Reviewer Note 배치 |
| **Reviewer Note 패턴** | 출처, 플래그, 최신성, 우선 검토 사항 명시 | 구조화된 Reviewer Note 구현 |
| **불확실성 명시** | “모든 출력은 법률 결론이 아닌 초안” | `[draft — attorney review required]` 표시 |

## ✅ 포팅됨 — 법률 문서 템플릿

| 템플릿 | 원본 | 적용 방식 |
|----------|----------------|----------------|
| **Claim / Element Chart** | litigation-legal 플러그인 | 요소별 증거·강도·누락사항 표 제공 |
| **연표(Chronology / Timeline)** | chronology-builder 스킬 | 중요도 표시(🔴/🟡/⚪) 포함 날짜순 정리 |
| **Research Brief** | 공통 출력 포맷 | Summary / Findings / Open Questions / Sources 구조 |

## ✅ 포팅됨 — 규제 리서치 워크플로우

| 워크플로우 | 원본 | 구현 |
|----------|----------------|----------------|
| **Federal Regulation Search** | Reg Feed Watcher | Federal Register API 기반 검색 |
| **Regulation Digest 형식** | 🔴 중요 / 🟡 검토 필요 / 📝 참고 | 동일 형식 유지 |
| **기관별 검색** | agency configurations | FTC, SEC, CFPB, DOL, HHS, FCC, EPA, DOJ 등 지원 |

## ✅ 포팅됨 — 리서치 원칙 및 가드레일

- 모든 출력은 변호사 검토가 필요한 초안이라는 점을 명시
- 검증되지 않은 인용을 사실처럼 제시하지 않음
- 수집 → 종합 → 검증 → 전달의 반복 루프 유지

## ✅ 포팅됨 — 판례 리서치 전략

- 구조화된 웹 검색 기반 판례 탐색
- Bluebook 스타일 인용 형식 사용

---

# 🔲 범위 밖(포팅되지 않은 기능)

다음 요소들은 유료 서비스, MCP 커넥터, 플랫폼 종속 기능 등에 의존하므로 의도적으로 제외되었습니다.

| 기능 | claude-for-legal | 제외 이유 |
|---------|---------------------|--------------|
| 실무 분야별 플러그인 | 11개 이상의 개별 플러그인 | Hermes Agent는 단순 스킬 구조 사용 |
| MCP 커넥터 | CourtListener, Trellis 등 | API 키·유료 구독 필요 |
| 콜드 스타트 인터뷰 | practice profile 작성 | Hermes Agent는 memory 기반 |
| 스케줄링 에이전트 | Watcher 계열 에이전트 | Hermes cronjob 사용 가능 |
| Microsoft 365 연동 | Word/Excel 연동 | 플랫폼 종속 기능 |
| Practice Profile (`CLAUDE.md`) | 파일 기반 설정 | Hermes는 사용자 메모리 사용 |
| Legal Builder Hub | 커뮤니티 허브 | 단일 스킬 범위 초과 |
| Thomson Reuters CoCounsel 연동 | 외부 플러그인 | 유료 구독 필요 |

---

# 🔧 동작 방식

이 스킬은 독립 실행형 애플리케이션이 아니라, Hermes Agent를 위한 **리서치 방법론 가이드**입니다. 사용자가 법률 리서치 질문을 입력하면, 에이전트는 `SKILL.md`에 정의된 워크플로우를 따라 Hermes Agent 내장 도구를 호출합니다.

## Tool Architecture

| 도구 | 역할 | 기본 동작 여부 | 비고 |
|------|------|----------------|------|
| `delegate_task(toolsets=["web"])` | 웹 검색 가능한 서브에이전트 생성 | ✅ | 판례·법령 리서치 기본 수단 |
| `curl` + Federal Register API | REST API 기반 규제 데이터 조회 | ✅ | 인증 불필요 |
| `r.jina.ai` | URL 전체 내용을 Markdown으로 추출 | ✅ | 무료, API 키 불필요 |
| `browser_navigate` + `browser_snapshot` | Chromium 기반 렌더링 | ✅ | JS-heavy 사이트 대응 |
| `web_extract` | URL 내용 추출 | ⚠️ 설정 필요 | extract_backend 필요 |
| `fetch_content` | web_extract와 동일 | ⚠️ 설정 필요 | 동일 |

## DuckDuckGo 기본 동작 관련 주의사항

Hermes Agent는 기본 설정에서 `extract_backend: ''` 상태로 배포됩니다. 이 상태에서 `web_extract`를 호출하면 DuckDuckGo 검색 엔진을 사용하게 되는데, 이는 콘텐츠 추출기가 아니라 검색엔진입니다.

```python
# ❌ 설정이 없으면 실패 가능
web_extract(urls=["https://example.com/case-law"])

# ✅ 항상 동작
curl -sL "https://r.jina.ai/https://example.com/case-law"

# ✅ Chromium 렌더링 기반
browser_navigate("https://example.com/case-law")
browser_snapshot(full=true)
```

따라서 본 스킬은 `web_extract`에 의존하지 않으며, 다음 우선순위를 권장합니다.

1. `r.jina.ai`
2. Browser tools
3. 설정된 경우에만 `web_extract`

---

# 🛠️ 설치 방법

## 사전 준비

- [Hermes Agent](https://github.com/NousResearch/hermes-agent)가 설치 및 설정되어 있어야 함
- API 키, 유료 구독, MCP 서버는 필요하지 않음

## 설치

```bash
git clone https://github.com/charliehotel/oh-my-hermes-for-legal-researcher.git
mkdir -p ~/.hermes/skills/research
cp -r oh-my-hermes-for-legal-researcher/research/us-legal-research ~/.hermes/skills/research/
```

## 확인

```bash
ls ~/.hermes/skills/research/us-legal-research/
# 출력 예시: SKILL.md  references/
```

---

# 🔌 선택적 API 연동

## r.jina.ai — 무료 콘텐츠 추출

```bash
curl -sL "https://r.jina.ai/https://www.courtlistener.com/opinion/12345"
```

API 키 없이 사용 가능하며, 깔끔한 Markdown을 반환합니다.

## CourtListener API — 검증된 인용

장점:
- 연방·주 법원 판결문에 대한 구조화된 접근
- `[web search — verify]` 대신 `[CourtListener — verified]` 사용 가능

설정:

```bash
# 1. 가입
https://www.courtlistener.com/api/register/

# 2. ~/.hermes/.env 추가
COURTLISTENER_API_KEY="your_token_here"

# 3. 사용 예시
curl -s "https://www.courtlistener.com/api/rest/v4/opinions/?q=AI+copyright" \
  -H "Authorization: Token $COURTLISTENER_API_KEY"
```

---

# 🚀 사용 예시

**"2026년 FTC의 AI 관련 Proposed Rules를 Federal Register에서 검색해줘"**
→ Federal Register API 호출 → 구조화된 Regulation Digest 반환

**"오픈소스 코드와 영업비밀 관련 최근 판례 찾아줘"**
→ 웹 검색 기반 서브에이전트 실행 → `[verify]` 태그 포함 결과 반환

**"이 증언 날짜들을 기반으로 chronology 만들어줘"**
→ 연표 템플릿 사용

**"의료 분야 AI 거버넌스 규제 체계 조사해줘"**
→ 전체 리서치 루프 수행

## 리서치 루프 구조

```text
   ┌─────────────┐
   │ 목표 설정     │
   └──────┬──────┘
          ▼
   ┌─────────────┐
   │ 자료 수집     │
   └──────┬──────┘
          ▼
   ┌─────────────┐
   │ 종합·정리     │
   └──────┬──────┘
          ▼
   ┌─────────────┐     ┌─────────────┐
   │ 검증         │────→│ 추가 필요?    │──→ 반복
   └──────┬──────┘     └─────────────┘
          ▼
   ┌─────────────┐
   │ 결과 전달     │
   └─────────────┘
```

---

# 📁 프로젝트 구조

```text
oh-my-hermes-for-legal-researcher/
├── LICENSE
├── README.md
└── research/
    └── us-legal-research/
        ├── SKILL.md
        └── references/
            └── open-source-trade-secret-cases.md
```

---

# 📜 라이선스 및 출처

**Apache 2.0** 라이선스를 따릅니다. 자세한 내용은 [LICENSE](./LICENSE)를 참고하세요.

본 프로젝트는 Anthropic의 [claude-for-legal](https://github.com/anthropics/claude-for-legal)을 기반으로 파생되었습니다.

적용된 주요 요소:
- 리서치 방법론
- 규제 리서치 워크플로우
- Litigation 템플릿
- Reviewer Note 규칙

Hermes Agent용 추가 워크플로우 및 각색은 **copylawbot**이 수행하였습니다.

---

# 🤝 기여하기

PR은 언제나 환영합니다.

기여 가능한 영역:

- 추가 법률 레퍼런스 및 판례 정리
- 무료 법률 API 지원 확장
- 실무 분야별 워크플로우 개선
- 추가 문서 템플릿 개발

Apache 2.0 라이선스 및 원 프로젝트에 대한 attribution은 유지해 주세요.

---

> **면책 조항:** 본 도구는 법률 리서치를 보조하기 위한 것이며 법률 자문을 제공하지 않습니다. 모든 출력은 변호사 검토가 필요한 초안입니다. AI가 생성한 인용은 허구일 수 있으므로, 실제 활용 전 반드시 1차 출처와 대조하여 검증하시기 바랍니다.
