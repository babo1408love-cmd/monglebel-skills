---
name: monglebel-image-gen
description: "MongeulBell image generation skill. Automatically generates game art images (cards, UI, characters, environments) using Pollinations AI API. Use PROACTIVELY whenever creating visual assets for MongeulBell."
argument-hint: "[description of what to generate]"
metadata:
  author: MongeulBell
  version: 1.0.0
  tags: [unity, image, generation, art, pollinations, monglebel]
  model: opus
---

# MongeulBell Image Generation Skill

MongeulBell 프로젝트의 모든 이미지 에셋을 **Pollinations AI API**로 자동 생성한다.
수동 복사-붙여넣기 없이, Claude Code가 직접 요청 → 다운로드 → 저장까지 자동 처리.

---

## 1. 생성 엔드포인트

```
https://image.pollinations.ai/prompt/{URL_ENCODED_PROMPT}?width={W}&height={H}&nologo=true&seed={SEED}
```

- **Method**: GET (URL에 프롬프트를 인코딩하여 요청)
- **Response**: PNG 이미지 바이너리
- **Rate Limit**: 요청 사이 2초 대기 권장

---

## 2. MongeulBell 공통 스타일 프리픽스

**모든 이미지 프롬프트 앞에 반드시 붙일 것:**

```
Style: Cute fantasy mobile game illustration.
Art direction: Rounded shapes, pastel colors, warm lighting, toy-like diorama feel, storybook aesthetic.
The image should feel cozy and child-friendly, like a magical toy come to life.
No text on the image. No UI elements unless explicitly requested.
```

---

## 3. 에셋 타입별 설정

| Asset Type | Width | Height | Aspect | Notes |
|------------|-------|--------|--------|-------|
| Card Illustration | 768 | 1024 | 3:4 | 세로 카드 |
| Card Frame/Back | 768 | 1024 | 3:4 | 세로 카드 |
| Character Portrait | 512 | 512 | 1:1 | 정사각형 |
| Enemy Sprite | 512 | 512 | 1:1 | 정사각형 |
| Spirit Portrait | 512 | 512 | 1:1 | 정사각형 |
| Tower Design | 512 | 768 | 2:3 | 세로형 |
| UI Icon | 256 | 256 | 1:1 | 작은 아이콘 |
| Background/Scene | 1024 | 576 | 16:9 | 배경 |
| Board Tile | 512 | 512 | 1:1 | 보드 타일 |

---

## 4. 자동 생성 프로세스

### Step 1: 프롬프트 구성
```
{공통 스타일 프리픽스}
Background: {배경 설명}
Subject: {구체적 내용 설명}
```

### Step 2: URL 생성
```python
import urllib.parse
encoded = urllib.parse.quote(prompt)
url = f"https://image.pollinations.ai/prompt/{encoded}?width={W}&height={H}&nologo=true&seed={seed}"
```

### Step 3: 다운로드
```python
import urllib.request
req = urllib.request.Request(url, headers={"User-Agent": "MongeulBell/1.0"})
with urllib.request.urlopen(req, timeout=120) as resp:
    with open(filepath, "wb") as f:
        f.write(resp.read())
```

### Step 4: 저장 위치

| Asset Type | Save Path |
|------------|-----------|
| Card Art | `Downloads/MongeulBell_Card_Images/` |
| Character Art | `Downloads/MongeulBell_Character_Art/` |
| Enemy Art | `Downloads/MongeulBell_Enemy_Art/` |
| UI Assets | `Downloads/MongeulBell_UI_Assets/` |
| Background | `Downloads/MongeulBell_Backgrounds/` |
| General | `Downloads/MongeulBell_Generated_Art/` |

---

## 5. 금지 스타일 (절대 프롬프트에 넣지 않기)

- `dark`, `horror`, `gore`, `blood`, `scary`
- `realistic`, `photorealistic`, `hyperrealistic`
- `sharp`, `aggressive`, `military`, `mechanical`
- `complex UI`, `cluttered`
- `ugly`, `disgusting`, `grotesque`

---

## 6. 권장 스타일 키워드

- `cute`, `chibi`, `kawaii`, `adorable`
- `rounded`, `soft`, `cozy`, `warm`
- `pastel`, `magical`, `sparkle`, `glow`
- `toy-like`, `diorama`, `storybook`
- `child-friendly`, `whimsical`, `fantasy`

---

## 7. 재생성 가이드

마음에 안 들면:
1. `seed` 값만 바꿔서 재시도 (같은 프롬프트, 다른 결과)
2. 프롬프트에 디테일 추가: "more rounded", "softer colors", "bigger eyes"
3. 원하는 분위기 키워드 추가: "more magical", "warmer lighting"

---

## 8. 배치 생성 스크립트 템플릿

여러 이미지를 한번에 생성할 때:

```python
import urllib.request, urllib.parse, os, time

OUTPUT_DIR = "Downloads/MongeulBell_Generated_Art/"
os.makedirs(OUTPUT_DIR, exist_ok=True)

STYLE_PREFIX = (
    "Style: Cute fantasy mobile game illustration. "
    "Art direction: Rounded shapes, pastel colors, warm lighting, "
    "toy-like diorama feel, storybook aesthetic. "
    "The image should feel cozy and child-friendly. "
    "No text on the image. "
)

images = {
    "filename1": "Subject description here",
    "filename2": "Subject description here",
}

for name, subject in images.items():
    prompt = STYLE_PREFIX + subject
    encoded = urllib.parse.quote(prompt)
    url = f"https://image.pollinations.ai/prompt/{encoded}?width=512&height=512&nologo=true"
    filepath = os.path.join(OUTPUT_DIR, f"{name}.png")

    print(f"Generating {name}...")
    req = urllib.request.Request(url, headers={"User-Agent": "MongeulBell/1.0"})
    with urllib.request.urlopen(req, timeout=120) as resp:
        with open(filepath, "wb") as f:
            f.write(resp.read())
    print(f"  Saved: {filepath}")
    time.sleep(2)
```

---

## 9. Unity 에셋 적용

생성된 이미지를 Unity에 적용할 때:
1. `Downloads/` 에서 `Assets/_MongleBell/Art/` 하위로 복사
2. Unity Inspector에서 Texture Type 설정:
   - Sprite: `Sprite (2D and UI)`
   - Max Size: 모바일 기준 `512` 또는 `1024`
   - Compression: `ASTC 6x6` (모바일 최적)
3. Sprite Atlas에 추가 (배칭 최적화)

---

## 10. 예시: 카드 1장 자동 생성

```
User: "꽃잎 궁수 카드 이미지 만들어줘"

Claude:
1. 공통 스타일 프리픽스 + 꽃잎 궁수 프롬프트 구성
2. Pollinations API URL 생성 (768x1024, 3:4)
3. Python urllib로 다운로드
4. Downloads/MongeulBell_Card_Images/BlossomArcher.png 저장
5. 결과 확인 (파일 크기, 저장 경로 보고)
```

End.
