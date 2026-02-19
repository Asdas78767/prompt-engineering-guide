# CLAUDE.md — prompt-engineering-guide

This repository is a **Claude Skill** (plugin) that provides a comprehensive reference guide for engineering AI chatbot prompts — particularly for character roleplay, visual novel, and TRPG use cases on Korean AI chat platforms.

---

## Repository Overview

**Type:** Documentation-only skill (no build system, no tests, no package manager)
**Language:** Korean (content), with English technical terms mixed in
**Purpose:** Reference guide for writing, compressing, and optimizing AI chatbot prompts

The skill auto-triggers when users request help with AI chatbot prompt writing, token compression, character creation, system design, or JSX UI components on platforms like Nekochat, Lunatalk, and Barb.

---

## File Structure

```
prompt-engineering-guide/
├── CLAUDE.md                         ← This file (AI assistant guide)
├── README.md                         ← Minimal project description
├── INSTALL.md                        ← Installation guide for Claude Desktop / Claude Code
├── SKILL.md                          ← Main skill entry point (read first on trigger)
└── references/
    ├── token-compression.md          ← Token compression techniques & symbol substitution
    ├── narrative-techniques.md       ← Narrative principles & descriptive writing rules
    ├── character-creation.md         ← 19-section character creation framework
    ├── system-design.md              ← System prompts, status panels, story progression, lorebooks
    ├── jsx-guide.md                  ← JSX renderer guide for Nekochat UI components
    ├── nekochat-templates.md         ← Nekochat reserved template reference ({{char}}, {{dN}}, etc.)
    ├── checklist.md                  ← Final review checklist & blank prompt template
    └── platform-notes.md             ← Platform-specific and model-specific notes
```

---

## How the Skill Works

`SKILL.md` is the entry point. It contains:
1. A YAML front-matter block with the skill `name`, `description`, and trigger keywords
2. A reference table mapping each task type to the appropriate `references/` file
3. Core principles and prompt structure overview

When triggered, Claude reads `SKILL.md` first, then loads only the relevant `references/` file(s) for the specific task. **Do not read all reference files at once** unless the task requires a full review.

### Trigger → Reference File Mapping

| User Task | Reference File to Load |
|-----------|------------------------|
| Compress / optimize prompt | `references/token-compression.md` |
| Improve narrative / prose quality | `references/narrative-techniques.md` |
| Create or refine a character | `references/character-creation.md` |
| Design system prompts, status panels, lorebooks | `references/system-design.md` |
| Build JSX UI components | `references/jsx-guide.md` |
| Use Nekochat-specific features | `references/nekochat-templates.md` |
| Final prompt review | `references/checklist.md` |
| Platform-specific adjustments | `references/platform-notes.md` |

---

## Content Conventions

### Document Language
All content is written in Korean. When editing reference files, maintain Korean for all explanatory prose. Technical terms (JSX, MBTI, API names, mathematical symbols) remain in English or as-is.

### Markdown Style
- Use `##` and `###` for section headers (not `#` within reference files, since `#` is used as a prompt section marker in the domain language)
- Code examples for prompt snippets go in fenced code blocks
- Tables are used extensively for structured comparisons — preserve this format
- The `◇`, `§`, `¶`, `∧`, `∨`, `¬`, `∴`, `∵`, `→` symbols are **domain vocabulary**, not formatting errors

### Prompt Symbol Vocabulary
These symbols appear throughout the content as part of the prompt-engineering grammar taught by this skill:

| Symbol | Meaning in prompt context |
|--------|--------------------------|
| `§` | Major section divider |
| `◇` | Sub-topic divider |
| `¶` | Line break / list item |
| `∧` | AND |
| `∨` | OR |
| `¬` | NOT / forbidden |
| `∴` | Therefore / result |
| `∵` | Because / reason |
| `→` | Triggers / leads to |
| `↑↓` | Increase / decrease |
| `①②③` | Named entity substitution (characters) |
| `♡♣♠` | Named numeric substitution (affection, etc.) |

Do not mistake these for broken formatting — they are intentional compressed notation taught to users.

---

## Key Domain Concepts

### Token Compression
The core philosophy: **increase information density, do not discard information**. Key techniques covered in `references/token-compression.md`:
- Symbol substitution (`∧`, `∨`, `¬`, `→`)
- Term aliasing (token aliasing table at the top of the prompt)
- Key-value conversion of narrative sentences
- Condition compression (`♡60↓손잡기→손빼며-5~-15`)
- Hierarchy flattening (max 2 levels of nesting)
- Deduplication strategies

