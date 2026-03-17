---
title: "VS Code 필수 확장 프로그램 추천 — 생산성 2배로 올리기"
date: 2026-03-17T16:00:00+09:00
draft: false
description: "VS Code에서 꼭 설치해야 할 확장 프로그램을 카테고리별로 정리합니다. 코딩 생산성을 크게 높여줄 확장들입니다."
tags: ["vscode", "확장프로그램", "개발도구", "생산성"]
categories: ["개발도구"]
author: "latte3cup"
showToc: true
TocOpen: true
---

## 필수 기본 확장

### 1. GitHub Copilot

AI 코드 자동완성 도구입니다. 코드를 작성하면 다음 줄을 예측해서 제안합니다.

- **용도**: 코드 자동완성, 함수 생성, 테스트 코드 작성
- **가격**: 월 $10 (학생 무료)
- **설치**: `GitHub.copilot`

```
// 함수 이름만 쓰면 Copilot이 내용을 제안
function calculateTax(price, rate) {
  // Tab으로 수락
}
```

### 2. GitLens

Git 히스토리를 에디터 안에서 바로 확인할 수 있습니다.

- **용도**: 코드 라인별 마지막 수정자/시간 표시, 파일 히스토리, blame
- **설치**: `eamodio.gitlens`

각 줄 옆에 누가 언제 수정했는지 바로 보여줘서, 코드 리뷰나 디버깅에 매우 유용합니다.

### 3. Error Lens

에러와 경고를 해당 줄 옆에 바로 표시합니다. Problems 패널을 열지 않아도 됩니다.

- **용도**: 인라인 에러/경고 표시
- **설치**: `usernamehw.errorlens`

## 언어별 확장

### Python

- **Python** (`ms-python.python`): 공식 Python 지원. IntelliSense, 디버깅, 린팅
- **Pylance** (`ms-python.vscode-pylance`): 빠른 타입 체크, 자동완성 강화
- **Black Formatter** (`ms-python.black-formatter`): 코드 자동 포맷팅

### JavaScript / TypeScript

- **ESLint** (`dbaeumer.vscode-eslint`): JS/TS 린트 규칙 적용
- **Prettier** (`esbenp.prettier-vscode`): 코드 포맷터
- **Auto Rename Tag** (`formulahendry.auto-rename-tag`): HTML 태그 이름 동시 수정

### C# / Unity

- **C#** (`ms-dotnettools.csharp`): C# IntelliSense, 디버깅
- **C# Dev Kit** (`ms-dotnettools.csdevkit`): 솔루션 탐색기, 테스트 실행

## 생산성 확장

### 4. Thunder Client

VS Code 안에서 REST API를 테스트할 수 있습니다. Postman의 경량 대안입니다.

- **용도**: API 요청/응답 테스트
- **설치**: `rangav.vscode-thunder-client`

### 5. Todo Tree

코드 내 `TODO`, `FIXME`, `HACK` 등의 주석을 트리 뷰로 모아 보여줍니다.

- **용도**: 코드 내 할 일 관리
- **설치**: `Gruntfuggly.todo-tree`

### 6. Path Intellisense

파일 경로를 입력할 때 자동완성을 제공합니다.

- **용도**: import문, 파일 경로 자동완성
- **설치**: `christian-kohler.path-intellisense`

### 7. Project Manager

여러 프로젝트를 즐겨찾기처럼 관리하고 빠르게 전환할 수 있습니다.

- **용도**: 프로젝트 간 빠른 전환
- **설치**: `alefragnani.project-manager`

## 외관 / UI 확장

### 8. Material Icon Theme

파일/폴더 아이콘을 파일 타입별로 구분되는 깔끔한 아이콘으로 변경합니다.

- **설치**: `PKief.material-icon-theme`

### 9. One Dark Pro

VS Code에서 가장 인기 있는 다크 테마입니다. Atom의 One Dark를 포팅한 것입니다.

- **설치**: `zhuangtongfa.Material-theme`

### 10. Indent Rainbow

들여쓰기 레벨별로 다른 색상을 표시해서 코드 구조를 한눈에 파악할 수 있습니다.

- **설치**: `oderwat.indent-rainbow`

## 원격 개발 확장

### 11. Remote - SSH

SSH로 원격 서버에 접속해서 VS Code에서 직접 편집할 수 있습니다.

- **용도**: 원격 서버 개발
- **설치**: `ms-vscode-remote.remote-ssh`

### 12. Dev Containers

Docker 컨테이너 안에서 개발 환경을 구성합니다. 팀 전체가 동일한 환경을 사용할 수 있습니다.

- **용도**: 컨테이너 기반 개발
- **설치**: `ms-vscode-remote.remote-containers`

## 추천 설정

확장을 설치한 후 `settings.json`에 추가하면 좋은 설정들입니다.

```json
{
  // 저장 시 자동 포맷
  "editor.formatOnSave": true,

  // 기본 포맷터
  "[python]": {
    "editor.defaultFormatter": "ms-python.black-formatter"
  },
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },

  // 미니맵 비활성화 (화면 절약)
  "editor.minimap.enabled": false,

  // 괄호 쌍 색상
  "editor.bracketPairColorization.enabled": true,

  // 파일 자동 저장
  "files.autoSave": "afterDelay",
  "files.autoSaveDelay": 1000,

  // 터미널 폰트 (코딩 폰트 권장)
  "terminal.integrated.fontFamily": "JetBrains Mono"
}
```

## 유용한 단축키

확장 없이도 VS Code 자체 단축키로 생산성을 높일 수 있습니다.

| 단축키 | 기능 |
|--------|------|
| `Ctrl+P` | 파일 빠른 열기 |
| `Ctrl+Shift+P` | 명령 팔레트 |
| `Ctrl+D` | 같은 단어 다중 선택 |
| `Alt+↑/↓` | 줄 이동 |
| `Ctrl+Shift+K` | 줄 삭제 |
| `Ctrl+/` | 주석 토글 |
| `Ctrl+B` | 사이드바 토글 |
| `` Ctrl+` `` | 터미널 토글 |
| `Ctrl+Shift+F` | 전체 검색 |
| `F2` | 변수명 일괄 변경 |

## 마무리

VS Code의 강점은 확장 생태계입니다. 하지만 너무 많이 설치하면 오히려 느려질 수 있으니, 실제로 사용하는 확장만 활성화하는 것을 권장합니다. `@installed` 검색으로 설치된 확장을 주기적으로 점검하세요.
