---
name: sme-research-report
description: >
  Generate a structured research briefing (调研简报) for small and medium-sized enterprises (SMEs),
  startups, or any company the user wants to investigate. Use this skill whenever the user says
  things like "调研一下 X 公司", "帮我做一个关于 X 的调研报告", "我想了解 X 公司的情况",
  "research company X", "generate a company briefing for X", or anything implying they want
  a structured analysis of a company. The output is a self-contained HTML report that mirrors
  the structure and visual quality of a professional investment/BD research brief. Always trigger
  this skill for company research tasks — even if the request is casual or only names a company
  without explicitly asking for a "report".
  Supports three audience modes: BD/investment due diligence, job-seeker background check, and
  business partnership evaluation. Each mode adjusts section weights and writing tone.
---

# SME Research Report Skill v2.0

Generate professional, evaluative company research briefings (调研简报). Output is a polished, self-contained HTML file **and an accompanying full-page PNG long screenshot** generated via Puppeteer + Chrome headless.

---

## Workflow Overview

1. **Clarify scope** — company name, audience mode, special focus
2. **Disambiguate** — lock down full legal entity name before any broad search
3. **Research** — parallel searches across 5-7 data dimensions
4. **Synthesize** — map findings to report structure with evaluative judgment
5. **Render** — produce HTML using the template + visual component catalog
6. **Generate PNG** — full-page long screenshot via Puppeteer + Chrome headless
7. **Validate** — quality checklist before delivery

---

## Step 0 — Audience Mode Detection

Before any research, determine WHO will read this report. This changes everything.

| Mode | Trigger phrases | Section weights | Writing tone |
|------|----------------|-----------------|--------------|
| **投资/BD尽调** | "调研一下" "帮我做个报告" "了解一下" | Balanced all sections | Neutral-analyst, flag risks explicitly |
| **求职者背调** | "面试" "求职" "背调" "加入" "值不值得去" | Heavy on §四(Team) + §六(Funding stability); lighter on §二(Credentials) | Evaluative for candidate: "this matters because..." |
| **商务合作评估** | "合作" "供应商" "采购" "代理" | Heavy on §三(Product) + §五(Market) + §二(Credentials) | Due-diligence: reliability, compliance, capacity |

**For job-seeker mode specifically:**
- Every caution/warn block should include "求职者须知" perspective
- Add interview question suggestions at key decision points
- Flag: option/equity feasibility, runway risk, founder commitment signals
- Flag: name confusion risks (similar company names with complaints)
- In §六, explicitly suggest asking about cash runway and funding timeline

**For early-stage startups (founded < 2 years, < 100 employees):** Proactively ask audience mode if not specified.

---

## Step 1 — Name Disambiguation (MANDATORY first research step)

**Always run this query first, before any other research:**
```
[公司名] 公司 注册 法人 天眼查 OR 企查查
```

Lock down:
- Full legal entity name (e.g., "北京银河智学教育科技有限公司" ≠ "银河智学")
- Confirm there isn't a similarly-named company in a different industry
- Note: brand name ≠ legal name (e.g., DREO ≠ 深圳市和生创新技术有限公司)

**Name confusion check:** Search `[公司简称] 投诉 OR 纠纷 OR 骗` to see if similarly-named entities have negative records. Flag these explicitly in the report to avoid guilt-by-association.

---

## Step 2 — Research Strategy

### Query Matrix (run in parallel batches)

**Batch 1 — Identity & Legal (always first):**
```
"[公司全称]" 天眼查 OR 企查查
"[公司全称]" 法定代表人 股东
"[创始人名] [公司名] 创业 背景"
```

**Batch 2 — Product & Market:**
```
"[公司名] OR [品牌名] 产品 业务 模式"
"[公司名] OR [品牌名] [行业词] 市场 排名"
"[行业词] 竞争 格局 2025 2026"
```

**Batch 3 — Team & Recruitment:**
```
"[公司名] Boss直聘 招聘 OR [公司名] 猎聘"
"[创始人名] 经历 OR 背景 OR 前职"
"[公司名] 团队 规模 架构"
```

**Batch 4 — Funding & Credentials:**
```
"[公司名] 融资 OR 估值 OR 投资"
"[公司名] 专利 OR 资质 OR 高新技术"
"[公司名] OR [品牌名] 获奖 OR 荣誉"
```

**Batch 5 — Sentiment & Risk:**
```
"[品牌名] OR [产品名] 评价 OR 投诉 OR 用户体验"
"[公司名] 风险 OR 纠纷 OR 问题"
```

