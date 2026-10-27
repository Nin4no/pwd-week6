# 🍜 PWD Week 6 과제 수행 체크리스트 & 가이드

> **과제 목표**: TypeScript + Express 백엔드 API를 구현하여 **Render**에 배포하고, 5주차 React 클라이언트(`pwd-week5`)를 연동하여 **Vercel**에 배포합니다.

---

## 📋 최종 제출 항목

제출 전 아래 4개 URL을 준비해야 합니다:

- [ ] **Backend GitHub URL**: `https://github.com/<username>/pwd-week6`
- [ ] **Client GitHub URL**: `https://github.com/<username>/pwd-week5`
- [ ] **Render Service URL**: `https://pwd-week6-<username>.onrender.com`
- [ ] **Vercel Client URL**: `https://pwd-week5-<username>.vercel.app`

---

## 🛠️ Phase 1. 백엔드 (`pwd-week6`) 구현 및 Render 배포

> **작업 디렉토리**: `c:\pwd\pwd-week6`

### 1-1. 프로젝트 초기화 및 환경 설정
- [ ] **1. 패키지 초기화 및 의존성 설치**
  ```powershell
  cd c:\pwd\pwd-week6
  npm init -y
  npm install express cors
  npm install -D typescript @types/node @types/express @types/cors tsx
  npm install -D jest ts-jest @types/jest supertest @types/supertest
  ```
- [ ] **2. `package.json` 스크립트 및 엔진 설정**
  - `"type": "commonjs"`
  - `"main": "dist/server.js"`
  - `"engines": { "node": ">=22" }`
  - 스크립트 등록:
    - `"dev": "tsx watch server.ts"`
    - `"typecheck": "tsc --noEmit"`
    - `"build": "tsc -p tsconfig.build.json && tsx scripts/copy-data.ts"`
    - `"start": "node dist/server.js"`
    - `"test": "jest --runInBand"`
    - `"test:watch": "jest --watch --runInBand"`
- [ ] **3. 설정 파일 작성**
  - `tsconfig.json`: 전체 프로젝트 타입 검사용 설정
  - `tsconfig.build.json`: 배포용 컴파일 설정 (`tests`, `scripts` 제외)
  - `jest.config.cjs`: Jest 테스트 환경 설정
  - `.gitignore`: `node_modules`, `dist`, `.env` 등 제외 설정
  - `scripts/copy-data.ts`: 빌드 시 `restaurants.json`을 `dist/` 폴더로 복사하는 스크립트

---

### 1-2. 백엔드 핵심 코드 구현 (MVCS 구조)
교수님 레포(`pwd-week6-clone/pwd-week6`)의 소스코드를 참고하여 구현합니다:

- [ ] **1. 타입 정의 (`src/types/`)**
  - `restaurant.types.ts`: `RestaurantData`, `RestaurantInput`, `CreateRestaurantInput`, `CreateRestaurantBody`
  - `api.types.ts`: `DataResponse<T>`, `ErrorResponse`
- [ ] **2. 유틸리티 및 모델 (`src/utils/`, `src/models/`)**
  - `src/utils/httpError.ts`: 커스텀 `HttpError` 클래스
  - `src/utils/asyncHandler.ts`: 비동기 라우트 핸들러 에러 래퍼
  - `src/models/restaurant.model.ts`: `Restaurant` 클래스 (데이터 정규화 및 불변성)
- [ ] **3. 데이터 시드 (`src/data/`)**
  - `src/data/restaurants.json`: 맛집 초기 데이터 (최소 3개 이상)
- [ ] **4. 서비스 계층 (`src/services/`)**
  - `src/services/restaurants.service.ts`:
    - 파일 비동기/동기 로드 (`loadRestaurantsAsync`, `loadRestaurantsSync`)
    - 지연 시뮬레이션 (`simulateLatency`)
    - 맛집 조회 (`getAllRestaurants`, `getAllRestaurantsSync`, `getRestaurantById`, `getPopularRestaurants`)
    - 맛집 생성 및 유효성 검증 (`createRestaurant`)
    - 데모 데이터 리셋 (`resetStore`)
