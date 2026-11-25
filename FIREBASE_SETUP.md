# 도둑차 대탈주 공개 버전 설정

목표: `https://leewonyoungethan.github.io/chase/` 링크 하나로 **누구나 로그인 없이** 플레이하고 순위표에 기록을 올리게 하기.

## 1. GitHub Pages 켜기 (2분)
1. https://github.com/leewonyoungethan/chase/settings/pages 를 엽니다.
2. **Build and deployment → Source**: `Deploy from a branch`
3. **Branch**: `main` / `/ (root)` 를 고르고 **Save**.
4. 1~2분 뒤 `https://leewonyoungethan.github.io/chase/` 에서 게임이 열립니다. (이 단계까지는 순위표 없이 플레이만 됩니다.)

## 2. Firebase 프로젝트 만들기 (무료, 5분)
1. https://console.firebase.google.com 에서 **프로젝트 추가** → 이름 아무거나(예: `thief-chase`) → Google 애널리틱스는 꺼도 됩니다.
2. **빌드 → Authentication → 시작하기 → 로그인 방법 → 익명(Anonymous)** 을 **사용 설정**.
3. 같은 Authentication 화면의 **설정 → 승인된 도메인**에 `leewonyoungethan.github.io` 를 추가.
4. **빌드 → Firestore Database → 데이터베이스 만들기** → 위치는 `asia-northeast3 (서울)` → **프로덕션 모드**.
5. Firestore의 **규칙(Rules)** 탭에 이 저장소의 `firestore.rules` 내용을 통째로 붙여 넣고 **게시**.
6. **프로젝트 개요 옆 톱니바퀴 → 프로젝트 설정 → 내 앱 → 웹(</>)** 으로 앱을 등록하면 `firebaseConfig = { apiKey: ..., authDomain: ..., projectId: ..., ... }` 가 나옵니다.

## 3. 설정값 넣기
`firebaseConfig` 내용을 Claude에게 붙여 주면 `index.html` 의 `FIREBASE_CONFIG` 에 넣어 드립니다. 직접 하려면:

```js
const FIREBASE_CONFIG = { apiKey: "...", authDomain: "...", projectId: "...", storageBucket: "...", messagingSenderId: "...", appId: "..." };
```

> 웹 앱의 apiKey는 비밀번호가 아니라 공개되는 값입니다. 데이터 보호는 `firestore.rules` 가 맡습니다.

## 순위표 규칙이 막는 것
- 다른 사람 기록 덮어쓰기·삭제 (익명 로그인 계정마다 기록 1개)
- 내 최고점보다 낮은 점수로 덮어쓰기
- 말이 안 되는 점수 (도주 시간 대비 너무 높은 점수, 500만 점 초과 등)
- 순위표 외 다른 데이터 쓰기

브라우저 개발자 도구로 그럴듯한 가짜 점수를 넣는 것까지 완전히 막을 수는 없습니다. 이상한 기록은 Firebase 콘솔의 Firestore 화면에서 직접 지우면 됩니다.
