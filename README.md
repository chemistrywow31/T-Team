# T-Team — Tech-Advisor

> Brand: **T-Team** · Folder: `tech-advisor` · Codename: `Tech-Advisor`

[繁體中文](#繁體中文) · [English](#english) · [📖 Glossary](#-glossary--術語對照表) · [🚀 Quick Start](#-quick-start--快速啟動)

---

## 繁體中文

### 一句話介紹

T-Team 是一個 **技術方案設計諮詢團隊**。
你給它一個技術問題，它幫你產出 **業界格式（ADR + C4 + Tech Radar）的專業設計文件**，並在過程中強制要求「來源追溯、對立辯論、AI 失敗模式分析」。

簡單說：**像個內部技術顧問，但會故意找自己的麻煩、會引用每個說法的出處、會考慮 AI 自己會壞的情況。**

### 什麼時候該用？

| 場景 | 用 T-Team？ |
|---|---|
| 「我要做一個語音轉文字服務，該怎麼選技術棧？」 | ✅ |
| 「multi-agent 還是單一 LLM 比較適合我這個案例？」 | ✅ |
| 「我想評估從 PostgreSQL 換到 MongoDB 的可行性」 | ✅ |
| 「幫我寫一個 Python class」 | ❌（用一般 Claude 即可）|
| 「這段 code 有 bug 幫我修」 | ❌ |

### 核心能力與亮點

#### 1. **三維分析框架**（Layer 1/2/3 + Layer-A/B/C）

兩個維度同時看一個技術方案：

- **知識來源維度**（Layer 1/2/3）
  - L1：已建立的最佳實踐
  - L2：當前社群趨勢
  - L3：第一性原理推導
- **存在性證明維度**（Layer-A/B/C）
  - A：單元件層級——這個技術組件存不存在？
  - B：編排層級——這個架構組合別人做過嗎？
  - C：跨域層級——這個跨領域整合誠實揭露限制

#### 2. **強制證據鏈**

每個說法都必須掛上：
- **來源**（URL、文件、訪談）
- **時間**（資料時效，舊於 6 個月會自動標 recency warning）
- **可信度**（high / medium / low / UNVERIFIED）

未驗證的說法強制標 `UNVERIFIED:` 前綴，**禁止以肯定句呈現**。

#### 3. **對立辯論機制（Phase 2.5）**

Level 4 諮詢時自動觸發。兩個對立 agent：
- **Solution Advocate**：找支持證據、論證可行性
- **Solution Skeptic**：找反例、挑漏洞、提出失敗情境

辯論到收斂或標明分歧，避免單一視角誤導決策。

#### 4. **AI 失敗模式分析**

明確分析以下 AI 特有失敗：
- LLM tool-call schema 幻覺
- Context 截斷導致關鍵指令遺失
- Retry loop 無限循環
- Silent quality degradation（看似正常但品質悄悄下降）

並要求三層 Eval：Unit / LLM-as-Judge / Production Sampling。

#### 5. **AI/Multi-agent 決策樹**

每個牽涉 AI 或 multi-agent 的建議，必須先過：
- ROI ≥ 15× 才考慮（否則建議單純自動化）
- 有沒有專責工程資源？
- 是否真的 Level 4 場景？
- 跨域數量是否合理？

避免「為了用 AI 而用 AI」的反模式。

#### 6. **業界標準格式輸出**

- **ADR**：每個關鍵決策一張紙
- **C4**：Level 1-3 架構圖
- **Tech Radar**：技術選型分 Adopt / Trial / Assess / Hold

### 工作流程（6+1 階段）

```
Phase 1  需求探索      ← 訪談你，搞清楚真正要解決的問題
Phase 2  多維度分析    ← 6 dimensions × Layer A/B/C 平行分析
Phase 2.5 對立辯論     ← (Level 4 觸發) Advocate vs Skeptic
Phase 3  技術選型      ← Tech Radar 評等 + 決策樹
Phase 4  UX/流程設計   ← 使用者旅程 + 互動設計
Phase 5  規格撰寫      ← ADR + C4 + 完整 spec
Phase 6  審查         ← spec 審查 + 流程審查
```

Phase 1-4 需要你親自參與並確認；Phase 5-6 是團隊內部作業。

### T-Team 的成員

| Agent | 職責 |
|---|---|
| **Solution Coordinator** | 協調全流程、決定何時觸發 Phase 2.5 |
| **Requirements Analyst** | 訪談、釐清真實需求 |
| **Solution Analyst** | 6 維度分析、Layer-A/B/C 存在性證明 |
| **Solution Advocate** | 找方案的支持證據（Phase 2.5）|
| **Solution Skeptic** | 找方案的反例與漏洞（Phase 2.5）|
| **UX Designer** | 設計使用者旅程與互動流程 |
| **Spec Writer** | 產出 ADR + C4 + 完整 spec |
| **Spec Reviewer** | 審查 spec 內容正確性 |
| **Code Reviewer** | 審查 spec 內 code 片段語法 |
| **Process Reviewer** | 審查整個流程協作品質 |

### 設計理念

1. **務實優先**——boring tech 勝過 exciting tech
2. **湖優於海**——把可完成範圍 100% 完成，而非把無底範圍切 10%
3. **立場優於騎牆**——每個建議必須有明確立場 + 證據 + 可被反證的條件
4. **抗 AI 過度工程**——multi-agent 不是預設答案，是條件達成才用

---

## English

### One-Liner

T-Team is a **technical solution design advisory team**.
Give it a technical problem; it produces a **professional spec document in industry-standard format (ADR + C4 + Tech Radar)** while enforcing **source traceability, adversarial debate, and AI failure-mode analysis** throughout the process.

In short: **a senior tech advisor that picks fights with itself, cites every claim, and never forgets that AI itself can break.**

### When to Use

| Scenario | Use T-Team? |
|---|---|
| "I want to build a speech-to-text service — how do I pick the stack?" | ✅ |
| "Should I use multi-agent or a single LLM for this case?" | ✅ |
| "I want to evaluate migrating from PostgreSQL to MongoDB" | ✅ |
| "Write me a Python class" | ❌ (use plain Claude) |
| "Fix the bug in this code" | ❌ |

### Core Capabilities & Highlights

#### 1. **Two-Dimensional Analysis Framework** (Layer 1/2/3 + Layer-A/B/C)

Every solution is examined along two orthogonal axes:

- **Knowledge-source axis** (Layer 1/2/3)
  - L1: Established best practices
  - L2: Current community trends
  - L3: First-principles reasoning
- **Existence-proof axis** (Layer-A/B/C)
  - A: Component level — does this building block exist?
  - B: Orchestration level — has this combination been done?
  - C: Cross-domain level — honest disclosure of integration limits

#### 2. **Mandatory Evidence Chain**

Every claim must carry:
- **Source** (URL, document, interview transcript)
- **Recency** (data freshness; > 6 months triggers a recency warning)
- **Credibility** (high / medium / low / UNVERIFIED)

Unverified claims must be prefixed with `UNVERIFIED:` — **assertive phrasing is forbidden** for these.

#### 3. **Adversarial Debate** (Phase 2.5)

Triggered automatically for Level 4 consultations. Two opposing agents:
- **Solution Advocate**: Hunts for supporting evidence and feasibility proofs
- **Solution Skeptic**: Hunts for counter-evidence, edge cases, failure scenarios

They debate until convergence or until divergence is documented — preventing single-perspective blind spots.

#### 4. **AI-Specific Failure-Mode Analysis**

Explicitly examines:
- LLM tool-call schema hallucination
- Context truncation losing critical instructions
- Retry loops without termination
- Silent quality degradation (looks fine, slowly rotting)

And mandates a three-tier eval: Unit / LLM-as-Judge / Production Sampling.

#### 5. **AI/Multi-Agent Decision Tree**

Any AI- or multi-agent-flavored recommendation must first pass:
- Is the ROI ≥ 15×? (otherwise: plain automation)
- Are there dedicated engineering resources?
- Is this genuinely a Level 4 scenario?
- Is the cross-domain count justified?

Stops the "AI for AI's sake" anti-pattern.

#### 6. **Industry-Standard Output Formats**

- **ADR**: One sheet per key decision
- **C4**: Level 1-3 architecture diagrams
- **Tech Radar**: Tech choices binned into Adopt / Trial / Assess / Hold

### Workflow (6+1 phases)

```
Phase 1  Requirements Discovery   ← interview you, find the real problem
Phase 2  Multi-dimensional Analysis ← 6 dimensions × Layer A/B/C in parallel
Phase 2.5 Adversarial Debate      ← (Level 4 only) Advocate vs Skeptic
Phase 3  Technology Selection     ← Tech Radar tiering + decision tree
Phase 4  UX / Flow Design         ← user journey + interaction design
Phase 5  Spec Authoring           ← ADR + C4 + full spec
Phase 6  Review                   ← spec review + process review
```

Phases 1-4 require your participation and confirmation; Phases 5-6 are internal.

### T-Team Roster

| Agent | Role |
|---|---|
| **Solution Coordinator** | Orchestrates the full flow, decides when Phase 2.5 fires |
| **Requirements Analyst** | Interviews, clarifies the real requirement |
| **Solution Analyst** | 6-dimensional analysis, Layer-A/B/C existence proofs |
| **Solution Advocate** | Finds supporting evidence (Phase 2.5) |
| **Solution Skeptic** | Finds counter-evidence and gaps (Phase 2.5) |
| **UX Designer** | Designs user journey and interaction flow |
| **Spec Writer** | Produces ADR + C4 + full spec |
| **Spec Reviewer** | Audits spec content correctness |
| **Code Reviewer** | Audits code snippets embedded in the spec |
| **Process Reviewer** | Audits collaboration quality of the whole run |

### Design Philosophy

1. **Pragmatism first** — boring tech beats exciting tech
2. **Lakes over oceans** — fully finish a scoped lake instead of half-finishing an unbounded ocean
3. **Position over hedging** — every recommendation must carry a position, supporting evidence, and a falsification condition
4. **Anti-AI-over-engineering** — multi-agent is not the default answer; it must clear the decision tree

---

## 📖 Glossary — 術語對照表

對應上文中提到的所有專有名詞。**中英並列、原生表達**，不是機器翻譯。

### ADR — Architecture Decision Record / 架構決策紀錄

> Michael Nygard, 2011

**EN**: A short document (usually one page) that records *why* a particular technical choice was made. Format: Context · Decision · Consequences · Alternatives. The point is to give future maintainers a reason — three years from now, someone asking "why didn't we pick MongoDB?" deserves an answer that isn't archaeology.

**中文**：一張紙（通常一頁），記錄「**當時為什麼選了這個技術**」。固定四欄：背景／決策／後果／替代方案。三年後新人問「為什麼當初不選 MongoDB」，有 ADR 就有答案；沒 ADR 就變考古。

**Example**:
```
Title: Why we chose PostgreSQL over MySQL
Date: 2026-04-26 · Status: Adopted
Context: Need to store user-uploaded JSON with strong consistency
Decision: PostgreSQL 14
Consequences: Slightly heavier ops + cloud cost; richer JSONB queries
Alternatives: MySQL (rejected — weak JSONB), MongoDB (rejected — overkill)
```

---

### C4 Model — Software Architecture Visualization / 軟體架構視覺化

> Simon Brown, formalized 2018

**EN**: Draw architecture in **four zoom levels**, from globe to code:

| Level | Shows | Analogy |
|---|---|---|
| L1 Context | My system + the outside world | Google Maps satellite view |
| L2 Container | Services inside my system | City map |
| L3 Component | Modules inside one service | Neighborhood map |
| L4 Code | Class diagrams (rarely drawn) | House interior plan |

C4 replaces UML's chaos with a clean four-tier zoom. Most teams stop at L3.

**中文**：從遠到近用四個層級畫架構圖。L1 全景（系統與外界）、L2 城市地圖（系統內服務）、L3 街區地圖（服務內模組）、L4 房子內部圖（類別圖，通常不畫）。比 UML 簡單、比手繪有結構。多數團隊畫到 L3。

---

### Tech Radar — Technology Radar / 技術雷達

> ThoughtWorks, 2010

**EN**: A target chart tracking *what we currently think* about each technology. Four concentric rings:

| Ring | Meaning |
|---|---|
| **Adopt** | Default choice for new work |
| **Trial** | Validated, used in limited scope |
| **Assess** | Watching, researching |
| **Hold** | Don't start new projects with this |

Updated quarterly. It's the **health check chart for your tech stack** — and your team's collective opinion about each tool.

**中文**：一張靶圖，追蹤「我們目前對某個技術的態度」。四個同心圓：Adopt（採用）/ Trial（試用）/ Assess（評估）/ Hold（暫停新案）。每季更新一次，是「技術棧的健康檢查表」。

---

### ToT — Tree of Thoughts / 思考樹

> Yao et al., 2023 (NeurIPS)

**EN**: An LLM reasoning method. Instead of generating *one* chain of thought, the model spawns **N parallel branches**, evaluates each, prunes the worst, and continues from the best — like minimax search over reasoning paths.

**Use it when**: technology selection, multi-option comparison, complex decisions.
**Cost**: 5-20× more tokens than plain CoT.

**中文**：讓 LLM 同時生 N 條推理路徑、各自評分、剪掉差的、從好的繼續分岔。**像下棋時想「如果走 A 對手怎麼回？走 B 呢？」然後選最好的**。適合多方案比較。代價：token 成本上升 5-20×。

---

### CoVe — Chain of Verification / 驗證鏈

> Meta AI, 2023

**EN**: An anti-hallucination method. The LLM:
1. Generates a draft answer
2. Lists verification questions about that answer
3. Answers each verification question independently
4. Revises the draft if verification answers contradict it

**Use it when**: output contains specific facts (numbers, versions, API names, citations).

**中文**：抗幻覺技巧。LLM 先給草答 → 自己列「我這答案有哪些可驗證的點？」→ 一題一題回答 → 發現矛盾就修正。**像你寫完報告自己再讀一遍抓錯誤**。產出含具體事實（數字、版本、API 名）時必用。

---

### Self-Consistency / 自洽採樣

> Wang et al., 2022 (Google)

**EN**: Sample the same prompt N times with non-zero temperature, then take majority vote on the final answer. Cheap, surprisingly effective: +17.9% on GSM8K.

**中文**：同一題用隨機性問 N 次，多數決投票出答案。便宜有效，GSM8K 數學題提升 17.9%。

---

### ReAct — Reasoning + Acting / 推理與行動交錯

> Yao et al., 2022 (Princeton/Google)

**EN**: Interleave **think → act → observe → think** in a single trace. Lets the model use tools (search, calculator, API) while reasoning, instead of guessing facts. Foundation pattern for tool-using agents.

**中文**：把「想一步 → 用工具查 → 觀察結果 → 再想一步」交錯放進同一條推理鏈。讓 model 邊想邊查，而不是亂猜事實。**現在所有會用工具的 AI agent 都用這個基底模式**。

---

### Plan-and-Solve / 先計畫再執行

> Wang et al., 2023

**EN**: First produce an explicit plan (numbered steps), then execute against the plan. Reduces "drift" where the model wanders mid-reasoning.

**中文**：先列計畫（編號步驟），再依計畫執行。避免推理過程中游移、跳躍、忘記目標。

---

### Reflexion / 反思學習

> Shinn et al., 2023

**EN**: After a task fails, the model writes a self-critique ("why did I fail?") and stores it in episodic memory. The critique is fed into the next attempt. The model literally learns from its mistakes within a session.

**中文**：失敗後模型自己寫「我為什麼錯」筆記，存進記憶；下次嘗試時把筆記當作上下文。**讓 model 在 session 內從自己的錯誤學習**。

---

## 🚀 Quick Start — 快速啟動

### Invoke / 啟動指令

```bash
/boss
```

The entry-point skill spawns Solution Coordinator and runs the full T-Team workflow. You'll be guided through each phase interactively.
此入口 skill 會啟動 Solution Coordinator，跑完整的 T-Team 工作流程。每個階段會互動式引導你進行。

### Folder Structure / 目錄結構

```
teams/tech-advisor/
├── README.md           ← (this file / 本檔)
├── CLAUDE.md           ← team instructions / 團隊規範
└── .claude/
    ├── agents/         ← 10 agents (after upgrade / 升級後 10 個)
    ├── skills/         ← 9 skills incl. /boss entry / 9 個 skill
    ├── rules/          ← 5 rules / 5 條規則
    └── settings.json   ← hooks + permissions
```

### Versioning / 版本

- **v1.0** — 2026-03 · Original 7-agent technical-solution-design team
- **v2.0** — 2026-04 · Renamed to T-Team (Tech-Advisor); added evidence chain, adversarial debate, AI failure modes, ADR/C4/Tech Radar formats, code reviewer; sonnet → opus across all judgment-bearing agents

See `.worklog/202604/technical-solution-design-upgrade/` for the upgrade decision trail.
升級的完整決策證據鏈見 `.worklog/202604/technical-solution-design-upgrade/`。

---

## License & Attribution

Generated and maintained by [A-Team](../../) — the team designer.
本團隊由 [A-Team](../../) 團隊設計器產出與維護。
