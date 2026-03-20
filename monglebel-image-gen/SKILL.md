---
name: monglebel-image-gen
description: "MongeulBell image generation skill. Automatically generates game art images (cards, UI, characters, environments) using OpenAI DALL-E 3 API. Use PROACTIVELY whenever creating visual assets for MongeulBell."
argument-hint: "[description of what to generate]"
metadata:
  author: MongeulBell
  version: 2.0.0
  tags: [unity, image, generation, art, openai, dalle, monglebel]
  model: opus
---

# MongeulBell Image Generation Skill (OpenAI DALL-E 3)

MongeulBell 프로젝트의 모든 이미지 에셋을 **OpenAI DALL-E 3 API**로 자동 생성한다.
Claude Code가 직접 Python + openai 라이브러리로 요청 → 다운로드 → 저장까지 자동 처리.

---

## 1. 사전 요구사항

- **환경변수**: `OPENAI_API_KEY` 가 시스템에 설정되어 있어야 한다
- **Python 패키지**: `openai` (`python -m pip install openai`)
- **과금**: DALL-E 3 standard 1024x1024 = ~$0.04/장, HD = ~$0.08/장

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

| Asset Type | DALL-E Size | Notes |
|------------|-------------|-------|
| Card Illustration | 1024x1024 | 정사각형 (후처리로 3:4 크롭) |
| Card Frame/Back | 1024x1024 | 정사각형 |
| Character Portrait | 1024x1024 | 정사각형 |
| Enemy Sprite | 1024x1024 | 정사각형 |
| Spirit Portrait | 1024x1024 | 정사각형 |
| Tower Design | 1024x1792 | 세로형 |
| UI Icon | 1024x1024 | 정사각형 (후처리로 축소) |
| Background/Scene | 1792x1024 | 가로형 |
| Board Tile | 1024x1024 | 정사각형 |

DALL-E 3 지원 사이즈: `1024x1024`, `1024x1792`, `1792x1024`

---

## 4. 자동 생성 코드

### 단일 이미지 생성

```python
import os, urllib.request
from openai import OpenAI

client = OpenAI()  # OPENAI_API_KEY 환경변수 자동 사용

STYLE = (
    "Style: Cute fantasy mobile game illustration. "
    "Art direction: Rounded shapes, pastel colors, warm lighting, "
    "toy-like diorama feel, storybook aesthetic. "
    "The image should feel cozy and child-friendly. "
    "No text on the image. "
)

prompt = STYLE + "Background: Soft pink gradient. Subject: A tiny cute flower fairy archer on a petal tower."

response = client.images.generate(
    model="dall-e-3",
    prompt=prompt,
    size="1024x1024",
    quality="standard",
    n=1,
)

image_url = response.data[0].url
urllib.request.urlretrieve(image_url, "output.png")
```

### 배치 생성 (여러 장)

```python
import os, urllib.request, time
from openai import OpenAI

client = OpenAI()
OUTPUT_DIR = r"C:\Users\PC\Downloads\MongeulBell_Generated_Art"
os.makedirs(OUTPUT_DIR, exist_ok=True)

STYLE = (
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
    prompt = STYLE + subject
    print(f"Generating {name}...")
    response = client.images.generate(
        model="dall-e-3",
        prompt=prompt,
        size="1024x1024",
        quality="standard",
        n=1,
    )
    filepath = os.path.join(OUTPUT_DIR, f"{name}.png")
    urllib.request.urlretrieve(response.data[0].url, filepath)
    print(f"  Saved: {filepath}")
    time.sleep(1)
```

---

## 5. 저장 위치

| Asset Type | Save Path |
|------------|-----------|
| Card Art | `Downloads/MongeulBell_Card_Images/` |
| Character Art | `Downloads/MongeulBell_Character_Art/` |
| Enemy Art | `Downloads/MongeulBell_Enemy_Art/` |
| UI Assets | `Downloads/MongeulBell_UI_Assets/` |
| Background | `Downloads/MongeulBell_Backgrounds/` |
| General | `Downloads/MongeulBell_Generated_Art/` |

---

## 6. 금지 스타일 (절대 프롬프트에 넣지 않기)

- `dark`, `horror`, `gore`, `blood`, `scary`
- `realistic`, `photorealistic`, `hyperrealistic`
- `sharp`, `aggressive`, `military`, `mechanical`
- `complex UI`, `cluttered`
- `ugly`, `disgusting`, `grotesque`

---

## 7. 권장 스타일 키워드

- `cute`, `chibi`, `kawaii`, `adorable`
- `rounded`, `soft`, `cozy`, `warm`
- `pastel`, `magical`, `sparkle`, `glow`
- `toy-like`, `diorama`, `storybook`
- `child-friendly`, `whimsical`, `fantasy`

---

## 8. 재생성 가이드

마음에 안 들면:
1. 같은 프롬프트로 다시 요청 (DALL-E 3는 매번 다른 결과)
2. 프롬프트에 디테일 추가: "more rounded", "softer colors", "bigger eyes"
3. 원하는 분위기 키워드 추가: "more magical", "warmer lighting"
4. `quality="hd"` 로 변경 (더 세밀, 비용 2배)

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
2. OpenAI DALL-E 3 API 호출 (1024x1024, standard)
3. 반환된 URL에서 이미지 다운로드
4. Downloads/MongeulBell_Card_Images/BlossomArcher.png 저장
5. 결과 확인 (파일 크기, 저장 경로 보고)
```

End.
