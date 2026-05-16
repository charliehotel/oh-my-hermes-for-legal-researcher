# ⚖️ oh-my-hermes-for-legal-researcher

**[Hermes Agent](https://github.com/NousResearch/hermes-agent)용 미국 법률 리서치 스킬** — [Anthropic의 claude-for-legal](https://github.com/anthropics/claude-for-legal) 프로젝트에서 법령·판례·규제 리서치 방법론을 이식했습니다.

> **외부 의존성 제로, API 키 불필요.** 사용하는 모든 데이터 소스(Federal Register API, 웹 검색)는 무료로 공개되어 있습니다.

> 🇰🇷 [English README](./README.md)

---

## 🧬 이식 개요 (About This Port)

[claude-for-legal](https://github.com/anthropics/claude-for-legal)은 [Anthropic](https://www.anthropic.com/)이 만든 AI 기반 법률 워크플로우 오픈소스 레퍼런스 구현체입니다. **80개 이상의 에이전트**가 **11개 이상의 실무 플러그인**으로 구성되어 있습니다:

```
claude-for-legal/
├── commercial-legal/        # 계약 검토, 갱신, 에스컬레이션
├── corporate-legal/         # M&A 실사, 폐쇄 체크리스트
├── employment-legal/        # 채용/해고 검토, 근로자 분류
├── privacy-legal/           # DPA, DSAR, PIA
├── product-legal/           # 출시 검토, 마케팅 클레임
├── regulatory-legal/        # 규제 피드 워처, 정책 비교
├── ai-governance-legal/     # AI 사용 사례 분류, 영향 평가
├── ip-legal/                # 상표, FTO, DMCA, OSS
├── litigation-legal/        # 클레임 차트, 연대표, 변론 준비
├── legal-clinic/            # 로스쿨 클리닉 워크플로우
├── law-student/             # Socratic drilling, IRAC, bar prep
├── legal-builder-hub/       # 커뮤니티 스킬 발견 및 설치
│
└── [공통 리서치 메소드 레이어] ← 🎯 **이식한 부분**
    ├── 출처 태깅 및 인용 검증 패턴
    ├── Reviewer Note 컨벤션
    ├── 불확실성 공개 원칙
    └── 리서치 루프 (수집 → 종합 → 검증 → 전달)
```

**우리는 11개 플러그인 전체가 아닌, 모든 플러그인에 공통으로 깔려 있는 리서치 메소드 레이어만 집중적으로 추출하여 Hermes Agent 스킬로 이식했습니다.** 실무 도메인 로직(계약 검토, 실사, 고용, 프라이버시 등)은 의도적으로 제외했습니다. 이 스킬은 오직 하나에 집중합니다: **법령·판례·학술 자료를 찾고 분석하는 신뢰할 수 있는 방법론.**

이 스킬은 다음과 같은 사용자에게 적합합니다:

- **법학 연구자** — 미국 연방 법령(statute), 연방 규칙(regulation), 판례(case law) 검색 및 구조화
- **로스쿨 학생** — IRAC 분석, 클레임 차트, 리서치 브리프 작성
- **실무 변호사** — 기초 리서치를 빠르게 파악하고 검증이 필요한 포인트 식별
- **AI/정책 연구자** — 규제 프레임워크 탐색 및 Federal Register(연방 관보) 최신 업데이트 모니터링

claude-for-legal이 포괄적인 실무 영역 제품군(에이전트, MCP 커넥터, 스케줄 워처, 프랙티스 프로파일)이라면, `oh-my-hermes-for-legal-researcher`는 **설정 없이, 유료 구독 없이** 신뢰할 수 있는 출처 검증형 미국 법률 리서치를 제공하는 집중형 스킬입니다.

---

## 📋 claude-for-legal에서 이식한 내용

### ✅ 이식 — 핵심 리서치 방법론

| 개념 | claude-for-legal 원본 | 구현 |
|------|----------------------|------|
| **모든 인용에 출처 태깅** | 모든 플러그인의 핵심 원칙 — 모든 주장에 출처 표시 | `[Federal Register]`, `[web search — verify]`, `[model knowledge — verify]`, `[user provided]`, `[secondary source]` 태그 |
| **관찰과 추론 분리** | `claude-for-legal/README.md` — "출처가 말하는 것과 당신이 결론내리는 것을 분리하라" | 모든 리서치 출력에서 출처 주장과 분석 결론을 명시적으로 구분 |
| **무음 보강 금지** | "증거가 부족하면 `needs-verification` 표시, 절대 추정 금지" | 모든 리서치 출력에 `needs-verification` 플래그, 격차(gap)를 명시적으로 표시 |
| **의존 전 인용 검증** | "AI 생성 인용은 조작될 수 있음 — 모든 인용 검증 필수" | 모든 출력 상단의 Reviewer Note에서 미검증 출처 표시 |
| **Reviewer Note 패턴** | 모든 리서치 결과물 상단 — 사용 출처, 플래그, 최신성, 우선 확인 사항 | 구조화된 Reviewer Note: 출처, 플래그, 관할권 범위, 최신성 확인 |
| **불확실성 명시적 표시** | "모든 출력은 초안이며 법적 결론이 아님" | 모든 결과물에 `[draft — attorney review required]` 마커 |

### ✅ 이식 — 법률 문서 템플릿

| 템플릿 | 원본 | 적용 |
|--------|------|------|
| **청구/요소 차트 (Claim/Element Chart)** | `litigation-legal` 플러그인 — claim-chart 스킬 | 증거 열, 강도 평가, 격차 목록이 포함된 요소별 차트 |
| **연대표 (Chronology/Timeline)** | `litigation-legal` 플러그인 — chronology-builder 스킬 | 중요도 마커(🔴/🟡/⚪)가 포함된 날짜순 이벤트 연대표 |
| **리서치 브리프 (Research Brief)** | 플러그인 공통 표준 출력 형식 | 요약 / 발견사항 / 미해결 질문 / 출처 구조 |

### ✅ 이식 — 규제 리서치 워크플로우

| 워크플로우 | 원본 | 구현 |
|-----------|------|------|
| **연방 규칙 검색** | `regulatory-legal` 플러그인 — `Reg Feed Watcher` + `On-demand Reg Check` | Federal Register API 키워드·기관·기간 검색 |
| **Regulation Digest 형식** | `regulatory-legal` 출력 컨벤션 — 🔴 중요 / 🟡 검토 / 📝 참고 | 동일한 분류 형식 + 발효일, 의견 제출 마감일, 관련성 평가 |
| **기관별 검색** | `regulatory-legal` 플러그인 — 기관 설정 | 8+ 기관 슬러그 (FTC, SEC, CFPB, DOL, HHS, FCC, EPA, DOJ) |

### ✅ 이식 — 리서치 원칙 및 안전장치

- **면책조항 패턴** — 모든 출력은 변호사 검토가 필요한 초안 (claude-for-legal의 `[!IMPORTANT]` 콜아웃에서 차용)
- **인용 조작 인식** — 웹 검색 결과는 `[verify]` 태그, 검증되지 않은 주장은 사실로 제시하지 않음 (모든 claude-for-legal 플러그인의 핵심 안전장치)
- **리서치 반복 루프** — 수집(Gather) → 종합(Synthesize) → 검증(Validate) → 반복/전달(Iterate/Deliver) (모든 claude-for-legal 플러그인의 리서치 루프를 반영)

### ✅ 이식 — 판례 리서치 전략

- **웹 기반 판례 검색** — 구조화된 쿼리 사용 (claude-for-legal의 CourtListener MCP 커넥터 패턴에 대응, 무료 웹 검색으로 폴백)
- **출처 검증형 인용 형식** — Case Name, Volume Reporter Page (Court Year) — claude-for-legal 전반에서 사용하는 표준 Bluebook 형식

---

## 🔲 범위 외 (이식하지 않음)

유료 구독, MCP 커넥터, 플랫폼 특화 기능에 의존하거나 집중형 리서치 스킬의 범위를 벗어나므로 **의도적으로 제외**했습니다:

| 기능 | claude-for-legal에서의 위치 | 제외 사유 |
|------|---------------------------|----------|
| 실무 영역 플러그인 (상업, 기업, 고용, 프라이버시, IP, 소송, AI 거버넌스) | 11개 이상의 별도 플러그인 디렉토리 | Hermes Agent는 평면 스킬 네임스페이스 사용; 실무별 로직은 별도 스킬로 제작 가능 |
| MCP 커넥터 (CourtListener, Trellis, Ironclad, DocuSign, iManage, Everlaw, Box 등) | 플러그인별 `.mcp.json` + MCP 서버 | 유료 구독/API 키 필요; 이 스킬은 무료 운영을 원칙으로 설계 |
| Cold-start 인터뷰 메커니즘 | `/<plugin>:cold-start-interview` → `CLAUDE.md` 작성 | Hermes Agent는 파일 기반 프랙티스 프로파일 대신 memory + goal tracking 사용 |
| 스케줄 에이전트 (갱신 워처, 도킷 워처, 규제 피드 워처 등) | `agents/` 디렉토리 (cron 스케줄) | Hermes Agent 내장 cronjob 시스템으로 대체 가능 |
| Microsoft 365 연동 (Word 변경 추적, Excel 워크북) | Claude for Microsoft 365 애드인 | 플랫폼 종속적; Hermes Agent는 터미널/API 우선 |
| 프랙티스 프로파일 (`CLAUDE.md`) | 모든 스킬이 읽는 플러그인별 설정 파일 | Hermes Agent 스킬은 memory + user profile에서 읽음 |
| Legal Builder Hub (커뮤니티 스킬 발견, 신뢰 계층, QA 프레임워크) | `legal-builder-hub` 플러그인 | 단일 스킬 범위 초과; 별도 탐구 가능 |
| Managed Agent 배포 (`agent.yaml`, leaf-workers) | `managed-agent-cookbooks/` 디렉토리 | Hermes Agent는 자체 배포 모델 보유 |
| Thomson Reuters CoCounsel 연동 | `external_plugins/cocounsel-legal/` | 유료 구독 필요 |

---

## 🛠️ 설치

### 사전 요구사항

- [Hermes Agent](https://github.com/NousResearch/hermes-agent) 설치 및 설정 완료
- API 키, 유료 구독, MCP 서버 불필요

### 설치 방법

```bash
git clone https://github.com/charliehotel/oh-my-hermes-for-legal-researcher.git
mkdir -p ~/.hermes/skills/research
cp -r oh-my-hermes-for-legal-researcher/research/us-legal-research ~/.hermes/skills/research/
```

### 설치 확인

```bash
ls ~/.hermes/skills/research/us-legal-research/
# 다음 파일들이 보여야 함: SKILL.md  references/
```

---

## 🚀 사용법

설치 후 Hermes Agent가 관련 쿼리에 자동으로 스킬을 로드합니다. 사용 예시:

**"Search the Federal Register for FTC proposed rules on AI from 2026"**
→ Federal Register API 호출 → 출처 태그가 포함된 구조화된 Regulation Digest 반환

**"Find recent case law on open source code and trade secret"**
→ web_search를 가진 서브에이전트 실행 → `[verify]` 태그가 포함된 결과 반환 → Reviewer Note 포함

**"Build a chronology from these deposition dates"**
→ 중요도 마커가 포함된 연대표 템플릿 사용

**"Research the legal framework for AI governance in healthcare"**
→ 전체 리서치 루프 실행: 수집 → 종합 → 격차 표시 → Reviewer Note와 함께 전달

### 리서치 루프 작동 방식

```
   ┌─────────────┐
   │ 목표 설정   │  (리서치 질문 정의)
   └──────┬──────┘
          ▼
   ┌─────────────┐
   │ 수집       │  (Federal Register API + web_search)
   └──────┬──────┘
          ▼
   ┌─────────────┐
   │ 종합       │  (출처 태그와 함께 발견사항 추출)
   └──────┬──────┘
          ▼
   ┌─────────────┐     ┌─────────────┐
   │ 검증       │────→│ 더 필요한가?│──→ 반복
   └──────┬──────┘     └─────────────┘
          ▼ 아니오
   ┌─────────────┐
   │ 전달       │  (Reviewer Note + 출처 태그 포함)
   └─────────────┘
```

### 출력 형식

모든 리서치 결과물은 다음을 포함합니다:

```markdown
> **⚠️ Reviewer note**
> - **출처:** Federal Register API / delegated web search
> - **검증 필요 항목:** [N개 항목 `[verify]` 표시 | 없음]
> - **최종 확인일:** [날짜]
> - **의존 전 확인사항:** [우선 검증할 1-2가지]

[출처 태그가 포함된 리서치 내용]

*[draft — attorney review required]*
```

---

## 📁 프로젝트 구조

```
oh-my-hermes-for-legal-researcher/
├── LICENSE                                    # Apache 2.0
├── README.md                                  # 영문 README
├── README.ko.md                               # 한국어 README (여기)
└── research/
    └── us-legal-research/
        ├── SKILL.md                           # 메인 스킬: 방법론 및 워크플로우
        └── references/
            └── open-source-trade-secret-cases.md  # 편집된 판례 참조
```

---

## 📜 라이선스 및 저작자 표시

**Apache 2.0** — [LICENSE](./LICENSE) 참조.

이 프로젝트는 **[Anthropic](https://www.anthropic.com/)** 의 **[claude-for-legal](https://github.com/anthropics/claude-for-legal)** (Apache 2.0)에서 파생되었습니다. 원작품의 저작권은 Anthropic에 있습니다.

이식된 claude-for-legal 구성 요소:
- 핵심 리서치 방법론 (출처 표시, 검증, 불확실성 공개)
- 규제 리서치 워크플로우 (`regulatory-legal` 플러그인 패턴)
- 소송 문서 템플릿 (`litigation-legal` 플러그인 — 클레임 차트, 연대표)
- Reviewer Note 및 면책조항 컨벤션 (플러그인 공통 표준)

Hermes Agent 적용 및 추가 워크플로우: **copylawbot**

---

## 🤝 기여하기

PR 환영합니다! 기여 가능한 영역:

- 추가 법률 참조 판례 및 인용
- 다른 무료 법률 API 지원 (GovInfo, Congress.gov — 키 사용 가능 시)
- 특정 실무 영역을 위한 워크플로우 개선
- 추가 문서 템플릿

Apache 2.0 라이선스와 원본 claude-for-legal 프로젝트에 대한 저작자 표시를 유지해 주세요.

---

> **면책조항:** 이 도구는 법률 리서치를 보조하지만 법률 자문을 제공하지 않습니다. 모든 출력은 변호사 검토가 필요한 초안입니다. AI 생성 인용은 조작될 수 있습니다 — 의존 전 모든 인용을 원출처에서 검증하세요.
