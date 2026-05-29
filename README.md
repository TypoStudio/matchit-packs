# matchit-packs

[Match It](https://github.com/TypoStudio/matchit) 게임의 **학습팩 저장소**입니다. 게임 본체와 분리해 모든 팩을 여기서 관리하고, GitHub Pages로 배포합니다. 게임은 시작할 때 [`packs.json`](packs.json) 카탈로그를 읽어 기본 학습팩 목록을 구성합니다.

- 기본 카탈로그: `https://typostudio.github.io/matchit-packs/packs.json`
- 사용자는 게임의 **추가팩 관리**에서 다른 팩 JSON URL을 더할 수 있습니다(목록은 사용자 기기에만 저장).

## 사용법 (팩 추가)

1. 팩 폴더를 성격 폴더 아래(`{폴더}/{id}/`) 만들고, 아래 둘 중 한 구조로 둡니다.
   - **단일 파일**: `pack.json` 하나에 메타 + 인라인 레벨(`levels: [{level,label,items}]`).
   - **메타 + 레벨파일**: `pack.json`(메타, `levels: [{level,label,file}]`) + `1.json`, `2.json` …(문항 배열).
2. [`packs.json`](packs.json) 카탈로그에 한 줄(`name`/`url`/`levels`) 추가합니다.
3. 저장소를 **GitHub Pages** 로 배포합니다 (Settings → Pages → Branch: `main` / root).

> 카탈로그를 거치지 않고 단일 팩 URL(`pack.json`)을 게임의 **추가팩 관리**에 바로 넣어도 됩니다.

## 카탈로그 (`packs.json`)

기본 학습팩 **목록(포인터)** 입니다. 각 항목은 **이름·URL·레벨수**만 가집니다.

```json
[
  { "name": "한자능력시험", "url": "language/hanja-grade/pack.json", "levels": 16 },
  { "name": "과일 모으기",  "url": "arcade/fruit-emoji/pack.json",    "levels": 2 }
]
```

- `url` 은 이 `packs.json` 위치 기준 **상대경로**(개별 팩 파일). 게임이 절대 URL로 해석합니다.
- `levels` 는 **레벨 개수**(목록 표시용). 실제 레벨은 각 팩 파일이 정의합니다.
- 표시 순서는 게임이 정합니다: **추가팩 → 기본팩**, 각 그룹 이름 **가나다순**.

## 개별 팩 파일 (`pack.json`)

`levels` 는 레벨 객체 배열입니다. 각 레벨은 `level`(번호), `label`(표시 이름), 그리고 `items`(인라인 문항) **또는** `file`(같은 폴더 기준 상대경로의 문항 파일)을 가집니다.

```jsonc
// 단일 파일형 — 모든 레벨을 한 파일에 인라인(items)
{ "id": "fruit-emoji", "title": "과일 모으기", "accent": "#f97316", "random": true,
  "levels": [
    { "level": 1, "label": "1", "items": [ /* 문항 */ ] },
    { "level": 2, "label": "2", "items": [ … ] }
  ] }

// 메타 + 레벨파일형 — 각 레벨이 외부 파일(file)을 가리킴. label은 자유(예: 급수명)
{ "id": "hanja-grade", "title": "한자능력시험", "accent": "#dc2626",
  "levels": [
    { "level": 1,  "label": "8급",  "file": "./1.json" },
    { "level": 14, "label": "1급",  "file": "./14.json" }
  ] }
```

- `file` 은 `pack.json` 위치 기준 상대경로(문항 배열). 게임이 절대 URL로 해석해 지연 로드합니다.
- `label` 은 레벨 선택 UI에 그대로 표시됩니다(없으면 `Lv {level}`).

## 폴더 구성

학습팩은 성격별 하위 폴더로 정리합니다. 각 팩은 `{폴더}/{id}/pack.json`(+ 레벨파일) 구조입니다.

| 폴더 | 성격 | 예시 팩 |
| --- | --- | --- |
| `language/` | 어학·문자 학습 | 한자능력시험(`hanja-grade`, 급수별 16레벨), 영어단어(`english-word`, 중1~고3 레벨), 영문법 |
| `korean/` | 국어 | 사자성어(`korean-idiom`), 속담(`korean-proverb`) |
| `science/` | 과학 | 화학식(`science-core`) |
| `math/` | 수학 | 수학 공식(`math-formula`, 과목별: 중등·공통수학·수학Ⅰ·Ⅱ·미적분·기하·확률과통계) |
| `history/` | 역사 | 국사(`korean-history`), 세계사(`world-history`) |
| `arcade/` | 모으기·감각형 놀이 | 과일 모으기(`fruit-emoji`, `random`) |

새 성격의 팩을 추가할 때는 어울리는 폴더가 없으면 새 폴더를 만들고 이 표·카탈로그에 추가하세요.

## 팩 형식

게임은 아래 3가지 형태를 모두 자동 인식합니다.

### 형태 1 — 통합 단일 파일 (메타 + 모든 레벨)

`levels`를 `{ level, items }` 배열로 주면 문항을 한 파일에 담습니다.

```json
{
  "id": "my-vocab",
  "title": "내 단어장",
  "accent": "#0ea5e9",
  "format": "word",
  "levels": [
    { "level": 1, "items": [
      { "word": "go", "meaning": "가다", "hint": "동작" },
      { "word": "run", "meaning": "달리다" }
    ] },
    { "level": 2, "items": [ { "word": "eat", "meaning": "먹다" } ] }
  ]
}
```

### 형태 2 — 팩 목록 / 카탈로그 (레벨 파일 분리)

팩 배열에 `base`(레벨 파일 기준 경로, 끝에 `/`)를 지정하면 `base + {level}.json` 에서 레벨을 읽습니다. `base`는 이 파일 위치 기준 **상대경로**나 절대 URL 모두 가능하고, `levels`는 **숫자(레벨 개수)** 또는 번호 배열입니다.

```json
[
  {
    "id": "my-vocab",
    "title": "내 단어장",
    "accent": "#0ea5e9",
    "format": "word",
    "base": "language/my-vocab/",
    "levels": 3
  }
]
```
→ `language/my-vocab/1.json`, `2.json`, `3.json` … 각각에 문항 배열을 둡니다. (이것이 `packs.json` 카탈로그가 쓰는 형태입니다.)

### 형태 3 — 문항 배열만

URL이 문항 배열을 직접 가리키면 1레벨짜리 팩으로 자동 래핑됩니다.

```json
[
  { "word": "go", "meaning": "가다", "hint": "동작" },
  { "word": "run", "meaning": "달리다" }
]
```

## 필드

| 필드 | 설명 |
| --- | --- |
| `id` | 고유 식별자 (ascii kebab-case) |
| `title` | 팩 이름 |
| `accent` | 강조색 (생략 가능) |
| `format` | `"word"`(단어팩: `word`/`meaning`/`hint`) 또는 `"lesson"`(LessonItem). 생략 시 자동 판별 |
| `base` | 레벨 파일 기준 경로 (끝에 `/`). 상대경로/절대 URL 모두 가능. 통합·문항배열 형태에는 불필요 |
| `category` | 폴더 성격 표기(선택). 게임 동작에는 영향 없음 |
| `random` | `true`면 보드/낙하 블럭을 정답 유도 없이 **완전 무작위**로 채움(과일 모으기처럼 같은 토큰을 모으는 매치형에 적합) |
| `bidirectional` | `true`면 **출제 방향 토글** 노출. lesson 팩의 역방향은 답(label)을 문제로, 원래 문제(prompt)를 정답 블럭으로 뒤집음. 단어팩(`format:"word"`)은 자동 양방향 |
| `directions` | 방향 버튼 라벨 `{ "asis": "한자 → 훈음", "reverse": "훈음 → 한자" }`(lesson 양방향용) |
| `board` | 기본 보드 사이즈 `{ "cols": 5, "rows": 5 }`. 팩 선택 시 적용(미지정 7×7). 모으기 모드는 토큰 길이 ≤ 보드 한 변 |
| `wide` | `true`면 가로로 긴 블럭(토큰이 긴 영문법·수학 공식 등) |
| `blockStyle` | 기본 블럭 디자인 `jelly`/`card`/`tile`/`transparent`. 팩 선택 시 적용 |
| `levels` | **숫자(레벨 개수)** 또는 번호 배열(파일 분리) 또는 `[{level,label,items|file}]`(객체 배열) |

### 문항 형식

- **단어팩** (`format: "word"`): `{ "word": "go", "meaning": "가다", "hint": "동작" }` — `hint`는 선택.
- **일반팩** (`format: "lesson"`): `{ "id": "water", "label": "물", "prompt": "물 만들기", "tokens": ["H","H","O"], "hint": "H2O" }`

## 주의

- 브라우저 `fetch`가 되려면 **CORS 허용 + JSON 직접 서빙**이 필요합니다. GitHub Pages는 둘 다 만족합니다.
- GitHub Wiki는 raw JSON을 적절히 서빙하지 않아 부적합합니다.