### Research Rules
- Always run Batch 1 first, then Batches 2-5 in parallel
- Prioritize sources from the last 12 months
- If a company is in a specific vertical (e.g., cross-border e-commerce), add vertical-specific queries (e.g., Amazon Best Seller check)
- For recruitment-based org inference, note department clusters from job listings
- **Record source + date for every major data point** — this feeds the `.source` citations

### Handling Data Gaps
- "暂无公开数据" is acceptable and honest — never fabricate
- "基于[来源]推断" with explicit basis (e.g., "基于招聘岗位部门分布推断")
- Add ⚠️ prefix to all inferred data points
- If a whole section would be empty, shrink it rather than deleting it

---

## Step 3 — Report Structure & Writing

### Company Maturity Detection

| Signal | Mode |
|--------|------|
| Founded > 2 years, has revenue data, has formal certifications | **Standard** (all 8 sections, balanced) |
| Founded < 2 years, no revenue, no public funding, < 100 employees | **Startup** (adjusted — see below) |

### Startup Mode Adjustments
- **Expand §四 (Team)** — full founder career timelines with prior companies; "跟对人比选对公司更关键" framing
- **Expand §五 (Market)** — standalone 赛道竞争格局 with 3-tier table
- **Shrink §二 (Credentials)** — brief paragraph; explain WHY credentials are missing (e.g., "成立不足1年，不满足高新认定条件")
- **Shrink §六 (Funding)** — note 0 funding, add capital risk assessment, suggest runway questions for interviews
- **Add** product lineage subsection in §三 if product evolved from prior company

### The 7-Section Structure

#### 核心结论 (Executive Summary)
- **1 verdict sentence** (not just description — make a judgment)
- **5-7 evaluative tags**: ✅ strengths, ⚠️ risks, 🔵 unknowns/opportunities
- Tags should be specific and data-backed, not generic ("172项专利" not "有专利")

Example verdict: "DREO 是一家从亚马逊跨境起家、快速品牌化的智能家电公司。2021→2024 年营收由约 3 亿增至约 20 亿元（CAGR ≈ 88%），美国/德国 Amazon 多品类排名第一，当前正向全渠道 + IoT 生态 + AI 驱动的科技型家电公司转型。"

#### 一、公司概况 (Company Overview)
- Key-value table: 指标 | 数据 | 补充
- Must include: full legal name, brand, founding date, legal rep, registered capital, HQ, revenue milestone(s), headcount, latest funding stage
- If revenue multi-year: show CAGR
- Data anomalies get footnotes (e.g., 社保 vs 实际人数的差额说明)

#### 二、企业资质 (Credentials)
- Table: 资质/指标 | 详情 | 认定年份/说明
- If early-stage with no credentials: explain WHY (not just "暂无") and note when they'll become eligible
- IP counts are compelling even for early companies — always search for these

#### 三、主营业务 (Core Business)
- Summary must state: business model type (B2B/B2C/B2B2C), core value prop, primary market
- Table: 场景 | 代表产品/服务 | 市场地位
- For startups with product lineage: add "产品技术传承" blockquote explaining prior-company origins
- Separate business model table: 维度 | 详情 (pricing, target customers, differentiation)

#### 四、团队规模与组织架构 (Team)
- Summary should tell the "why this team" story, not just recite numbers
- For founder-driven companies: individual founder timelines with career milestones
- Org evolution table: 阶段 | 时间 | 核心特征 | 架构变化
- Org chart (pre block) with `.infer-warn` banner above it
- Recruitment signals table: 方向 | 在招岗位特征 | 信号解读

**Writing rule for §四:** "这是一家'创始人驱动型'公司——核心团队的履历权重远超公司本身。" — this framing matters for early-stage companies.

#### 五、市场表现 (Market)
- Channel performance table
- **赛道竞争格局 (MANDATORY):** 3-tier competitor table with subject company's differentiation clearly stated
- For companies with real-time ranking data: use summary-box KPI cards before detail tables (总-分 pattern)
- For companies with data contradictions: add caution block explaining the discrepancy
- Use comparison-grid for side-by-side region/channel comparison

#### 六、融资与品牌背书 (Funding + Brand)
- Merge thin funding and brand sections into one h2 (avoids "一句话章节" problem)
- Funding timeline table: 时间 | 事件 | 金额/估值 | 投资方
- For 0-funding companies: explain what this means, add caution block with capital risk assessment
- Brand honors table: 类别 | 详情
- Label all valuation estimates: "估值区间为基于融资金额和行业估值惯例的推算，非官方数据"

