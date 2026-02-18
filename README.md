# AI Trend News

AI Agent 개발자를 위한 **월간 AI 트렌드 다이제스트**를 자동으로 수집, 평가, 요약하여 공유하는 레포지토리입니다.

매월 발행되는 이슈 파일은 [`issues/`](issues/) 디렉토리에서 확인할 수 있습니다.

---

## 어떻게 수집되나요?

모든 콘텐츠는 [AI-Trend](https://github.com/) 자동화 파이프라인에 의해 사람의 개입 없이 수집 · 평가 · 요약됩니다. 전체 프로세스는 아래 4단계로 구성됩니다.

### 1단계 — 멀티소스 수집 (Collection)

비동기 수집기(`httpx` + `asyncio`)가 아래 소스들을 병렬로 크롤링합니다.

| 소스 | 수집 방법 | 주요 대상 |
|------|-----------|-----------|
| **RSS / Atom Feeds** | `feedparser`로 피드 파싱 | AI 연구소 블로그, 개인 연구자 블로그, arXiv 프리프린트 |
| **Hacker News** | Algolia Search API | AI 관련 키워드 검색 (50+ points 필터) |
| **GitHub** | GitHub Search API + Trending 스크래핑 | AI 토픽 레포 (100+ stars), 주간 트렌딩 |

#### RSS 피드 소스 목록

**AI 연구소 / 기업 블로그**
- OpenAI Blog, Anthropic, Google AI Blog, Microsoft Research, Meta Engineering

**프레임워크 / 도구**
- LangChain Blog, Hugging Face Blog

**개인 연구자 / 학술**
- Simon Willison, Lilian Weng, Sebastian Raschka, BAIR Blog (UC Berkeley)

**arXiv 프리프린트**
- cs.AI (Artificial Intelligence), cs.CL (Computation and Language), cs.MA (Multiagent Systems)

#### Hacker News 검색 키워드
`AI agent`, `LLM`, `large language model`, `RAG retrieval`, `GPT`, `Claude`, `machine learning`, `transformer`

#### GitHub 토픽
`ai-agent`, `llm`, `langchain`, `rag`, `machine-learning` (Python, TypeScript)

### 2단계 — 다차원 스코어링 (Scoring)

수집된 각 아티클에 대해 4개의 독립적인 스코어(0~100)를 산출하고, 가중 합산하여 최종 **Composite Score**를 계산합니다.

```
Composite = 0.25 × Social + 0.15 × Authority + 0.15 × Recency + 0.45 × LLM Quality
```

| 스코어 | 산출 방식 |
|--------|-----------|
| **Social** | HN points, Reddit score, GitHub stars를 `log1p` 정규화 후 플랫폼별 max 선택 |
| **Authority** | 소스별 사전 정의된 신뢰도 점수 (예: OpenAI Blog 95, arXiv 85, HN 60) |
| **Recency** | 발행일 기준 지수 감쇠 (`half_life = 14일`) — 최신 콘텐츠에 가중치 부여 |
| **LLM Quality** | LLM이 평가하는 4개 항목 (각 0~25점): Relevance, Depth, Actionability, Novelty |

### 3단계 — 분류 및 요약 (Classification & Summarization)

Composite Score 상위 아티클을 대상으로 LLM 기반 2-Phase 처리를 수행합니다.

- **Phase 1** (Claude Haiku): 키워드 매칭 + LLM 분류로 7개 카테고리에 배정
- **Phase 2** (Claude Sonnet): AI Agent 개발자 관점의 한국어 기술 요약 (2~3 문단) 생성

#### 7개 카테고리

| 카테고리 | 설명 |
|----------|------|
| AI Agent | 자율 에이전트, 멀티 에이전트, MCP, A2A, 도구 사용, 계획/메모리 |
| LLM | 모델 출시, 벤치마크, 파인튜닝, 프롬프트 엔지니어링, 추론 최적화 |
| RAG & Knowledge | RAG, 벡터DB, 임베딩, 지식그래프, 문서처리 |
| MLOps & Infra | 배포, 서빙, 모니터링, vLLM, GPU/TPU 인프라 |
| AI Applications | AI 제품, 코딩 어시스턴트, 산업 적용 사례 |
| AI Safety & Policy | 정렬, 해석가능성, 규제, 레드팀, 가드레일 |
| Open Source & Tools | 오픈소스 프로젝트, 개발자 도구, 라이브러리/SDK |

### 4단계 — 다이제스트 발행 (Rendering)

Jinja2 템플릿을 사용하여 카테고리별로 정렬된 마크다운 파일을 렌더링하고, 이 레포지토리의 `issues/` 디렉토리에 `YYYY-MM.md` 형식으로 발행합니다.

---

## 선정 기준 요약

아티클이 최종 다이제스트에 포함되려면 다음 조건들의 **가중 합산 점수**가 높아야 합니다:

1. **커뮤니티 반응** (25%) — HN, GitHub 등에서의 실제 engagement
2. **출처 신뢰도** (15%) — Tier 1 연구소/블로그일수록 높은 가중치
3. **최신성** (15%) — 14일 반감기 지수 감쇠, 오래된 글은 자연스럽게 필터링
4. **콘텐츠 품질** (45%) — LLM이 평가하는 관련성, 깊이, 실용성, 참신성

---

## 디렉토리 구조

```
ai-trend-news/
├── README.md
└── issues/
    └── YYYY-MM.md    # 월간 AI 트렌드 다이제스트
```

---

## 라이선스

이 레포지토리의 콘텐츠는 자동 수집 · 요약된 것이며, 각 원문의 저작권은 해당 저작자에게 있습니다. 요약본은 정보 공유 목적으로 작성되었습니다.
