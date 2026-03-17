---
title: "Hugo로 기술 블로그 만들기 — 설치부터 배포까지 완전 가이드"
date: 2026-03-17T19:00:00+09:00
draft: false
description: "Hugo 정적 사이트 생성기로 기술 블로그를 만드는 방법을 단계별로 설명합니다. 설치, 테마, 설정, GitHub Pages 배포까지."
tags: ["hugo", "블로그", "정적사이트", "github-pages"]
categories: ["블로그"]
author: "latte3cup"
showToc: true
TocOpen: true
---

## Hugo란?

Hugo는 **Go 언어**로 만든 정적 사이트 생성기(Static Site Generator)입니다. 마크다운 파일을 HTML로 변환해서 빠른 웹사이트를 만들어줍니다.

### 왜 Hugo인가?

| | Hugo | Jekyll | Next.js |
|---|---|---|---|
| 언어 | Go | Ruby | JavaScript |
| 빌드 속도 | 1초 미만 (1000페이지) | 수십 초 | 수 분 |
| 설치 | 바이너리 1개 | Ruby + gem | Node.js + npm |
| 학습 곡선 | 낮음 | 낮음 | 높음 |
| 용도 | 블로그/문서 | 블로그 | 웹 앱 전반 |

Hugo의 가장 큰 장점은 **속도**와 **간편함**입니다. npm이나 Ruby가 필요 없고, 바이너리 하나로 동작합니다.

## 설치

### Windows

```bash
winget install Hugo.Hugo.Extended
```

### macOS

```bash
brew install hugo
```

### Linux

```bash
# Snap
sudo snap install hugo --channel=extended

# 또는 직접 다운로드
# https://github.com/gohugoio/hugo/releases
```

**Extended 버전**을 설치해야 합니다. 대부분의 테마가 SCSS를 사용하기 때문입니다.

```bash
# 설치 확인
hugo version
# → hugo v0.158.0+extended ...
```

## 사이트 생성

```bash
# 새 사이트 생성
hugo new site my-blog

# 디렉토리 이동
cd my-blog
```

생성되는 구조:

```
my-blog/
├── archetypes/    # 새 글 템플릿
├── content/       # 마크다운 글
├── layouts/       # HTML 템플릿 오버라이드
├── static/        # 정적 파일 (이미지, CSS 등)
├── themes/        # 테마
└── hugo.toml      # 사이트 설정
```

## 테마 설치

PaperMod는 기술 블로그에 가장 인기 있는 Hugo 테마입니다.

```bash
# Git 초기화
git init

# PaperMod 테마를 서브모듈로 추가
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

다른 인기 테마들:
- **Stack**: 카드 레이아웃, 사이드바
- **Blowfish**: 모던 디자인, Tailwind CSS
- **Congo**: 미니멀, 다크모드

테마 목록: [themes.gohugo.io](https://themes.gohugo.io/)

## 기본 설정 (hugo.toml)

```toml
baseURL = "https://username.github.io/blog/"
title = "My Tech Blog"
languageCode = "ko"
theme = "PaperMod"

# 페이지당 글 수
paginate = 10

# SEO
enableRobotsTXT = true

[params]
  env = "production"
  description = "기술 블로그입니다"
  author = "작성자명"
  defaultTheme = "auto"     # 다크/라이트 자동
  ShowReadingTime = true     # 읽는 시간 표시
  ShowCodeCopyButtons = true # 코드 복사 버튼
  showtoc = true             # 목차 표시

# 메뉴
[menu]
  [[menu.main]]
    name = "Posts"
    url = "/posts/"
    weight = 10
  [[menu.main]]
    name = "Tags"
    url = "/tags/"
    weight = 20

# 코드 하이라이팅
[markup.highlight]
  codeFences = true
  style = "monokai"
```

## 글 작성

### 새 글 생성

```bash
hugo new posts/my-first-post.md
```

### 마크다운 작성

```markdown
---
title: "글 제목"
date: 2026-03-17
draft: false
description: "글 설명 (SEO에 중요)"
tags: ["태그1", "태그2"]
categories: ["카테고리"]
---

## 본문 시작

일반 마크다운으로 작성합니다.

### 코드 블록

​```python
print("Hello, Hugo!")
​```

### 이미지

![이미지 설명](/images/photo.jpg)
```

### Front Matter 주요 항목

| 항목 | 설명 |
|------|------|
| `title` | 글 제목 |
| `date` | 작성일 |
| `draft` | true면 빌드에서 제외 |
| `description` | SEO 메타 설명 |
| `tags` | 태그 목록 |
| `categories` | 카테고리 |
| `showToc` | 목차 표시 여부 |

## 로컬 미리보기

```bash
# 개발 서버 실행 (draft 포함)
hugo server -D

# → http://localhost:1313 에서 확인
# 파일 변경 시 자동 새로고침
```

## GitHub Pages 배포

### 1. GitHub Actions 워크플로우 생성

`.github/workflows/hugo.yml`:

```yaml
name: Deploy Hugo site to GitHub Pages

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Install Hugo
        run: |
          wget -O ${{ runner.temp }}/hugo.deb \
            https://github.com/gohugoio/hugo/releases/download/v0.158.0/hugo_extended_0.158.0_linux-amd64.deb
          sudo dpkg -i ${{ runner.temp }}/hugo.deb

      - uses: actions/checkout@v4
        with:
          submodules: recursive

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5

      - name: Build
        run: hugo --gc --minify --baseURL "${{ steps.pages.outputs.base_url }}/"

      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy
        id: deployment
        uses: actions/deploy-pages@v4
```

### 2. GitHub 설정

1. 레포지토리 생성 → push
2. Settings → Pages → Source: **GitHub Actions** 선택
3. push하면 자동 빌드/배포

### 3. 글 발행 워크플로우

```bash
# 새 글 작성
hugo new posts/new-article.md
# → 에디터에서 작성

# draft: false 확인 후
git add content/posts/new-article.md
git commit -m "새 글: 제목"
git push
# → 자동 배포!
```

## SEO 최적화 팁

1. **description 필수**: 모든 글에 `description` 작성
2. **구조화 데이터**: JSON-LD로 BlogPosting 스키마 추가
3. **sitemap**: Hugo가 자동 생성 (`/sitemap.xml`)
4. **robots.txt**: `enableRobotsTXT = true` 설정
5. **이미지 alt 텍스트**: 모든 이미지에 설명 추가
6. **내부 링크**: 관련 글끼리 링크 연결

## 마무리

Hugo + GitHub Pages 조합은 **비용 0원**으로 빠른 기술 블로그를 운영할 수 있는 최고의 방법입니다. 마크다운으로 글을 쓰고 push만 하면 자동으로 배포됩니다. 이 블로그도 바로 이 방식으로 만들었습니다.
