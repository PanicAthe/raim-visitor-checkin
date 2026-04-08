# 방문자 등록 시스템

AI 얼굴 인식 기반 방문자 등록 웹앱을 개인적으로 설계·구현한 프로젝트입니다. 얼굴 인식으로 방문자의 성별과 연령대를 보조 판별하고, Firebase를 통해 데이터를 수집·관리하는 흐름을 구현했습니다.

> 이 저장소는 개인 포트폴리오용으로 정리한 버전입니다. 실제 운영 환경의 계정 정보, 비공개 설정값, 기관 내부 데이터는 포함하지 않습니다.

## 주요 기능

### 🎯 핵심 기능
- **AI 얼굴 인식**: face-api.js를 활용한 실시간 성별/연령대 자동 감지
  - 최종 확인 모달: "확인/취소" 버튼만 제공
- **수동 입력 모드**: 기본 모드, AI 모드와 전환 가능
- **다국어 지원**: 한국어/영어 실시간 전환 (i18next, 메인 화면만)
- **실시간 통계**: 일일 방문자 현황 대시보드 (공간별 필터링)
- **데이터 백업**: Firebase + Google Sheets 자동 백업 시스템 (시간 간격 설정 가능)

### 🔐 보안 기능
- 관리자 잠금 화면 (PIN 인증)
  - Firebase 관리 공간 목록에서 선택
  - 공간 추가/삭제는 관리자 대시보드에서 가능
- 로고 3번 탭으로 대시보드 진입

### 📊 관리자 대시보드
- 로고 3번 탭으로 진입 
- localStorage 기반 일일 통계 (공간별 필터링)
- 오늘의 방문자 집계 (성별/연령대별)
- 연령대 분포 막대 차트
- 성별 분포 도넛 차트
- 공간 추가/삭제 가능
- 나이 보정값 조절 (-10 ~ +10)
- 수동 백업 및 삭제

## 기술 스택

- **Frontend**: React 19.2.0 + Vite 7.2.4, face-api.js 0.22.2
- **Backend**: Firebase 12.8.0 (Firestore), Google Apps Script (자동 백업)
- **UI/UX**: Lucide React 0.562.0, i18next 25.8.0 (한/영 지원)
- **배포**: Vercel (자동 배포)

> 상세 기술 스택 및 의존성은 [docs/REQUIREMENTS.md](docs/REQUIREMENTS.md)를 참조하세요.

## 시작하기

### 사전 요구사항
```bash
Node.js >= 16.x
npm >= 8.x
```

### 설치 및 실행

1. **저장소 클론**
```bash
git clone https://github.com/your-org/visitor-checkin-app.git
cd visitor-checkin-app
```

2. **의존성 설치**
```bash
npm install
```

3. **환경 변수 설정**

프로젝트 루트에 `.env` 파일을 생성하고 Firebase 설정값을 입력합니다:

```env
VITE_FIREBASE_API_KEY=your_api_key
VITE_FIREBASE_AUTH_DOMAIN=your_auth_domain
VITE_FIREBASE_PROJECT_ID=your_project_id
VITE_FIREBASE_STORAGE_BUCKET=your_storage_bucket
VITE_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
VITE_FIREBASE_APP_ID=your_app_id
VITE_FIREBASE_MEASUREMENT_ID=your_measurement_id
VITE_ADMIN_PIN=0000
VITE_GOOGLE_APPS_SCRIPT_URL=https://script.google.com/macros/s/XXXX/exec
```

**백업 기능 사용 시 추가 설정:**
- `VITE_GOOGLE_APPS_SCRIPT_URL`: Google Apps Script 웹 앱 배포 URL
- 수동 백업 버튼이 필요 없으면 설정하지 않아도 됩니다

