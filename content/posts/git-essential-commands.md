---
title: "Git 자주 쓰는 명령어 총정리 — 초보부터 실무까지"
date: 2026-03-17T14:00:00+09:00
draft: false
description: "Git 기본 명령어부터 브랜치, 되돌리기, stash까지 실무에서 자주 사용하는 Git 명령어를 정리합니다."
tags: ["git", "github", "버전관리", "명령어"]
categories: ["개발도구"]
author: "latte3cup"
showToc: true
TocOpen: true
---

## 기본 설정

Git을 처음 설치하면 사용자 정보를 설정해야 합니다.

```bash
# 사용자 이름/이메일 설정 (필수)
git config --global user.name "홍길동"
git config --global user.email "hong@example.com"

# 기본 브랜치명을 main으로 설정
git config --global init.defaultBranch main

# 설정 확인
git config --list
```

## 저장소 시작하기

```bash
# 새 저장소 초기화
git init

# 기존 저장소 복제
git clone https://github.com/user/repo.git

# 특정 브랜치만 복제
git clone -b develop https://github.com/user/repo.git
```

## 기본 워크플로우

Git의 기본 흐름은 **수정 → 스테이징 → 커밋**입니다.

```bash
# 현재 상태 확인
git status

# 변경사항 확인
git diff                  # 스테이징 전 변경사항
git diff --staged         # 스테이징된 변경사항

# 스테이징 (커밋 준비)
git add file.txt          # 특정 파일
git add .                 # 모든 변경사항

# 커밋
git commit -m "로그인 기능 추가"

# 로그 확인
git log --oneline         # 한 줄로 요약
git log --graph           # 브랜치 그래프 포함
```

## 브랜치 관리

브랜치는 Git의 핵심 기능입니다. 기능 개발, 버그 수정을 독립적으로 진행할 수 있습니다.

```bash
# 브랜치 목록
git branch                # 로컬 브랜치
git branch -a             # 원격 포함 전체

# 브랜치 생성 & 이동
git branch feature/login  # 생성만
git checkout feature/login # 이동만
git checkout -b feature/login # 생성 + 이동 (단축)
git switch -c feature/login   # 최신 방식

# 브랜치 병합
git checkout main
git merge feature/login

# 브랜치 삭제
git branch -d feature/login      # 병합된 브랜치만
git branch -D feature/login      # 강제 삭제
```

## 원격 저장소 (Remote)

```bash
# 원격 저장소 확인
git remote -v

# 원격 저장소 추가
git remote add origin https://github.com/user/repo.git

# 푸시 (업로드)
git push origin main
git push -u origin main   # 추적 브랜치 설정 (이후 git push만 하면 됨)

# 풀 (다운로드 + 병합)
git pull origin main

# 페치 (다운로드만, 병합은 안 함)
git fetch origin
```

## 되돌리기

실수했을 때 되돌리는 방법들입니다. 상황에 맞는 명령어를 선택하세요.

### 스테이징 취소

```bash
# 특정 파일 스테이징 취소
git restore --staged file.txt

# 전체 스테이징 취소
git restore --staged .
```

### 파일 변경 취소

```bash
# 수정한 파일을 마지막 커밋 상태로 복원
git restore file.txt

# 주의: 변경사항이 완전히 사라짐!
```

### 커밋 되돌리기

```bash
# 마지막 커밋 취소 (변경사항은 유지)
git reset --soft HEAD~1

# 마지막 커밋 취소 (스테이징도 취소)
git reset --mixed HEAD~1

# 마지막 커밋 완전 삭제 (변경사항도 삭제 — 주의!)
git reset --hard HEAD~1

# 안전하게 커밋 되돌리기 (새 커밋으로 취소 기록)
git revert HEAD
```

### reset vs revert 비교

| | `git reset` | `git revert` |
|---|---|---|
| 동작 | 커밋 히스토리 삭제 | 취소 커밋을 새로 생성 |
| 협업 시 | 위험 (이미 push한 경우) | 안전 |
| 용도 | 로컬에서 실수 수정 | push 후 되돌릴 때 |

## Stash — 임시 저장

작업 중 급하게 다른 브랜치로 이동해야 할 때 유용합니다.

```bash
# 현재 변경사항 임시 저장
git stash

# 메시지와 함께 저장
git stash save "로그인 페이지 작업 중"

# 임시 저장 목록
git stash list

# 복원 (목록에서 제거)
git stash pop

# 복원 (목록에서 유지)
git stash apply

# 특정 stash 복원
git stash apply stash@{2}

# stash 삭제
git stash drop stash@{0}
```

## .gitignore

추적하지 않을 파일을 지정합니다.

```gitignore
# IDE 설정
.vscode/
.idea/

# 빌드 결과물
dist/
build/
*.exe

# 환경 파일 (비밀 정보)
.env
*.key

# Python
__pycache__/
*.pyc
.venv/

# Node.js
node_modules/
```

## 실무 팁

### 커밋 메시지 컨벤션

좋은 커밋 메시지는 프로젝트 관리의 핵심입니다.

```
feat: 로그인 기능 추가
fix: 비밀번호 검증 오류 수정
docs: README 설치 가이드 업데이트
refactor: 사용자 인증 로직 리팩토링
chore: 의존성 업데이트
```

### 자주 쓰는 별칭(alias) 설정

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.lg "log --oneline --graph --all"
```

이후 `git st`, `git co main`, `git lg` 등으로 짧게 사용할 수 있습니다.

### 특정 커밋 찾기

```bash
# 메시지로 검색
git log --grep="로그인"

# 코드 변경 내용으로 검색
git log -S "function_name"

# 특정 파일의 변경 이력
git log --follow -- path/to/file.py
```

## 마무리

Git은 처음에는 복잡해 보이지만, 기본 흐름(add → commit → push)만 익히면 금방 적응할 수 있습니다. 나머지 명령어는 필요할 때 하나씩 배워가면 됩니다. 이 글을 북마크해두고 필요할 때 참고하세요.