- [ ] **5. 컨트롤러 계층 (`src/controllers/`)**
  - `src/controllers/restaurants.controller.ts`:
    - `getRestaurants`, `getRestaurantsSync`, `getRestaurant`, `getPopularRestaurants`
    - `createRestaurant`: `recommendedMenu` 정규화 로직 포함
    - `resetDemoData`
- [ ] **6. 라우트 및 미들웨어 (`src/routes/`, `src/middleware/`)**
  - `src/routes/restaurants.routes.ts`: 엔드포인트 라우팅 등록
  - `src/middleware/notFound.middleware.ts`: 404 핸들러
  - `src/middleware/error.middleware.ts`: 500 전역 에러 핸들러
- [ ] **7. 앱 구성 및 진입점**
  - `src/app.ts`: Express 인스턴스 구성, CORS, Body-parser, `/health`, 라우터 연결
  - `server.ts`: 포트 바인딩 (`PORT = Number(process.env.PORT) || 3000`) 및 리슨
- [ ] **8. 테스트 코드 작성 (`tests/`)**
  - `tests/restaurants.routes.test.ts`: Supertest 기반 엔드포인트 통합 테스트
  - `tests/restaurants.service.test.ts`: 서비스 단위 테스트

---

### 1-3. 로컬 테스트 및 빌드 검증
- [ ] **타입 검사 통과**: `npm run typecheck`
- [ ] **테스트 통과**: `npm test`
- [ ] **프로덕션 빌드 성공**: `npm run build` (`dist/` 생성 및 JSON 복사 확인)
- [ ] **프로덕션 모드 로컬 실행 확인**:
  ```powershell
  npm start
  # 별도 터미널에서 확인:
  curl.exe http://localhost:3000/health
  curl.exe http://localhost:3000/api/restaurants
  ```

---

### 1-4. GitHub 연동 및 Render 배포
- [ ] **1. GitHub에 `pwd-week6` 원격 레포지토리 생성 및 푸시**
  ```powershell
  git init
  git add .
  git commit -m "feat: Initial commit for pwd-week6 TypeScript Express API"
  git branch -M main
  git remote add origin https://github.com/<username>/pwd-week6.git
  git push -u origin main
  ```