> **Firebase 설정**: [Firebase Console](https://console.firebase.google.com/)에서 프로젝트 생성 후 웹 앱 추가, Firestore Database 생성. 상세 가이드는 [docs/SETUP_GUIDE.md](docs/SETUP_GUIDE.md) 참조.

4. **개발 서버 실행**
```bash
npm run dev
```
브라우저에서 `http://localhost:5173` 접속

5. **프로덕션 빌드**
```bash
npm run build
npm run preview
```

## 프로젝트 구조

```
visitor-checkin-app/
├── src/
│   ├── components/          # React 컴포넌트
│   │   ├── AdminLockScreen.jsx         # 관리자 PIN 인증 화면
│   │   ├── CameraCard.jsx              # AI 얼굴 인식 카메라
│   │   ├── Dashboard.jsx               # 관리자 대시보드
│   │   ├── LanguageToggle.jsx          # 언어 전환 버튼
│   │   ├── ManualEntryCard.jsx         # 수동 입력 폼
│   │   ├── VisitorList.jsx             # 방문자 목록 및 편집
│   │   ├── modals/                     # 모달 컴포넌트
│   │   │   ├── ErrorModal.jsx          # 오류 알림 모달
│   │   │   ├── FinalConfirmModal.jsx   # 최종 확인 모달
│   │   │   └── SuccessModal.jsx        # 완료 알림 모달
│   │   └── dashboard/                  # 대시보드 관련 컴포넌트
│   │       ├── AgeGroupChart.jsx       # 연령대 분포 차트
│   │       ├── GenderChart.jsx         # 성별 분포 차트
│   │       ├── BackupSection.jsx       # 백업 섹션
│   │       └── RoomManagementModal.jsx # 관람실 관리 모달
│   ├── hooks/               # 커스텀 훅
│   │   └── useIsMobile.js         # 모바일 감지
│   ├── i18n/                # 다국어 설정
│   │   ├── translations/
│   │   │   ├── en.json            # 영어 번역
│   │   │   └── ko.json            # 한국어 번역
│   │   └── i18n.js                # i18next 설정
│   ├── utils/               # 유틸리티 함수
│   │   ├── ageConverter.js        # 연령대 변환 로직
│   │   └── idGenerator.js         # 고유 ID 생성 함수
│   ├── App.jsx              # 메인 앱 컴포넌트
│   ├── constants.js         # 상수 정의 (연령대, 매핑)
│   ├── firebase.js          # Firebase 설정
│   └── main.jsx             # 앱 진입점
├── public/
│   └── models/              # face-api.js AI 모델 (50MB+)
├── docs/                    # 프로젝트 문서
│   ├── ARCHITECTURE.md
│   ├── FEATURES.md
│   ├── MAINTENANCE_GUIDE.md
│   ├── REQUIREMENTS.md
│   └── script.txt           # Google Apps Script 백업 코드
└── package.json
```

## 사용 방법

1. **초기 설정**: 관리자 잠금 화면에서 Firebase 관리 공간 목록에서 선택 및 PIN 입력
2. **체크인 모드 선택** (기본값: 수동 모드):
   - **수동 모드**: 성별/연령대 직접 선택 후 "추가" 버튼
   - **AI 모드**: "모드 전환" 버튼으로 전환, 카메라 앞에 서고 "AI 스캔" 클릭
3. **방문자 리스트**:
   - 동일 성별/연령대는 자동 그룹화
   - "+/-" 버튼으로 수량 조절, "X" 버튼으로 그룹 삭제, "초기화"로 전체 비우기
4. **최종 확인**: "완료" 버튼 클릭 → 최종 확인 모달에서 "확인" → Firestore 전송
5. **관리자 대시보드** (로고 3번 탭):
   - 일일 통계 조회 (공간별 필터링)
   - "공간 관리" 버튼으로 공간 추가/삭제
   - 나이 보정값 조절
## 상세 문서

- [📝 요구사항 명세서](docs/REQUIREMENTS.md)
- [📋 기능 명세서](docs/FEATURES.md)
- [🏗️ 시스템 아키텍처](docs/ARCHITECTURE.md)
- [🔧 유지보수 가이드](docs/MAINTENANCE_GUIDE.md)
