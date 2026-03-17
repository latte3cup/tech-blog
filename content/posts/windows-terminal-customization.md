---
title: "Windows Terminal 커스터마이징 — 예쁘고 효율적인 터미널 만들기"
date: 2026-03-17T21:00:00+09:00
draft: false
description: "Windows Terminal을 커스터마이징하는 방법을 정리합니다. 테마, 폰트, Oh My Posh, 단축키 설정까지 완벽 가이드."
tags: ["windows-terminal", "터미널", "oh-my-posh", "커스터마이징"]
categories: ["개발도구"]
author: "latte3cup"
showToc: true
TocOpen: true
---

## Windows Terminal이란?

Windows Terminal은 Microsoft가 만든 최신 터미널 앱입니다. CMD, PowerShell, WSL, Git Bash 등을 탭으로 관리할 수 있고, GPU 가속 렌더링으로 빠릅니다.

### 설치

```bash
# winget으로 설치
winget install Microsoft.WindowsTerminal

# 또는 Microsoft Store에서 "Windows Terminal" 검색
```

## 기본 설정

`Ctrl+,`로 설정을 열거나, 좌측 하단 톱니바퀴 아이콘을 클릭합니다.

### settings.json 직접 편집

설정 페이지 좌측 하단 "JSON 파일 열기"로 직접 편집할 수 있습니다.

```json
{
    "defaultProfile": "{GUID}",
    "copyOnSelect": true,
    "theme": "dark",
    "defaults": {
        "font": {
            "face": "JetBrains Mono",
            "size": 12
        },
        "opacity": 90,
        "useAcrylic": true,
        "padding": "8"
    }
}
```

## 코딩 폰트 설치

코딩용 폰트는 **Nerd Font** 버전을 설치해야 Oh My Posh 아이콘이 제대로 표시됩니다.

### 추천 폰트

| 폰트 | 특징 |
|------|------|
| **JetBrains Mono** | JetBrains 제작, 코딩 최적화 |
| **Fira Code** | 리거처(합자) 지원 |
| **Cascadia Code** | Microsoft 제작, Windows 기본 |
| **D2Coding** | 한글 지원이 뛰어남 |

### 설치 방법

```bash
# Oh My Posh로 Nerd Font 설치
winget install JanDeDobbeleer.OhMyPosh

# 폰트 설치 (관리자 권한 필요)
oh-my-posh font install JetBrainsMono
oh-my-posh font install FiraCode
```

settings.json에서 폰트 적용:

```json
"defaults": {
    "font": {
        "face": "JetBrainsMono Nerd Font"
    }
}
```

## Oh My Posh — 프롬프트 꾸미기

Oh My Posh는 터미널 프롬프트를 예쁘게 꾸며주는 도구입니다. Git 브랜치, Python 버전, 실행 시간 등을 표시합니다.

### 설치

```bash
winget install JanDeDobbeleer.OhMyPosh
```

### PowerShell 설정

PowerShell 프로필을 편집합니다.

```bash
# 프로필 파일 열기
notepad $PROFILE

# 파일이 없으면 생성
New-Item -Path $PROFILE -Type File -Force
```

프로필에 추가:

```powershell
# Oh My Posh 활성화
oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\catppuccin_mocha.omp.json" | Invoke-Expression

# 자동완성 (PSReadLine)
Set-PSReadLineOption -PredictiveViewSource History
Set-PSReadLineOption -EditMode Windows
Set-PSReadLineKeyHandler -Key Tab -Function MenuComplete
```

### 인기 테마

```bash
# 테마 목록 보기
Get-PoshThemes

# 또는 브라우저에서 미리보기
# https://ohmyposh.dev/docs/themes
```

추천 테마:
- **catppuccin_mocha**: 부드러운 색감, 인기 1위
- **agnoster**: 클래식, 심플
- **powerlevel10k_rainbow**: 정보 풍부
- **tokyo**: 도쿄 나이트 색상

## 배색(Color Scheme) 변경

### 내장 배색

settings.json의 프로필에서:

