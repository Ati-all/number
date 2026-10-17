# Number 게임 배포 및 온라인 설정

## 플레이 주소
GitHub Pages가 활성화되어 있다면 https://ati-all.github.io/number/ 에서 플레이할 수 있습니다.

## 현재 기능
- 닉네임 입력 후 AI 대전
- 방 코드 만들기 / 방 코드로 참가
- 빠른 대전 대기실
- 모바일 화면 지원

**중요:** AI 대전은 Firebase 없이 바로 실행됩니다. 온라인 기능을 실제로 쓰려면 아래 Firebase 설정을 한 번 해야 합니다. Firebase 프로젝트 설정이 저장소에 아직 들어 있지 않으므로, 설정 전에는 온라인 대전이 작동하지 않습니다.

## Firebase 온라인 기능 연결하기

### 1. Firebase 프로젝트 만들기
1. https://console.firebase.google.com/ 에 접속해 Google 계정으로 로그인합니다.
2. **프로젝트 추가**를 눌러 프로젝트를 만듭니다.
3. 프로젝트 개요에서 웹 앱 아이콘 **웹 앱 추가**를 선택합니다. 앱 이름은 Number로 해도 됩니다.
4. 표시되는 Firebase 웹 앱 설정값(apiKey, authDomain, databaseURL, projectId, appId)을 복사해 둡니다.

### 2. 익명 로그인 켜기
1. Firebase 콘솔에서 **빌드 → Authentication → 시작하기**로 이동합니다.
2. **Sign-in method(로그인 방법)**에서 **Anonymous(익명)** 로그인을 사용 설정합니다.

### 3. Realtime Database 만들기
1. **빌드 → Realtime Database → 데이터베이스 만들기**를 누릅니다.
2. 데이터베이스 위치를 선택합니다.
3. 데이터베이스 URL을 복사합니다. databaseURL에는 콘솔에서 표시한 실제 URL을 사용해야 합니다.

### 4. 데이터베이스 규칙 설정
처음 테스트용으로는 Realtime Database의 **규칙** 탭에 아래 규칙을 적용할 수 있습니다.

```json
{
  "rules": {
    "rooms": {
      "$room": {
        ".read": "auth != null",
        ".write": "auth != null"
      }
    },
    "matchmaking": {
      ".read": "auth != null",
      ".write": "auth != null"
    }
  }
}
```

이 규칙은 **프로토타입 테스트용**입니다. 익명 로그인 사용자라면 다른 방 데이터도 수정할 수 있으므로, 공개 서비스로 운영하기 전에는 방 참가자만 해당 방을 읽고 쓸 수 있도록 보안 규칙을 더 엄격하게 만들어야 합니다. 데이터베이스 규칙을 공개 읽기/쓰기로 설정하지 마세요.

### 5. 웹 앱 설정값을 index.html에 입력
1. GitHub 저장소의 index.html 파일을 엽니다.
2. firebaseConfig 부분을 찾습니다.
3. 아래처럼 Firebase 콘솔에서 받은 실제 값으로 바꿉니다.

```js
const firebaseConfig = {
  apiKey: "실제_API_KEY",
  authDomain: "실제_PROJECT_ID.firebaseapp.com",
  databaseURL: "실제_DATABASE_URL",
  projectId: "실제_PROJECT_ID",
  appId: "실제_APP_ID"
};
```

4. 변경 사항을 커밋합니다. GitHub Pages가 배포를 마치면 사이트를 새로고침하고 온라인 대전을 테스트합니다.

## 테스트 순서
1. AI 대전이 되는지 확인합니다.
2. 서로 다른 브라우저나 기기 두 대에서 사이트를 엽니다.
3. 첫 번째 기기에서 닉네임을 입력하고 **방 코드 만들기**를 누릅니다.
4. 표시된 6자리 코드를 두 번째 기기에서 입력하고 **참가**를 누릅니다.
5. 빠른 대전은 두 기기에서 거의 동시에 **상대 찾기**를 눌러 테스트합니다.

## 참고
- GitHub Pages는 정적 웹사이트 호스팅이므로 온라인 대전의 실시간 상태 저장은 Firebase Realtime Database가 담당합니다.
- Firebase 프로젝트 생성과 설정은 저장소 소유자의 계정에서 직접 완료해야 합니다.