#### 七、发展策略 (Strategic Direction)
- Summary should be evaluative: "当前 AI/IoT 布局仍处早期，成效有待验证"
- Table: 主线 | 具体举措 | 风险/不确定性 (the risk column is mandatory)
- Strategy inferences from hiring patterns should be labeled

#### 附录 — 产品深度调研
- Per-product table: 属性 | 详情
- Competitive comparison table: split pricing/rating into separate columns for scanability
- Product cards with images if available (use product-grid)
- Name confusion warnings if applicable

### WRITING VOICE: The Most Important Section

**Every section-summary must be evaluative, not just descriptive.**

| ❌ Descriptive (weak) | ✅ Evaluative (strong) |
|-----------------------|------------------------|
| "公司持有172项专利" | "知识产权密度在同体量跨境电商中处于头部水平" |
| "当前0融资" | "0外部融资意味着期权短期无法兑现，建议面试时直接询问现金跑道" |
| "产品覆盖20-30所学校" | "20-30所是'实验田'规模，2026秋续费率才是验证商业模型的关键" |

**Section-summary formula:** Fact + Context + Judgment
- Fact: what the data says
- Context: why this matters relative to industry norms
- Judgment: what this implies for the reader

---

## Step 4 — Visual Component Catalog

### Blockquote Variants (callout boxes)
```html
<blockquote>                  <!-- Blue: neutral info -->
<blockquote class="warn">     <!-- Red: risk/danger -->
<blockquote class="success">  <!-- Green: positive/strength -->
<blockquote class="caution">  <!-- Yellow: uncertainty/attention needed -->
```

### When to Use Each Callout
- `warn`: competitive threats, data gaps that matter, name confusion risks
- `success`: clear competitive advantages, strong team background, verified strengths
- `caution`: data contradictions, uncertain strategic bets, capital risk flags, inferred data that matters
- `blockquote` (blue, default): product lineage, methodology notes, contextual background

### Layout Patterns

**Pattern A — 总-分 (Summary → Detail):**
Use when you have an overall assessment followed by detailed breakdown.
```
KPI summary-box cards
  ↓
Blockquote judgments (warn/success/caution)
  ↓
Detail tables (often in comparison-grid)
```

**Pattern B — 分-总 (Detail → Summary):**
Use when data leads to a conclusion.
```
Detail table
  ↓
Blockquote judgment
```

**Pattern C — Side-by-Side Comparison:**
Use for A vs B comparisons (regions, products, competitors).
```html
<div class="comparison-grid">
  <div>...Table A...</div>
  <div>...Table B...</div>
</div>
```

### KPI Cards (summary-box)
Place at key junctions where several metrics together tell a story:
- At the start of a data-heavy subsection
- Before detail tables in 总-分 layout
- In §五 to summarize market position numerically

### Product Cards (product-grid)
Use for visual product showcases in the appendix. Each card: image + name + key stats + pros/cons.
Max 3 cards per row; stack on mobile.

### Conflict Badge (conflict-note)
Use inline in table cells when a data point contradicts another source:
```html
<span class="conflict-note">⚠️ 见下注</span>
```
Always follow with a caution blockquote explaining the discrepancy.

### Inference Warning (infer-warn)
Place above any content inferred from non-official sources:
```html
<div class="infer-warn">
  ⚠️ 以下架构基于公开招聘数据推断，非公司官方披露，仅供参考，请勿直接对外引用。
</div>
```

---

## Step 5 — HTML Rendering Rules

1. **Read `assets/template.html`** — it contains all CSS classes referenced above
2. **Use the exact CSS class names** from the template — do not invent new ones
3. **Monetary values:** 万元/亿元 for CNY; $/€ for foreign currency
4. **Dates:** YYYY年MM月DD日 consistently in body text; YYYY-MM-DD acceptable in tables
5. **Inferred data:** ⚠️ prefix + footnote explaining inference basis
6. **Source citations:** `📌` prefix at end of each section, listing specific sources
7. **Footer:** "本简报基于公开信息整理，仅供内部参考。数据采集自[来源列表]（[日期]）。"
8. **Output path:** `~/[公司简称]_调研简报.html` and `~/[公司简称]_调研简报.pdf`