```json
"profiles": {
    "defaults": {
        "colorScheme": "One Half Dark"
    }
}
```

내장 배색: `Campbell`, `One Half Dark`, `One Half Light`, `Solarized Dark`, `Tango Dark` 등

### 커스텀 배색 추가

```json
"schemes": [
    {
        "name": "Catppuccin Mocha",
        "background": "#1E1E2E",
        "foreground": "#CDD6F4",
        "cursorColor": "#F5E0DC",
        "selectionBackground": "#585B70",
        "black": "#45475A",
        "red": "#F38BA8",
        "green": "#A6E3A1",
        "yellow": "#F9E2AF",
        "blue": "#89B4FA",
        "purple": "#F5C2E7",
        "cyan": "#94E2D5",
        "white": "#BAC2DE",
        "brightBlack": "#585B70",
        "brightRed": "#F38BA8",
        "brightGreen": "#A6E3A1",
        "brightYellow": "#F9E2AF",
        "brightBlue": "#89B4FA",
        "brightPurple": "#F5C2E7",
        "brightCyan": "#94E2D5",
        "brightWhite": "#A6ADC8"
    }
]
```

## 유용한 단축키

| 단축키 | 기능 |
|--------|------|
| `Ctrl+Shift+T` | 새 탭 |
| `Ctrl+Shift+W` | 탭 닫기 |
| `Ctrl+Tab` | 다음 탭 |
| `Alt+Shift+D` | 창 분할 (자동) |
| `Alt+Shift+Plus` | 수직 분할 |
| `Alt+Shift+Minus` | 수평 분할 |
| `Ctrl+Shift+P` | 명령 팔레트 |
| `Ctrl+Shift+F` | 검색 |
| `Ctrl+=` / `Ctrl+-` | 폰트 크기 조절 |

## 프로필별 설정

Git Bash, WSL 등 프로필마다 다른 설정을 적용할 수 있습니다.

```json
"profiles": {
    "list": [
        {
            "name": "PowerShell",
            "commandline": "pwsh.exe",
            "icon": "ms-appx:///ProfileIcons/{574e775e-4f2a-5b96-ac1e-a2962a402336}.png",
            "colorScheme": "Catppuccin Mocha",
            "opacity": 85
        },
        {
            "name": "Git Bash",
            "commandline": "C:\\Program Files\\Git\\bin\\bash.exe",
            "icon": "C:\\Program Files\\Git\\mingw64\\share\\git\\git-for-windows.ico",
            "startingDirectory": "~",
            "colorScheme": "One Half Dark"
        },
        {
            "name": "Ubuntu (WSL)",
            "source": "Windows.Terminal.Wsl",
            "colorScheme": "Tango Dark"
        }
    ]
}
```

## 배경 이미지 / 아크릴 효과

```json
"defaults": {
    // 반투명 아크릴 효과
    "useAcrylic": true,
    "opacity": 85,

    // 또는 배경 이미지
    "backgroundImage": "C:/Users/me/wallpaper.jpg",
    "backgroundImageOpacity": 0.3,
    "backgroundImageStretchMode": "uniformToFill"
}
```

## 추천 PowerShell 모듈

```powershell
# 아이콘 표시 (ls 결과에 파일 아이콘)
Install-Module -Name Terminal-Icons -Scope CurrentUser
Import-Module Terminal-Icons

# z - 자주 가는 디렉토리로 빠르게 이동
Install-Module -Name z -Scope CurrentUser

# PSFzf - 퍼지 검색
Install-Module -Name PSFzf -Scope CurrentUser
Set-PsFzfOption -PSReadlineChordProvider 'Ctrl+f'
```

프로필(`$PROFILE`)에 `Import-Module` 줄을 추가하면 터미널 시작 시 자동 로드됩니다.

## 마무리

Windows Terminal + Oh My Posh + Nerd Font 조합은 Windows에서 최고의 터미널 경험을 제공합니다. 한 번 설정하면 개발 효율이 크게 올라갑니다. 설정 파일을 Git으로 관리하면 다른 PC에서도 동일한 환경을 바로 구성할 수 있습니다.
