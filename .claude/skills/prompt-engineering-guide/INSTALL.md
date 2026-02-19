# prompt-engineering-guide 스킬 설치 가이드

AI 챗봇 프롬프트 엔지니어링 종합 가이드 스킬입니다.

## 📁 파일 구조

```
prompt-engineering-guide/
├── SKILL.md                          ← 메인 스킬 파일
└── references/
    ├── token-compression.md          ← 토큰 압축 기법
    ├── narrative-techniques.md       ← 서사 원칙과 묘사 기법
    ├── character-creation.md         ← 캐릭터 설정 작성법
    ├── system-design.md              ← 시스템/상태창/전개/로어북
    ├── jsx-guide.md                  ← JSX 렌더러 가이드
    ├── nekochat-templates.md         ← 네코챗 예약 템플릿
    ├── checklist.md                  ← 종합 체크리스트 & 빈 템플릿
    └── platform-notes.md             ← 플랫폼별 유의사항
```

---

## 🖥️ Claude Desktop (claude.ai) 설치

1. 설정(Settings) → Features → "Agent Skills" 또는 "Skills" 활성화
2. 다운로드한 `prompt-engineering-guide` 폴더를 통째로 스킬 디렉토리에 복사:
   - **Windows**: `%APPDATA%\Claude\skills\user\prompt-engineering-guide\`
   - **macOS**: `~/Library/Application Support/Claude/skills/user/prompt-engineering-guide/`
3. Claude Desktop 재시작
4. 대화에서 "프롬프트 작성 도와줘" 등의 트리거로 스킬이 자동 활성화됨

---

## 💻 Claude Code 설치

### 방법 A: 프로젝트 로컬 스킬

1. 프로젝트 루트에 `.claude/skills/` 디렉토리 생성
2. `prompt-engineering-guide` 폴더를 복사:
   ```
   .claude/skills/prompt-engineering-guide/
   ├── SKILL.md
   └── references/
       └── ...
   ```
3. Claude Code가 자동 인식

### 방법 B: 전역 스킬

1. 홈 디렉토리에 스킬 배치:
   ```
   ~/.claude/skills/prompt-engineering-guide/
   ├── SKILL.md
   └── references/
       └── ...
   ```
2. 모든 프로젝트에서 사용 가능

### 방법 C: CLAUDE.md에서 참조

프로젝트의 `CLAUDE.md` 파일에 다음 추가:
```markdown
## Skills
- prompt-engineering-guide: /path/to/prompt-engineering-guide/SKILL.md
```

---

## 🔧 사용법

스킬이 설치되면 다음과 같은 요청에 자동 트리거됩니다:

- "AI 챗봇 프롬프트를 만들어줘"
- "이 프롬프트를 압축해줘"
- "캐릭터 설정을 잡아줘"
- "상태창을 설계해줘"
- "네코챗 JSX 컴포넌트를 만들어줘"
- "프롬프트를 리뷰해줘"

Claude는 SKILL.md를 먼저 읽고, 필요에 따라 적절한 references 파일을 추가로 참조합니다.
