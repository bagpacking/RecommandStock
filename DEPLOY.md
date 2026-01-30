# 시놀로지 NAS Docker 배포 가이드

## 사전 요구사항
- Synology NAS에 Docker 패키지 설치
- SSH 또는 파일 스테이션 접근 권한

## 배포 방법

### 방법 1: Docker Compose 사용 (권장)

1. **프로젝트를 NAS로 복사**
   - 파일 스테이션을 통해 프로젝트 폴더를 NAS의 `docker` 폴더에 복사
   - 예: `/volume1/docker/RecommandStock/`

2. **SSH로 NAS 접속**
   ```bash
   ssh admin@your-nas-ip
   ```

3. **프로젝트 디렉토리로 이동**
   ```bash
   cd /volume1/docker/RecommandStock
   ```

4. **Docker Compose로 빌드 및 실행**
   ```bash
   sudo docker-compose up -d --build
   ```

5. **접속**
   - 브라우저에서 `http://your-nas-ip:3000` 접속

### 방법 2: Docker GUI 사용

1. **Docker 이미지 빌드 (로컬에서)**
   ```bash
   docker build -t recommandstock:latest .
   ```

2. **이미지 저장**
   ```bash
   docker save recommandstock:latest > recommandstock.tar
   ```

3. **NAS에 업로드**
   - 파일 스테이션으로 `.tar` 파일을 NAS에 업로드

4. **Synology Docker 패키지에서 이미지 불러오기**
   - Docker 앱 열기 → 이미지 → 추가 → 파일에서 추가
   - 업로드한 `.tar` 파일 선택

5. **컨테이너 실행**
   - 이미지 선택 → 시작
   - 포트 설정: 로컬 포트 3000 → 컨테이너 포트 80
   - 자동 재시작 활성화

### 방법 3: Docker Hub 사용

1. **Docker Hub에 이미지 푸시 (로컬에서)**
   ```bash
   docker build -t your-dockerhub-username/recommandstock:latest .
   docker push your-dockerhub-username/recommandstock:latest
   ```

2. **NAS에서 이미지 다운로드**
   ```bash
   sudo docker pull your-dockerhub-username/recommandstock:latest
   ```

3. **컨테이너 실행**
   ```bash
   sudo docker run -d \
     --name recommandstock \
     -p 3000:80 \
     --restart unless-stopped \
     your-dockerhub-username/recommandstock:latest
   ```

## 유용한 명령어

### 로그 확인
```bash
sudo docker logs recommandstock
sudo docker logs -f recommandstock  # 실시간 로그
```

### 컨테이너 상태 확인
```bash
sudo docker ps
```

### 컨테이너 중지/시작
```bash
sudo docker stop recommandstock
sudo docker start recommandstock
sudo docker restart recommandstock
```

### 컨테이너 제거
```bash
sudo docker stop recommandstock
sudo docker rm recommandstock
```

### 이미지 재빌드
```bash
sudo docker-compose down
sudo docker-compose up -d --build
```

## 포트 변경
`docker-compose.yml` 파일에서 포트를 변경할 수 있습니다:
```yaml
ports:
  - "8080:80"  # 8080 포트로 변경
```

## 환경 변수 설정
`.env` 파일을 생성하여 환경 변수를 설정할 수 있습니다:
```env
VITE_API_URL=http://your-api-server:port
```

`docker-compose.yml`에서 참조:
```yaml
env_file:
  - .env
```

## 트러블슈팅

### 포트 충돌
- 다른 포트로 변경하거나 기존 서비스 중지

### 빌드 실패
- Node.js 버전 확인
- 의존성 문제: `npm ci` 대신 `npm install` 시도

### 접속 불가
- 방화벽 설정 확인
- NAS 외부 접속 설정 확인