### Section Merging Rules
- If a section would have ≤ 2 rows of content, merge it into the adjacent section
- Typical merges: 融资 + 荣誉 → "融资与品牌背书"; 资质 + 荣誉 → depends on data richness
- Never force a standalone section with thin content — it looks like unfinished work

### Content Density Rules
- Table: when comparing 3+ structured data points
- List: when enumerating items without comparison
- Prose: for narrative, judgment, context
- Blockquote: for callout judgments, risk flags, methodology notes
- summary-box: for 3-5 KPI snapshots

---

## Step 6 — Generate Full-Page PNG Long Screenshot

After writing the HTML file, produce a full-page PNG long screenshot using the bundled `assets/screenshot.js` script. This script uses Puppeteer-core (pre-installed globally) + the system's existing Chrome to render the HTML and capture the entire page as a single seamless image.

### Command

```bash
NODE_PATH=$(npm root -g) node /tmp/sme-skill/sme-research-skill/assets/screenshot.js \
  "[ABS_PATH_TO_HTML]" \
  "[ABS_PATH_TO_PNG]"
```

### Prerequisites

- **puppeteer-core** — pre-installed globally via `npm install -g puppeteer-core` (no bundled Chromium, uses system Chrome)
- **Google Chrome** — macOS: `/Applications/Google Chrome.app/Contents/MacOS/Google Chrome`; Linux: `google-chrome` or `chromium`
- The `assets/screenshot.js` script auto-detects Chrome at the above paths

### Rules

- **Output filename:** Same as HTML, with `.png` extension (e.g., `~/广州海尔信息科技_调研简报.png`)
- **Use absolute paths** — both input HTML and output PNG must be absolute paths
- **Run after HTML is finalized** — do not regenerate PNG if HTML has errors
- **Viewport:** 980px wide (matches the `.container` CSS max-width), height auto-calculated by `fullPage: true`
- **File size:** Typically 1-2 MB for a standard report — well within acceptable range
- The `NODE_PATH=$(npm root -g)` prefix is required so Node can find the globally installed puppeteer-core

### Fallback

If puppeteer-core is not installed or Chrome is not found:
- Skip PNG generation and inform the user which dependency is missing
- Do NOT try to install packages or Chrome automatically
- The HTML report alone still satisfies the core deliverable

---

## Step 7 — Quality Checklist

Before delivering the HTML:

- [ ] Name disambiguation confirmed (legal name ≠ brand name)
- [ ] Audience mode reflected in section weights and writing tone
- [ ] Every section-summary is evaluative (Fact + Context + Judgment)
- [ ] Every major data point has a specific source citation
- [ ] Inferred data (org chart, valuation, strategy) clearly labeled
- [ ] Data contradictions called out with conflict-note + caution blockquote
- [ ] No thin standalone sections (merged if ≤ 2 rows)
- [ ] Total-分 layout used where appropriate
- [ ] No fabricated numbers
- [ ] Core conclusions match body evidence
- [ ] Footer includes source list and date
- [ ] HTML validates (no broken tags, proper nesting)
- [ ] Mobile responsive (check comparison-grid, product-grid, exec-tags)
- [ ] PDF generated and opens correctly (check `ls -lh [path].pdf` to confirm non-empty)

---

## Common Pitfalls (Updated)

- **Skipping name disambiguation** — always lock down legal entity before broad search
- **Repeating the same data point across sections** — if 11,000+ stores appears in §五, reference it briefly in §七, don't re-explain
- **Descriptive-only summaries** — every section-summary needs judgment, not just description
- **Thin standalone sections** — a section with one paragraph and no table looks unfinished; merge or expand
- **Not calling out data contradictions** — if Source A says "#1 for 2 years" but real-time data shows otherwise, flag it
- **Missing the "so what"** — every data point should answer "why should the reader care?"
- **Using rigid 8-section structure for startups** — adjust weights per maturity mode
- **Conflating brand name with legal name** — always clarify both
- **Headcount from platforms as exact** — note discrepancies (社保 vs 招聘平台)
- **No risk column in §七 development strategy table** — strategic bets always have risks
- **Placing Amazon BSR rankings in non-ecommerce company reports** — only for cross-border e-commerce companies
- **Presenting org charts as official** — always label as inference
- **Forgetting the PDF step** — after HTML is written, always run Chrome headless print-to-pdf to generate the accompanying PDF

---

## Reference Files

- `references/sources.md` — detailed source list with query templates
- `assets/template.html` — HTML/CSS template with full component library
