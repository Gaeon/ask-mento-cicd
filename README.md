# 🖥️ Vue 3 Frontend for 물어멘토

이 저장소는 물어멘토 서비스의 **프론트엔드(Vue 3 + Vite 기반)** 코드만 분리하여 관리하는 레포지토리입니다.  
CI/CD 자동화를 위해 Jenkins 빌드 대상으로 구성되었습니다.

## 🛠️ 기술 스택

- **Vue 3** (`<script setup>` SFC 기반)
- **Vite**
- **Vue Router**
- **Axios** (API 연동)
- **Docker**
- **Kubernetes YAML (deploy/service/cm 포함)**

---

## 📂 프로젝트 구조

```
frontend/
├─ Dockerfile              # 프론트엔드 컨테이너 빌드 설정
├─ default.conf            # Nginx 설정 (optional)
├─ k8s/                    # Kubernetes 배포 YAML
├─ public/                 # 정적 파일
├─ src/                    # Vue 3 소스 코드
│  ├─ components/          # 공통 컴포넌트
│  ├─ layouts/             # 레이아웃 구성
│  ├─ pages/               # 각 라우트별 페이지
│  ├─ router/              # Vue Router 설정
│  ├─ stores/              # Pinia 상태관리
│  └─ main.js              # 진입점
└─ vite.config.js          # Vite 설정
```

![물어멘토_logo](https://github.com/user-attachments/assets/45e009c8-5266-4af1-a473-b464716b2822)