### Prompt Section Order
Standard recommended order for a chatbot prompt:
1. Error-correction keywords
2. Term substitution table (`#명칭`)
3. OOC (role definition)
4. Core rules / priority hierarchy
5. AI thinking engine (`#엔진`)
6. Content (world rules, interaction rules, narrative principles)
7. Negative prompting (`#표현부정`)
8. Character data (`#C` / `#인물`)
9. Story progression (`#전개`)
10. Output format
11. Status panel (at the very bottom)

### AI Attention Weight by Position
```
Top of prompt:    ████████████ 100%
Upper quarter:    ███████████  90%
Middle:           ████████     70%
Lower quarter:    ██████       55%
Bottom:           ████         40%
```
Critical rules should be placed at both the top AND their relevant section (intentional double-placement, not redundancy).

### Status Panel
Always placed at the bottom of the prompt. Output as a code block at the end of every AI response. Contains: day, time, location, relationship, mood/mode, inner thoughts.

### Lorebook Strategy
- Main prompt: system rules, engine, term aliases, character summaries, output format, status panel
- Lorebook: detailed character data, story stages, modes/routes, deification-prevention rules, NPCs, DLC content

---

## Platform Context

This skill targets these Korean AI chatbot platforms:

| Platform | Key Notes |
|----------|-----------|
| **Nekochat (네코챗)** | JSX rendering supported; auxiliary prompt (보조프롬프트) separation; `fetch` is blocked |
| **Lunatalk (루나톡)** | 800-char infinite prompt + 500-char lorebook backup recommended |
| **Barb (바베)** | Output length directives don't apply; SVG/HTML/JSX status panels supported |
| **Caveduck (케이브덕)** | Accurate token counting; symbol compression highly effective |
| **Teapot (티팟)** | HTML prompts supported; impersonation-prevention prompt needed |
| **Eve/Elin (이브/엘린)** | Separate output-adjustment prompts required |

Model tier affects compression strategy:
- **Low-tier models** (Gemini Flash, etc.): Avoid operator symbols; use natural language; add more examples
- **High-tier models** (Gemini Pro, GPT-4, etc.): Aggressive compression with all symbol types works well

---

## JSX Component Rules (Nekochat)

When writing JSX for the Nekochat platform, these constraints are absolute:

- Pure functional components only (`const X = () => { ... }`)
- No `export default`; entry point is a bare `<Component />` at the end
- Inline styles only (`style={{}}`) — no CSS imports
- Maximum **10 top-level definitions** per character across all JSX files
- Sub-components must NOT be declared inside the main component (causes `ReferenceError`)
- Forbidden APIs: `window`, `document`, `localStorage`, `fetch`, `eval`, `import`
- React hooks allowed: `useState`, `useEffect`, `useMemo`, `useCallback`

---

## Workflow for Common Tasks

### New Prompt (from scratch)
1. Read `SKILL.md` for section structure
2. Read `references/character-creation.md` for character setup
3. Read `references/system-design.md` for system/mechanics
4. Final review with `references/checklist.md`

### Compress an Existing Prompt
1. Read `references/token-compression.md`
2. Apply compression techniques
3. Verify with `references/checklist.md`

### Improve Narrative Quality
1. Read `references/narrative-techniques.md`
2. Apply data→sensation conversion, sensory description rules, negative prompting

### Build JSX UI
1. Read `references/jsx-guide.md`
2. Set up AI variable update instructions in the prompt
3. Build the React component following the constraint rules

### Platform-Specific Tuning
1. Read `references/platform-notes.md`
2. Adjust compression level and symbol usage for target model tier

---

## Editing Guidelines for AI Assistants

1. **Read before editing.** Always read the target file fully before making changes.
2. **Preserve domain symbols.** The `◇`, `§`, `∧`, `¬`, `→` etc. are intentional vocabulary — do not normalize them to plain text.
3. **Maintain Korean prose.** Keep all explanatory text in Korean unless the original was in English.
4. **Keep code examples exact.** Prompt snippets in code blocks are precise templates — preserve whitespace, symbols, and structure.
5. **No new files without explicit instruction.** All content belongs in the existing `references/` files or `SKILL.md`.
6. **Do not add emoji** unless the existing file uses emoji in that context (some files use emoji for UI illustration).
7. **Table alignment.** Markdown tables in this repo are dense — keep them formatted cleanly with pipe alignment.
8. **SKILL.md front matter.** The YAML block at the top of `SKILL.md` (between `---` delimiters) defines the skill metadata. Preserve its format exactly.

---

## Git Workflow

- Default branch: `master`
- No CI/CD, no linting, no automated tests
- All changes are documentation edits; commit messages should describe which reference section was updated and why
- Example commit message: `Update token-compression.md: add kanji abbreviation section examples`
