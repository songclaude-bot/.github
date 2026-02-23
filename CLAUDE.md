# songclaude-bot — Claude 가이드라인

이 계정은 사람과 AI(Claude)가 함께 만들어가는 프로젝트 공간입니다.
새로운 환경에서 작업을 시작할 때 이 문서를 먼저 읽고 따르세요.

---

## 초기 셋업

새 환경(EC2 등)에서 처음 시작할 때:

1. **사용자에게 GitHub PAT를 요청**하세요. 절대 토큰을 추측하거나 파일에 기록하지 마세요.
2. git 인증 설정:
   ```bash
   git config --global credential.helper store
   git config --global user.name "songclaude-bot"
   git config --global user.email ""
   ```
3. gh CLI 설치 및 인증
4. 한글 폰트 설치: `sudo apt install -y fonts-noto-cjk`
5. 이 repo를 clone하여 가이드라인 확인
6. 허브 페이지 repo(`songclaude-bot.github.io`) clone하여 현재 프로젝트 목록 파악

---

## 규칙

- **repo 삭제 금지**: `DELETE /repos` API를 절대 호출하지 않는다. 삭제가 필요하면 반드시 사용자에게 먼저 확인.
- **비밀 정보 커밋 금지**: PAT, API 키, .env 파일 등을 절대 공개 repo에 커밋하지 않는다.

## 브랜치 & 커밋 전략

- 작업마다 브랜치를 나눈다 (`feature/xxx`, `fix/xxx` 등)
- 작업 단위로 커밋한다 (한 커밋에 한 작업)
- 이렇게 하면 문제가 생겨도 언제든 복구 가능

---

## 디자인 가이드라인

모든 프로젝트 페이지는 통일된 감성을 유지합니다.
**레퍼런스**: https://songclaude-bot.github.io

### 테마
- 어두운 우주 배경 (`#0a0a0f` ~ `#08080d`)
- 반짝이는 별 canvas 애니메이션
- 보라/핑크 그라디언트 포인트

### 색상 팔레트
```
배경:       #0a0a0f, #08080d, #111118
텍스트:     #e0e0e0, #c8c8d0, #b0b0c0
텍스트(dim): #666680, #555, #444, #333
액센트1:    #f43f5e (핑크/레드)
액센트2:    #667eea (퍼플/블루)
액센트3:    #f093fb (라이트 핑크)
골드:       #fbbf24, #f59e0b (수상 등 특별 표시)
```

### 폰트
```
영문 UI:    'Inter'
코드/수치:  'JetBrains Mono'
한글:       'Noto Sans KR'
```
Google Fonts import:
```
https://fonts.googleapis.com/css2?family=Inter:wght@200;300;400;600&family=JetBrains+Mono:wght@300;400&family=Noto+Sans+KR:wght@200;300;400;500;600;700&display=swap
```

### 공통 UI 요소
- 별 배경: canvas에 랜덤 위치, 반짝임(alpha 변화) 애니메이션
- 스크롤 reveal: `IntersectionObserver`로 fade-up
- 구분선: `linear-gradient(90deg, transparent, #1a1a25, transparent)`
- 카드: `border-radius: 12px`, `border: 1px solid #1a1a25`, `background: #111118`

---

## 허브 페이지 구조

**repo**: `songclaude-bot/songclaude-bot.github.io`
**URL**: https://songclaude-bot.github.io

허브 페이지는 모든 프로젝트의 진입점입니다.

### 별 = 프로젝트
각 프로젝트는 화면의 빛나는 별로 표현됩니다.
- 마우스 호버(모바일: 탭) → 프로젝트 설명 툴팁
- 클릭 → 프로젝트 페이지로 이동

### 프로젝트 별 추가 방법

`songclaude-bot.github.io/index.html`에 아래 형식으로 추가:

```html
<a class="project-star fade-in delay-6"
   href="프로젝트_URL"
   style="top: Y%; left: X%;">
  <div class="star-dot"></div>
  <div class="star-tooltip">
    <div class="tt-name">프로젝트 이름</div>
    <div class="tt-desc">한줄 설명</div>
    <div class="tt-tag">태그</div>
  </div>
</a>
```

위치(`top`, `left`)는 기존 별들과 겹치지 않도록 배치하세요.

### 프로젝트 목록 확인
현재 프로젝트 목록은 이 파일이 아닌 **허브 페이지의 index.html**에서 확인하세요.
`project-star` 클래스를 검색하면 모든 프로젝트를 찾을 수 있습니다.

---

## 프로젝트 구조

```
songclaude-bot.github.io (허브, public)
  └─ 별 → 각 프로젝트 설명 페이지로 연결

프로젝트 설명 페이지 (public repo, GitHub Pages)
  └─ 소개, 스크린샷, 기술 스택, 스토리
  └─ 실제 서비스 링크 (있다면)

실제 서비스 코드 (private repo)
  └─ Netlify / Vercel / AWS 등 별도 호스팅
```

- **정적 설명 페이지**: GitHub Pages로 배포 (public)
- **서비스 코드**: private repo, 별도 플랫폼에서 호스팅
- 설명 페이지에서 실제 서비스로 링크

---

## 소개 페이지 템플릿

퍼블릭 프로젝트 소개 페이지를 만들 때 `project-page-template` repo를 활용하세요.

```bash
# 1. 템플릿 clone
gh repo clone songclaude-bot/project-page-template /tmp/template

# 2. 새 repo 생성 & 템플릿 복사
gh repo create songclaude-bot/{{프로젝트명}} --public --clone
cp -r /tmp/template/* {{프로젝트명}}/
cp -r /tmp/template/.github {{프로젝트명}}/

# 3. CLAUDE.md의 체크리스트대로 진행
```

템플릿에 포함된 내용:
- 우주 테마 index.html (플레이스홀더 교체만으로 커스텀)
- GitHub Pages workflow
- 프로젝트별 CLAUDE.md (상세 커스텀 가이드)

**주의**: 이 템플릿은 퍼블릭 소개 페이지 전용입니다. private 서비스 코드에는 사용하지 않습니다.

---

## 배포

### GitHub Pages (정적 페이지)

각 repo에 `.github/workflows/pages.yml`:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: ["main"]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v4
      - uses: actions/upload-pages-artifact@v3
        with:
          path: '.'
      - id: deployment
        uses: actions/deploy-pages@v4
```

Pages API로 활성화:
```bash
gh api repos/songclaude-bot/REPO_NAME/pages -X POST \
  -f "build_type=workflow" -f "source[branch]=main" -f "source[path]=/"
```

### 새 프로젝트 생성 체크리스트

1. GitHub repo 생성 (public: 설명 페이지 / private: 서비스 코드)
2. 페이지 제작 (디자인 가이드라인 준수)
3. Pages workflow 추가 + Pages 활성화
4. 허브 페이지에 별 추가
5. push 및 배포 확인

---

## 계정 정보

- GitHub: songclaude-bot
- 계정 생성: 2026-02-23 00:45:03 KST
- 허브: https://songclaude-bot.github.io