- [ ] **2. Render Web Service 생성 및 설정**
  - [Render Dashboard](https://dashboard.render.com/) 접속 → **+ New → Web Service**
  - 본인의 `pwd-week6` GitHub 레포 연결
  - 설정값:
    - **Build Command**: `npm ci --include=dev && npm run build`
    - **Start Command**: `npm start`
    - **Instance Type**: `Free`
    - **Environment Variables**: `NODE_VERSION=22` (또는 24)
    - **Advanced → Health Check Path**: `/health`
- [ ] **3. 배포 상태 확인**
  - 상태가 **Live**가 되는지 확인
  - 공개 HTTPS URL로 헬스체크 및 API 호출 확인:
    ```powershell
    curl.exe -i "https://<service-name>.onrender.com/health"
    curl.exe -i "https://<service-name>.onrender.com/api/restaurants"
    ```

---

## 🎨 Phase 2. 프론트엔드 (`pwd-week5`) 연동 및 Vercel 배포

> **작업 디렉토리**: `c:\pwd\pwd-week5`

### 2-1. 5주차 클라이언트 최신 코드 보완
- [ ] **1. 누락 페이지 파일 동기화**
  - `pwd-week5-clone`의 `src/pages/AdminPage.tsx`, `src/pages/SubmissionsPage.tsx`를 `pwd-week5/src/pages/`로 복사
- [ ] **2. 라우터 설정 확인 (`src/App.tsx`)**
  - `BrowserRouter` 대신 **`HashRouter`** 사용 (`/#/list`, `/#/restaurant/1` 등)
  - `/admin`, `/submissions` 라우트 등록 확인

### 2-2. API 모듈 교체 및 백엔드 연동
- [ ] **1. 기존 API 모듈 백업**
  ```powershell
  cd c:\pwd\pwd-week5
  # src/services/api.ts를 api.local.ts로 이름 변경
  Move-Item src/services/api.ts src/services/api.local.ts
  ```
- [ ] **2. 새 `src/services/api.ts` 작성**
  - `axios` 기반 HTTP 클라이언트 생성 (`timeout: 60000`, `baseURL: import.meta.env.VITE_API_BASE_URL`)
  - `submissionAPI`는 `./api.local`에서 re-export
  - `restaurantAPI` 구현:
    - `getRestaurants`, `getRestaurantById`, `getPopularRestaurants`, `createRestaurant`
    - `updateRestaurant`, `deleteRestaurant`는 "미지원 API" 에러를 던지도록 처리
- [ ] **3. `src/pages/AdminPage.tsx` 수정/삭제 버튼 비활성화**
  - 서버가 수정/삭제를 지원하지 않으므로 버튼에 `disabled` 및 `title` 속성 추가:
    ```tsx
    <Button disabled title="이번 주 서버는 수정 API를 제공하지 않습니다." onClick={() => onEdit(r)}>수정</Button>
    <Danger disabled title="이번 주 서버는 삭제 API를 제공하지 않습니다." onClick={() => onDelete(r)}>삭제</Danger>
    ```

### 2-3. 로컬 연동 및 빌드 테스트
- [ ] **1. `.env.local` 생성**
  ```dotenv
  VITE_API_BASE_URL=http://localhost:3000
  ```
- [ ] **2. 로컬 실행 확인**
  - 로컬 백엔드(`pwd-week6`, 포트 3000) 실행 상태에서 `npm run dev` 실행
  - 브라우저에서 맛집 목록 조회 및 관리자 화면 등록 동작 확인
- [ ] **3. 배포 전 빌드 검증**
  ```powershell
  npm run typecheck
  npm run lint
  npm run build
  ```

### 2-4. GitHub Push 및 Vercel 재배포
- [ ] **1. 클라이언트 GitHub 푸시**
  ```powershell
  git add .
  git commit -m "feat: Connect to Week 6 Express API with HashRouter"
  git push origin main
  ```
- [ ] **2. Vercel 환경변수 등록**
  - Vercel 대시보드 → `pwd-week5` 프로젝트 → **Settings → Environment Variables**
  - **Key**: `VITE_API_BASE_URL`
  - **Value**: 실제 Render 공개 URL (예: `https://pwd-week6-<username>.onrender.com`)
  - **Environment**: `Production`, `Preview` 모두 체크 후 저장
- [ ] **3. Vercel Redeploy**
  - **Deployments** 탭 → 최신 배포 오른쪽 **... → Redeploy** 클릭하여 환경변수 적용 새 빌드 생성

---

## 🔍 Phase 3. 통합 검증 및 체크리스트

- [ ] **Render 백엔드 헬스체크**: `https://<service-name>.onrender.com/health` 응답이 `{"status":"ok"}`인가요?
- [ ] **Vercel 클라이언트 정상 접속**: `https://<project-name>.vercel.app`에서 메인 및 목록 화면이 잘 열리나요?
- [ ] **실제 Render 요청 확인**:
  - 브라우저 개발자 도구(F12) **Network** 탭에서 맛집 목록 요청이 `https://<service-name>.onrender.com/api/restaurants`로 전송되고 `200`을 반환하나요?
- [ ] **관리자 맛집 등록 검증**:
  - `/#/admin`에서 새 식당을 등록했을 때 `POST /api/restaurants` 요청이 `201`로 성공하고 목록에 즉시 추가되나요?
  - 수정/삭제 버튼이 비활성화(disabled)되어 있나요?
- [ ] **제보 기능 유지 검증**:
  - `/#/submit`에서 제보 작성 시 브라우저 LocalStorage에 저장되고, `/#/submissions`에서 승인 시 Render 등록 요청이 가나요?
- [ ] **URL 4종 최종 제출 준비 완료**
