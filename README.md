# matchit-packs

[Match It](https://github.com/TypoStudio/matchit) 게임의 **외부 학습팩 저장소**입니다. 게임 본체와 분리해 팩을 관리하고, GitHub Pages로 배포해 게임에서 URL로 불러옵니다.

## 사용법

1. 이 저장소에 팩 JSON을 추가하고 push 합니다.
2. 저장소를 **GitHub Pages** 로 배포합니다 (Settings → Pages → Branch: `main` / root).
3. 게임 화면의 **JSON URL** 칸에 배포된 주소를 입력합니다.
   - 예: `https://typostudio.github.io/matchit-packs/packs.json`

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

### 형태 2 — 여러 팩 목록 (레벨 파일 분리)

팩 배열에 `base`(레벨 파일 기준 URL, 끝에 `/`)를 지정하면 `base + {level}.json` 에서 레벨을 읽습니다.

```json
[
  {
    "id": "my-vocab",
    "title": "내 단어장",
    "accent": "#0ea5e9",
    "format": "word",
    "base": "https://typostudio.github.io/matchit-packs/my-vocab/",
    "levels": [1, 2, 3]
  }
]
```
→ `my-vocab/1.json`, `my-vocab/2.json` … 각각에 문항 배열을 둡니다.

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
| `base` | 레벨 파일을 분리할 때의 기준 URL (끝에 `/`). 통합·문항배열 형태에는 불필요 |
| `levels` | 숫자 배열(파일 분리) 또는 `[{level,items}]`(인라인) |

### 문항 형식

- **단어팩** (`format: "word"`): `{ "word": "go", "meaning": "가다", "hint": "동작" }` — `hint`는 선택.
- **일반팩** (`format: "lesson"`): `{ "id": "water", "label": "물", "prompt": "물 만들기", "tokens": ["H","H","O"], "hint": "H2O" }`

## 주의

- 브라우저 `fetch`가 되려면 **CORS 허용 + JSON 직접 서빙**이 필요합니다. GitHub Pages는 둘 다 만족합니다.
- GitHub Wiki는 raw JSON을 적절히 서빙하지 않아 부적합합니다.
