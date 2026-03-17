---
title: "Docker 입문 가이드 — 개념부터 실전 사용법까지"
date: 2026-03-17T15:00:00+09:00
draft: false
description: "Docker의 핵심 개념과 설치, 컨테이너 실행, Dockerfile 작성, Docker Compose까지 입문자를 위한 완벽 가이드입니다."
tags: ["docker", "컨테이너", "devops", "dockerfile"]
categories: ["DevOps"]
author: "latte3cup"
showToc: true
TocOpen: true
---

## Docker란?

Docker는 애플리케이션을 **컨테이너**라는 격리된 환경에서 실행하는 도구입니다. "내 컴퓨터에서는 되는데?"라는 문제를 해결합니다.

### 가상머신(VM) vs Docker

| | 가상머신 | Docker 컨테이너 |
|---|---|---|
| 부팅 시간 | 수 분 | 수 초 |
| 용량 | 수 GB | 수십 MB |
| OS | 전체 OS 포함 | 호스트 OS 커널 공유 |
| 성능 | 오버헤드 있음 | 네이티브에 가까움 |

## 설치

### Windows

Docker Desktop을 설치합니다.

```bash
# winget으로 설치
winget install Docker.DockerDesktop

# 설치 확인
docker --version
docker run hello-world
```

### macOS

```bash
brew install --cask docker
```

### Linux (Ubuntu)

```bash
# 공식 설치 스크립트
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 현재 사용자를 docker 그룹에 추가 (sudo 없이 실행)
sudo usermod -aG docker $USER
```

## 핵심 개념

- **이미지(Image)**: 컨테이너의 설계도. 읽기 전용 템플릿
- **컨테이너(Container)**: 이미지를 실행한 인스턴스. 격리된 프로세스
- **Dockerfile**: 이미지를 만드는 스크립트
- **레지스트리(Registry)**: 이미지 저장소 (Docker Hub 등)

```
Dockerfile → (빌드) → 이미지 → (실행) → 컨테이너
```

## 기본 명령어

### 컨테이너 실행

```bash
# 컨테이너 실행
docker run nginx

# 백그라운드 실행 (-d) + 포트 매핑 (-p)
docker run -d -p 8080:80 nginx
# → http://localhost:8080 에서 nginx 접속 가능

# 이름 지정
docker run -d --name my-nginx -p 8080:80 nginx

# 환경 변수 전달
docker run -d -e MYSQL_ROOT_PASSWORD=secret mysql:8.0

# 볼륨 마운트 (호스트 파일 연결)
docker run -d -v $(pwd)/data:/var/lib/mysql mysql:8.0
```

### 컨테이너 관리

```bash
# 실행 중인 컨테이너 목록
docker ps

# 모든 컨테이너 (중지된 것 포함)
docker ps -a

# 컨테이너 중지 / 시작 / 재시작
docker stop my-nginx
docker start my-nginx
docker restart my-nginx

# 컨테이너 삭제
docker rm my-nginx

# 실행 중인 컨테이너에 접속
docker exec -it my-nginx bash

# 로그 확인
docker logs my-nginx
docker logs -f my-nginx   # 실시간
```

### 이미지 관리

```bash
# 이미지 목록
docker images

# 이미지 다운로드
docker pull python:3.12

# 이미지 삭제
docker rmi nginx

# 사용하지 않는 리소스 정리
docker system prune
```

## Dockerfile 작성

Dockerfile은 이미지를 만드는 레시피입니다.

### Python 앱 예제

```dockerfile
# 베이스 이미지
FROM python:3.12-slim

# 작업 디렉토리 설정
WORKDIR /app

# 의존성 먼저 복사 (캐시 활용)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 소스 코드 복사
COPY . .

# 포트 노출
EXPOSE 8000

# 실행 명령
CMD ["python", "app.py"]
```

### 빌드 및 실행

```bash
# 이미지 빌드
docker build -t my-python-app .

# 실행
docker run -d -p 8000:8000 my-python-app
```

### Node.js 앱 예제

```dockerfile
FROM node:20-slim

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```

### Dockerfile 최적화 팁

1. **레이어 캐시 활용**: 변경이 적은 것(의존성)을 먼저 복사
2. **slim/alpine 이미지 사용**: 용량 절약
3. **.dockerignore 작성**: 불필요한 파일 제외

```
# .dockerignore
.git
__pycache__
*.pyc
.env
node_modules
```

## Docker Compose

여러 컨테이너를 한 번에 관리합니다. 웹 앱 + DB 같은 구성에 필수입니다.

### docker-compose.yml 예제

```yaml
services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

### Compose 명령어

```bash
# 전체 시작 (백그라운드)
docker compose up -d

# 로그 확인
docker compose logs -f

# 전체 중지
docker compose down

# 볼륨까지 삭제
docker compose down -v

# 특정 서비스만 재빌드
docker compose build web
docker compose up -d web
```

## 실전 활용 예시

### 개발 환경 통일

팀원 모두 같은 환경에서 개발할 수 있습니다.

```yaml
# docker-compose.dev.yml
services:
  app:
    build: .
    volumes:
      - .:/app          # 소스 코드 실시간 반영
    ports:
      - "3000:3000"
    command: npm run dev  # 개발 모드
```

### 로컬에서 DB 테스트

```bash
# PostgreSQL 바로 실행
docker run -d \
  --name test-db \
  -e POSTGRES_PASSWORD=test \
  -p 5432:5432 \
  postgres:15

# 접속
docker exec -it test-db psql -U postgres
```

## 자주 하는 실수

1. **컨테이너 내부에서 직접 파일 수정** → 컨테이너 삭제 시 사라짐. 볼륨을 사용하세요
2. **latest 태그 의존** → 버전을 명시하세요 (`python:3.12`, not `python:latest`)
3. **불필요한 레이어** → RUN 명령을 `&&`로 연결하여 레이어 최소화
4. **루트로 실행** → 보안을 위해 USER 지시문으로 일반 사용자 지정

```dockerfile
RUN adduser --disabled-password appuser
USER appuser
```

## 마무리

Docker의 핵심은 **"환경을 코드로 관리한다"**는 것입니다. Dockerfile과 docker-compose.yml만 있으면 누구나 동일한 환경을 재현할 수 있습니다. 개인 프로젝트부터 적용해보면 금방 익숙해집니다.
