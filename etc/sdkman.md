# SDKMAN 설정 가이드 (Mac + IntelliJ + Gradle)

## SDKMAN 설치
```
1. 설치 스크립트 실행curl -s "https://get.sdkman.io" | bash
2. 터미널에 설정 적용 (현재 창에서 바로 쓰기 위함)source "$HOME/.sdkman/bin/sdkman-init.sh"# 
3. 설치 확인sdk version
```

### JDK 목록 확인
```
sdk list java

# q를 누르면 목록에서 나갑니다.
# Status 열에 installed라고 적힌 것이 현재 설치된 버전입니다.
```

### JDK 설치
```
sdk install java 17.0.10-tem
```

### 현재 상태 확인
```
# 지금 어떤 버전을 쓰고 있는지 확인sdk current java
# java 명령어로 직접 확인java -version
```


## 1️⃣ Java는 SDKMAN으로만 관리하기 (중요)

Mac에서는 보통 Java가 여러 경로로 설치됩니다.

예:

- Homebrew
- Oracle installer
- IntelliJ bundled JDK
- SDKMAN

이게 섞이면 **PATH 충돌**이 발생합니다.

그래서 가장 깔끔한 방식은

```
Java = SDKMAN만 사용
```

설치 가능한 Java 확인

```
sdk list java
```

예

```
21.0.2-tem
17.0.10-tem
```

Java 설치

```
sdk install java 21.0.2-tem
sdk install java 17.0.10-tem
```

---

# 2️⃣ 기본 Java 설정

기본 Java 버전 설정

```
sdk default java 21.0.2-tem
```

설정 확인

```
java --version
```

예

```
openjdk version "21.0.2"
Temurin
```

---

# 3️⃣ IntelliJ에서 SDKMAN JDK 사용

IntelliJ에서 JDK를 SDKMAN 경로로 설정합니다.

경로

```
Settings
 → Build, Execution, Deployment
   → Build Tools
     → Gradle
```

그리고

```
Gradle JVM
```

에서 선택

```
Add JDK
```

경로 지정

```
~/.sdkman/candidates/java
```

예

```
~/.sdkman/candidates/java/21.0.2-tem
```

---

# 4️⃣ 프로젝트별 Java 버전 관리 (.sdkmanrc)

SDKMAN의 가장 강력한 기능입니다.

프로젝트 루트에 파일 생성

```
.sdkmanrc
```

내용

```
java=17.0.10-tem
```

또는

```
java=21.0.2-tem
```

사용 방법

프로젝트 폴더에서

```
sdk env
```

그러면 자동으로

```
Java 버전 전환
```

예

```
Using java version 17.0.10-tem in this shell
```

---

# 5️⃣ 자동 전환 설정 (추천)

폴더 이동 시 자동으로 Java 버전이 변경되도록 설정합니다.

설정 확인

```
sdk config
```

그리고

```
sdkman_auto_env=true
```

로 설정합니다.

이제

```
cd projectA
```

하면

```
Java 17
```

```
cd projectB
```

하면

```
Java 21
```

자동 전환됩니다.

---

# 6️⃣ Gradle에서도 동일하게 맞추기

Gradle과 IntelliJ Java 버전이 달라지는 경우가 있습니다.

그래서 Gradle 설정도 확인해야 합니다.

경로

```
Settings
 → Build Tools
   → Gradle
```

추천 설정

```
Build and run using: Gradle
Run tests using: Gradle
Gradle JVM: Project SDK
```

---

# 7️⃣ 최종 구조 (추천 세팅)

```
Java 설치
   ↓
SDKMAN

Java 버전 관리
   ↓
.sdkmanrc

IDE 사용
   ↓
IntelliJ → SDKMAN JDK 사용

빌드
   ↓
Gradle Wrapper
```

---

# 8️⃣ 실제 개발 환경 예시

예를 들어

```
tmk-project
```

프로젝트 구조

```
tmk-project
 ├─ .sdkmanrc
 ├─ build.gradle
 ├─ settings.gradle
 └─ src
```

`.sdkmanrc`

```
java=21.0.2-tem
```

이제

```
cd tmk-project
```

하면 자동으로

```
Java 21
```

로 전환됩니다.
