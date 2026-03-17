---
title: "GitHub Pages 무료 호스팅 — 정적 사이트를 0원으로 배포하는 방법"
date: 2026-03-17T20:00:00+09:00
draft: false
description: "GitHub Pages로 정적 웹사이트를 무료로 호스팅하는 방법을 설명합니다. 설정, 커스텀 도메인, HTTPS, 제한사항까지 정리합니다."
tags: ["github-pages", "호스팅", "배포", "github"]
categories: ["블로그"]
author: "latte3cup"
showToc: true
TocOpen: true
---

## GitHub Pages란?

GitHub Pages는 GitHub 레포지토리에서 직접 정적 웹사이트를 호스팅하는 무료 서비스입니다. HTML, CSS, JavaScript로 된 사이트라면 무엇이든 배포할 수 있습니다.

### 무료로 제공되는 것

- **HTTPS 자동 적용** (SSL 인증서 무료)
- **글로벌 CDN** (Fastly)
- **커스텀 도메인** 연결 가능
- **무제한 트래픽** (합리적인 범위 내)
- **자동 배포** (GitHub Actions 연동)

### 제한사항

| 항목 | 제한 |
|------|------|
| 저장소 크기 | 1GB 권장 |
| 사이트 크기 | 1GB 이하 |
| 대역폭 | 월 100GB |
| 빌드 횟수 | 시간당 10회 |
| 용도 | 정적 사이트만 (서버 사이드 불가) |

일반 블로그나 포트폴리오 사이트에는 충분한 스펙입니다.

## 사이트 유형

### 1. 사용자/조직 사이트

- 레포 이름: `username.github.io`
- URL: `https://username.github.io`
- 계정당 1개만 가능

### 2. 프로젝트 사이트

- 레포 이름: 아무거나 (예: `my-blog`)
- URL: `https://username.github.io/my-blog/`
- 레포당 1개, 개수 제한 없음

## 배포 방법

### 방법 1: 직접 HTML 파일 push

가장 간단한 방법입니다.

```bash
# 레포 생성
mkdir my-site && cd my-site
git init

# HTML 파일 생성
echo '<h1>Hello, GitHub Pages!</h1>' > index.html

# push
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/username/my-site.git
git branch -M main
git push -u origin main
```

Settings → Pages → Source: **Deploy from a branch** → `main` → Save

### 방법 2: GitHub Actions (권장)

빌드 도구(Hugo, Jekyll, Astro 등)를 사용할 때 권장합니다.

```yaml
# .github/workflows/deploy.yml
name: Deploy to Pages

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: './public'  # 빌드 결과 폴더

      - name: Deploy
        id: deployment
        uses: actions/deploy-pages@v4
```

Settings → Pages → Source: **GitHub Actions** 선택

## 커스텀 도메인 설정

`username.github.io` 대신 자신의 도메인을 사용할 수 있습니다.

### 1단계: 도메인 구매

Namecheap, Cloudflare, 가비아 등에서 도메인을 구매합니다.

### 2단계: DNS 설정

도메인 관리 페이지에서 DNS 레코드를 추가합니다.

**A 레코드** (apex 도메인: `example.com`):

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

**CNAME 레코드** (서브도메인: `www.example.com`):

```
www → username.github.io
```

### 3단계: GitHub 설정

Settings → Pages → Custom domain에 도메인 입력 → Save

`CNAME` 파일이 자동으로 레포에 추가됩니다.

### 4단계: HTTPS 활성화

DNS 전파 완료 후 (최대 24시간), "Enforce HTTPS" 체크박스를 활성화합니다.

## GitHub Pages + Hugo

Hugo 사이트를 GitHub Pages에 배포하는 전체 워크플로우입니다.

```bash
# 1. Hugo 사이트 생성
hugo new site my-blog
cd my-blog
git init

# 2. 테마 설치
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod

# 3. 설정 (hugo.toml에 theme = "PaperMod" 추가)

# 4. 글 작성
hugo new posts/my-post.md

# 5. 로컬 확인
hugo server -D

# 6. GitHub Actions 워크플로우 생성 (.github/workflows/hugo.yml)

# 7. push → 자동 배포
git add .
git commit -m "Initial blog"
git push
```

## 유용한 팁

### 404 페이지 커스터마이징

루트에 `404.html`을 만들면 커스텀 404 페이지가 표시됩니다.

### 리다이렉트

JavaScript로 간단한 리다이렉트를 구현할 수 있습니다.

```html
<!-- old-page.html -->
<meta http-equiv="refresh" content="0; url=/new-page/">
```

### 캐싱 주의

GitHub Pages는 약 10분 캐시됩니다. 변경이 바로 안 보이면 브라우저 캐시를 삭제하거나 시크릿 모드로 확인하세요.

### 빌드 상태 확인

Actions 탭에서 빌드 상태를 확인할 수 있습니다. 빌드 실패 시 로그에서 원인을 확인하세요.

## 대안 비교

| 서비스 | 가격 | 특징 |
|--------|------|------|
| **GitHub Pages** | 무료 | Git 기반, 정적 전용 |
| Netlify | 무료 티어 | 폼, 함수, 프리뷰 배포 |
| Vercel | 무료 티어 | Next.js 최적화, 서버리스 |
| Cloudflare Pages | 무료 티어 | 빠른 CDN, Workers 통합 |

블로그 용도라면 GitHub Pages로 충분합니다. 서버 사이드 기능이 필요하면 Vercel이나 Netlify를 고려하세요.

## 마무리

GitHub Pages는 정적 사이트를 무료로 호스팅하는 가장 간단한 방법입니다. Git push만 하면 자동 배포되고, HTTPS와 CDN이 기본 제공됩니다. 블로그, 포트폴리오, 문서 사이트를 만들 때 첫 번째 선택지로 추천합니다.
