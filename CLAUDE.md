# songclaude-bot — Claude 가이드라인

이 계정은 사람과 AI(Claude)가 함께 만들어가는 프로젝트 공간입니다.

---

## 시작하기

`gh auth status`를 실행하여 현재 상태를 확인하고, 아래에서 해당하는 시나리오를 따르세요.

| 상태 | 시나리오 | 이동 |
|---|---|---|
| `gh` 명령 없음 또는 인증 안 됨 | 새 환경 (EC2 초기화 등) | → [A. 초기 셋업](#a-초기-셋업) |
| 인증 OK + 서비스 개발 요청 | 새 서비스 만들기 | → [B. 새 서비스 만들기](#b-새-서비스-만들기) |
| 인증 OK + 소개 페이지 요청 | 퍼블릭 소개 페이지 만들기 | → [C. 소개 페이지 만들기](#c-소개-페이지-만들기) |
| 인증 OK + 기존 작업 | 기존 프로젝트 이어서 작업 | → 해당 repo clone 후 작업 |

어떤 시나리오든 아래 **규칙**과 **브랜치 전략**은 항상 적용됩니다.

---

## 규칙

- **repo 삭제 금지**: `DELETE /repos` API를 절대 호출하지 않는다. 삭제가 필요하면 반드시 사용자에게 먼저 확인.
- **비밀 정보 커밋 금지**: PAT, API 키, .env 파일 등을 절대 공개 repo에 커밋하지 않는다.

## 브랜치 & 커밋 전략

- 작업마다 브랜치를 나눈다 (`feature/xxx`, `fix/xxx` 등)
- 작업 단위로 커밋한다 (한 커밋에 한 작업)
- 이렇게 하면 문제가 생겨도 언제든 복구 가능

---

## A. 초기 셋업

새 환경(EC2 등)에서 처음 시작할 때만 실행합니다. 이미 인증이 되어 있다면 건너뛰세요.

1. **사용자에게 GitHub PAT를 요청**하세요. 절대 토큰을 추측하거나 파일에 기록하지 마세요.
2. git 인증 설정:
   ```bash
   git config --global credential.helper store
   git config --global user.name "songclaude-bot"
   git config --global user.email ""
   ```
3. gh CLI 설치 및 인증
4. 한글 폰트 설치: `sudo apt install -y fonts-noto-cjk`
5. 이 repo(`.github`)를 clone하여 가이드라인 확인
6. 허브 페이지 repo(`songclaude-bot.github.io`) clone하여 현재 프로젝트 목록 파악

셋업 완료 후, 사용자의 요청에 따라 B 또는 C로 이동하세요.

---

## B. 새 서비스 만들기

실제 동작하는 서비스(웹앱, API 등)를 만들 때의 절차입니다.

### 1. private repo 생성
```bash
gh repo create songclaude-bot/{{서비스명}} --private --clone
cd {{서비스명}}
```

### 2. 개발
- 사용자 요구사항에 맞게 개발
- 브랜치 & 커밋 전략 준수
- 서비스 코드에는 우주 테마를 적용할 필요 없음

### 3. 배포
- 서비스 성격에 맞는 플랫폼 선택 (Netlify / Vercel / AWS 등)
- 배포 URL 확보

### 4. 소개 페이지 연결 (선택)
서비스를 공개적으로 소개하려면 → [C. 소개 페이지 만들기](#c-소개-페이지-만들기)로 이동

---

## C. 소개 페이지 만들기

프로젝트를 공개적으로 소개하는 우주 테마 페이지를 만듭니다.

### 1. 템플릿으로 시작
```bash
gh repo clone songclaude-bot/project-page-template /tmp/template
gh repo create songclaude-bot/{{프로젝트명}} --public --clone
cp -r /tmp/template/* {{프로젝트명}}/
cp -r /tmp/template/.github {{프로젝트명}}/
cd {{프로젝트명}}
```

### 2. 커스텀
- `index.html`의 `{{PLACEHOLDER}}` 교체 (상세 가이드는 템플릿 내 CLAUDE.md 참조)
- 챕터 구성, 이미지 추가
- 디자인 가이드라인 준수 (아래 참조)

### 3. Pages 배포
```bash
# Pages 활성화
gh api repos/songclaude-bot/{{프로젝트명}}/pages -X POST \
  -f "build_type=workflow" -f "source[branch]=main" -f "source[path]=/"

# push (workflow가 자동 배포)
git add -A && git commit -m "Initial page" && git push -u origin main
```

### 4. 허브에 별 추가
`songclaude-bot.github.io/index.html`에 추가:
```html
<a class="project-star fade-in delay-6"
   href="https://songclaude-bot.github.io/{{프로젝트명}}/"
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

### 5. 확인
- [ ] 소개 페이지 배포 확인
- [ ] 허브 페이지에 별 표시 확인
- [ ] 서비스 링크 연결 확인 (있다면)

---

## 프로젝트 구조

```
songclaude-bot.github.io (허브, public)
  └─ 별 → 각 프로젝트 소개 페이지로 연결

프로젝트 소개 페이지 (public repo, GitHub Pages)
  └─ 소개, 스크린샷, 기술 스택, 스토리
  └─ 실제 서비스 링크 (있다면)

실제 서비스 코드 (private repo)
  └─ Netlify / Vercel / AWS 등 별도 호스팅
```

현재 프로젝트 목록은 **허브 페이지의 index.html**에서 `project-star` 클래스를 검색하면 확인할 수 있습니다.

---

## 디자인 가이드라인

퍼블릭 소개 페이지에만 적용됩니다. private 서비스 코드에는 해당 없음.
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

## 계정 정보

- GitHub: songclaude-bot
- 계정 생성: 2026-02-23 00:45:03 KST
- 허브: https://songclaude-bot.github.io
