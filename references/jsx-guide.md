# JSX 렌더러 & 스크립팅 가이드

AI 채팅 플랫폼에서 JSX 컴포넌트를 사용한 캐릭터 상태 시각화, 사용자 상호작용, AI 응답 제어 레퍼런스.

## 목차

1. [AI와 JSX의 관계](#1-ai와-jsx의-관계)
2. [스크립팅 API](#2-스크립팅-api)
3. [데이터 접근 (Props)](#3-데이터-접근-props)
4. [작성 규칙 & 제약사항](#4-작성-규칙--제약사항)
5. [플랫폼 함정 & 오류 방지](#5-플랫폼-함정--오류-방지)
6. [디버깅 팁](#6-디버깅-팁)
7. [작업 워크플로우](#7-작업-워크플로우)
8. [체크리스트](#8-체크리스트)

---

## 1. AI와 JSX의 관계

- **AI의 역할**: 텍스트 생성, 변수 값 변경 (`hp=50`, `mood="angry"`)
- **JSX의 역할**: 변경된 변수를 읽어 UI 업데이트 (빨간색 체력바, 화난 표정 등)
- **연결 고리**: `variables` props를 통한 데이터 전달

**중요**: AI는 JSX 코드를 직접 실행하지 않음. **데이터(변수)**를 변경하고, JSX가 감지하여 화면을 다시 그림.

---

## 2. 스크립팅 API (내장 함수)

JSX 컴포넌트 내 이벤트 핸들러(onClick 등)에서 호출 가능한 플랫폼 전용 함수.

### sendToAI(message, confirm?)

사용자가 입력한 것처럼 AI에게 메시지 전송.

```jsx
// 즉시 전송
<button onClick={() => sendToAI("공격한다!")}>⚔️ 공격</button>

// 확인 후 전송
<button onClick={() => sendToAI("전재산을 기부한다", true)}>💰 기부하기</button>
```

### reroll(guidance?, confirm?)

AI의 마지막 답변 재생성(Reroll).

```jsx
// 단순 리롤
<button onClick={() => reroll()}>🔄 다시 쓰기</button>

// 가이던스 포함 리롤
<button onClick={() => reroll("공격이 빗나가는 상황으로 다시 묘사해줘", true)}>
  🎲 운명 변경
</button>
```

### getPrevUserMessage()

직전 사용자 메시지 반환.

### getPrevCharMessage()

직전 캐릭터(AI) 메시지 반환.

```jsx
const CombatPanel = () => {
  const lastAct = getPrevCharMessage();
  const isUnderAttack = lastAct.includes("공격") || lastAct.includes("돌진");
  return (
    <button
      onClick={() => sendToAI("방패를 들어 막는다!")}
      style={{ background: isUnderAttack ? '#ffd700' : '#555' }}
    >
      🛡️ 방어 {isUnderAttack && "(추천!)"}
    </button>
  );
};
```

---

## 3. 데이터 접근 (Props)

| Prop | 타입 | 설명 | 예시 |
|------|------|------|------|
| `variables` | Object | **[핵심]** AI가 변경하는 게임 상태 변수 | HP, 호감도, 현재 장소 |
| `character` | Object | 캐릭터 기본 정보 | 프로필 카드 |
| `messages` | Array | 최근 대화 내역 | 로그, 말풍선 |
| `user` | Object | 사용자 정보 | 유저 이름 |

**AI 설정에서 변수 연동:**
```markdown
상황에 따라 다음 변수들을 갱신하라:
- `hp`: 체력 (0~100)
- `mood`: 기분 ("happy", "angry", "sad" 등)
- `location`: 현재 장소

대화 끝에 메타데이터 블록 포함:
`<metadata> {"hp": 45, "mood": "angry", "location": "Dungeon"} </metadata>`
```

---

## 4. 작성 규칙 & 제약사항

### 필수 규칙
1. **순수 함수형 컴포넌트**: `export default` 없이 컴포넌트 정의만
2. **단일 리턴**: 코드 마지막에 `<Component />` 형태의 리턴문 필수
3. **인라인 스타일**: CSS 파일 임포트 불가, `style={{}}` 사용
4. **Fragments**: 여러 요소 시 `<>...</>` 사용
5. **최상단 화살표 함수만**: `const X = () => { ... };` 형태만 허용
6. **정의 개수 제한 (최대 10개)**: 캐릭터당 모든 JSX 파일의 최상단 정의 합계 최대 10개

### 허용 기능
- **React Hooks**: `React.useState`, `React.useEffect`, `React.useMemo`, `React.useCallback`
- **이벤트**: `onClick`, `onChange`, `onSubmit` 등

### 금지 기능 (보안 제약)
- ❌ `window`, `document`, `navigator`
- ❌ `localStorage`, `sessionStorage`
- ❌ `alert()`, `confirm()`, `prompt()`
- ❌ `fetch`, `XMLHttpRequest`, `WebSocket`
- ❌ `eval()`, `new Function()`
- ❌ `import ... from ...`

---

## 5. 플랫폼 함정 & 오류 방지

### CRITICAL: 최상단 정의 개수 제한

캐릭터당 전체 JSX 파일의 최상단 정의 합계 **최대 10개**.

**허용:**
```jsx
const MyComponent = () => { ... };  // ✅
```

**거부:**
```jsx
function getGrade(score) { ... }     // ❌ function 선언
const PRODUCTS = { easy: [...] };    // ❌ 객체 리터럴
const PAY_AMOUNTS = [5000, 10000];   // ❌ 배열 리터럴
```

**해결: 파일당 최상단 1개.** 유틸리티 함수·데이터 상수는 컴포넌트 내부 선언.

### CRITICAL: 서브 컴포넌트 내부 선언 금지

메인 내부에서 서브 컴포넌트 선언 → `ReferenceError: Cannot access 'XXX' before initialization`

```jsx
// ❌ 에러
const MyApp = ({ data }) => {
  const StatusBar = ({ dark }) => (
    <div>{data.time}</div>
  );
  return <div><StatusBar dark={false} /></div>; // ← ReferenceError!
};
```

**해결법 A: 전부 인라인 (대형 컴포넌트 권장)**
```jsx
const MyApp = ({ data, currentScreen }) => {
  return (
    <div>
      {/* 상태바를 인라인으로 직접 작성 */}
      <div style={{ display: 'flex', padding: '10px' }}>
        <span>{data.time}</span>
      </div>
      {currentScreen === 'home' && (<div><h1>홈 화면</h1></div>)}
    </div>
  );
};
```

**해결법 B: 형제 레벨 선언 (소형, 독립적 UI 조각)**
⚠️ 형제 레벨도 최상단 정의 1개로 카운트됨.

| 상황 | 권장 |
|------|------|
| 복잡한 UI (스마트폰, 대시보드) | **A** (인라인) |
| 반복 UI가 메인 props에 의존 | **A** (인라인) |
| 독립적 순수 UI (진행바, 뱃지) | **B** (형제) |
| 서브 컴포넌트 5개 이상 필요 | **A** (인라인) |

### 메인 내부 선언 가능 vs 불가능

| 가능 ✅ | 불가능 ❌ |
|---------|----------|
| `const value = 42` (단순 값) | `const MyComp = () => (...)` (컴포넌트) |
| `const items = [{...}]` (데이터 배열) | `function MyComp() { return ... }` |
| `const calc = () => x + y` (유틸 함수) | `const MyComp = ({ props }) => (...)` |
| `React.useState`, `useEffect`, `useMemo` | — |

**핵심:** JSX 반환 함수 = 컴포넌트 → 내부 금지. 단순 값/배열/계산 = 데이터 → 내부 가능.

### IIFE 패턴 (복잡한 조건부 로직)

```jsx
{app === 'chat' && currentPerson && (() => {
  const chat = chatList.find(c => c.person === currentPerson);
  if (!chat) return null;
  return <div>{chat.messages.map((msg, i) => <div key={i}>{msg.text}</div>)}</div>;
})()}
```

주의: IIFE 끝에 `()` 빠뜨리면 렌더링 안 됨.

### 캐싱 주의

| 증상 | 원인 | 해결 |
|------|------|------|
| 이전 에러 계속 발생 | 기존 채팅방 캐싱 | **새 채팅방**에서 테스트 |
| UI 변하지 않음 | 브라우저 캐시 | 하드 리프레시 (Ctrl+Shift+R) |
| 간헐적 에러 | 구버전 JSX 참조 | 채팅 기록 삭제 후 재시작 |

### 자주 발생하는 에러

| 에러 | 원인 | 해결 |
|------|------|------|
| `Cannot access 'X' before initialization` | 내부에서 서브 컴포넌트 const 선언 | 인라인 또는 형제 레벨 |
| `X is not defined` | 내부에서 function 선언 | 동일 |
| `Too many re-renders` | 렌더링 중 setState 직접 호출 | 이벤트 핸들러 안에서만 |
| `Cannot read property of undefined` | props 없이 접근 | Optional Chaining (`?.`) |
| 코드가 텍스트로 보임 | AI가 코드블록으로 감쌈 | "코드블록 금지" 명시 |

---

## 6. 디버깅 팁

**변수 내용 출력:**
```jsx
<pre style={{ fontSize: '10px', background: '#000', padding: '10px' }}>
  {JSON.stringify(variables, null, 2)}
</pre>
```

**방어적 코딩:**
```jsx
const hp = variables?.hp ?? 100;
const hp = Number(variables?.hp) || 0;
```

---

## 7. 작업 워크플로우

1. **기획**: 시각화할 변수(HP, 호감도 등) 결정
2. **프롬프트 설정**: AI가 해당 변수 업데이트하도록 지시
3. **JSX 작성**: `variables` props 받아 UI 그리는 React 코드
4. **테스트**: 대화 진행 시 AI가 변수 변경 → UI 실시간 반영 확인

---

## 8. 체크리스트

```
□ 최상단에 메인 컴포넌트 1개만 있는가?
□ 전체 JSX 파일 최상단 정의 합계 ≤ 10개?
□ 메인 내부에 서브 컴포넌트 선언 없는가?
□ 형제 레벨 서브 컴포넌트가 메인 props/state에 의존하지 않는가?
□ 중괄호/소괄호 밸런스 맞는가?
□ 마지막에 return <Component /> 진입점 있는가?
□ window, document, fetch 등 금지 API 사용하지 않는가?
□ 새 채팅방에서 테스트했는가? (캐싱 방지)
```
