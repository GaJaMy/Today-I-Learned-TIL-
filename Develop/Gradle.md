# 실무 Spring 프로젝트 build.gradle 완전 해부

## 1. 기본 설정 영역

### plugins { }
- **역할**: 프로젝트에 기능을 추가하는 플러그인 적용
- **메서드**: `id(String pluginId)`, `version(String version)`, `apply(boolean)`

```groovy
plugins {
    id 'java'                                              // Java 컴파일, 테스트 기능
    id 'org.springframework.boot' version '3.2.0'          // Spring Boot 플러그인
    id 'io.spring.dependency-management' version '1.1.4'   // 의존성 버전 관리
    id 'jacoco'                                            // 코드 커버리지
    id 'checkstyle'                                        // 코드 스타일 검사
}
```

### group
- **타입**: String
- **역할**: 프로젝트의 그룹 ID (Maven의 groupId와 동일)
- **사용처**: JAR 파일 생성, Maven 저장소 배포 시 경로 결정

```groovy
group = 'com.example.myapp'
// Maven 저장소 경로: com/example/myapp/myapp/1.0.0/myapp-1.0.0.jar
```

### version
- **타입**: String
- **역할**: 프로젝트 버전
- **관례**: `MAJOR.MINOR.PATCH[-SNAPSHOT]`

```groovy
version = '1.0.0-SNAPSHOT'  // 개발 중
version = '1.0.0'           // 릴리스
version = '1.0.1'           // 버그 픽스
version = '1.1.0'           // 기능 추가
```

### description
- **타입**: String
- **역할**: 프로젝트 설명

```groovy
description = 'E-commerce Backend API Server'
```

---

## 2. Java 설정

### java { }
- **역할**: Java 컴파일 옵션 설정
- **주요 프로퍼티**: `sourceCompatibility`, `targetCompatibility`, `toolchain`

```groovy
java {
    sourceCompatibility = JavaVersion.VERSION_17  // 소스 코드 Java 버전
    targetCompatibility = JavaVersion.VERSION_17  // 컴파일된 바이트코드 버전
    
    // 또는 문자열로
    sourceCompatibility = '17'
    targetCompatibility = '17'
}

// Java Toolchain 사용 (권장)
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
        vendor = JvmVendorSpec.ADOPTIUM  // Eclipse Temurin
    }
}
```

### configurations { }
- **역할**: 의존성 구성(Configuration) 커스터마이징
- **사용 케이스**: Lombok, QueryDSL 등 어노테이션 프로세서 설정

```groovy
configurations {
    compileOnly {
        extendsFrom annotationProcessor  // compileOnly가 annotationProcessor 포함
    }
    
    // 특정 의존성 전체 제외
    all {
        exclude group: 'org.springframework.boot', module: 'spring-boot-starter-logging'
    }
}
```

---

## 3. 저장소 설정

### repositories { }
- **역할**: 의존성을 다운로드할 저장소 지정
- **메서드**: `mavenCentral()`, `google()`, `maven { }`

```groovy
repositories {
    mavenCentral()  // Maven Central Repository (가장 많이 사용)
    
    google()  // Google Maven Repository (Android 프로젝트)
    
    // Spring Milestone/Snapshot
    maven { 
        url 'https://repo.spring.io/milestone' 
    }
    maven { 
        url 'https://repo.spring.io/snapshot' 
    }
    
    // 사내 Nexus/Artifactory
    maven {
        url 'https://nexus.company.com/repository/maven-releases/'
        credentials {
            username = project.findProperty('nexusUsername') ?: System.getenv('NEXUS_USERNAME')
            password = project.findProperty('nexusPassword') ?: System.getenv('NEXUS_PASSWORD')
        }
        // 또는
        credentials(PasswordCredentials)
    }
    
    // 로컬 Maven 저장소
    mavenLocal()  // ~/.m2/repository (일반적으로 사용 안 함)
}
```

---

## 4. 의존성 관리 (가장 중요!)

### dependencies { }
- **역할**: 프로젝트 의존성 선언
- **주요 메서드**: `implementation()`, `api()`, `compileOnly()`, `runtimeOnly()`, `annotationProcessor()`, `testImplementation()`

```groovy
dependencies {
    // ============ Spring Boot 스타터 ============
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    implementation 'org.springframework.boot:spring-boot-starter-validation'
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.boot:spring-boot-starter-cache'
    
    // ============ 데이터베이스 ============
    runtimeOnly 'com.mysql:mysql-connector-j'           // MySQL
    runtimeOnly 'org.postgresql:postgresql'             // PostgreSQL
    runtimeOnly 'com.h2database:h2'                     // H2 (테스트/개발용)
    
    // 커넥션 풀
    implementation 'com.zaxxer:HikariCP'                // Spring Boot 기본 포함
    
    // ============ Lombok ============
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    testCompileOnly 'org.projectlombok:lombok'
    testAnnotationProcessor 'org.projectlombok:lombok'
    
    // ============ QueryDSL ============
    implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'
    annotationProcessor 'com.querydsl:querydsl-apt:5.0.0:jakarta'
    annotationProcessor 'jakarta.persistence:jakarta.persistence-api'
    annotationProcessor 'jakarta.annotation:jakarta.annotation-api'
    
    // ============ JWT ============
    implementation 'io.jsonwebtoken:jjwt-api:0.12.3'
    runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.12.3'
    runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.12.3'
    
    // ============ Redis ============
    implementation 'org.springframework.boot:spring-boot-starter-data-redis'
    implementation 'org.redisson:redisson-spring-boot-starter:3.25.0'
    
    // ============ 유틸리티 ============
    implementation 'org.apache.commons:commons-lang3'
    implementation 'com.google.guava:guava:32.1.3-jre'
    implementation 'org.apache.commons:commons-collections4:4.4'
    
    // ============ JSON 처리 ============
    implementation 'com.fasterxml.jackson.datatype:jackson-datatype-jsr310'
    implementation 'com.fasterxml.jackson.datatype:jackson-datatype-hibernate5-jakarta'
    
    // ============ API 문서화 ============
    implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.3.0'
    
    // ============ 이메일 ============
    implementation 'org.springframework.boot:spring-boot-starter-mail'
    
    // ============ 파일 업로드 ============
    implementation 'commons-io:commons-io:2.15.0'
    implementation 'commons-fileupload:commons-fileupload:1.5'
    
    // ============ AWS SDK ============
    implementation platform('software.amazon.awssdk:bom:2.21.0')
    implementation 'software.amazon.awssdk:s3'
    implementation 'software.amazon.awssdk:ses'
    
    // ============ 로깅 ============
    implementation 'net.logstash.logback:logstash-logback-encoder:7.4'
    
    // ============ 모니터링 ============
    implementation 'io.micrometer:micrometer-registry-prometheus'
    
    // ============ Configuration Processor ============
    annotationProcessor 'org.springframework.boot:spring-boot-configuration-processor'
    
    // ============ 테스트 ============
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.security:spring-security-test'
    testImplementation 'io.rest-assured:rest-assured:5.3.2'
    testImplementation 'com.h2database:h2'
    
    // Testcontainers (통합 테스트)
    testImplementation 'org.testcontainers:testcontainers:1.19.3'
    testImplementation 'org.testcontainers:junit-jupiter:1.19.3'
    testImplementation 'org.testcontainers:mysql:1.19.3'
    
    // ============ 개발 도구 ============
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
}
```

### 의존성 스코프 상세 설명

| 스코프 | 컴파일 | 런타임 | 테스트 | 추이적 노출 | 사용 예시 |
|--------|--------|--------|--------|-------------|-----------|
| `implementation` | ✅ | ✅ | ✅ | ❌ | 대부분의 의존성 |
| `api` | ✅ | ✅ | ✅ | ✅ | 공통 라이브러리 모듈 |
| `compileOnly` | ✅ | ❌ | ❌ | ❌ | Lombok, Servlet API |
| `runtimeOnly` | ❌ | ✅ | ✅ | ❌ | JDBC 드라이버, 로깅 구현체 |
| `annotationProcessor` | ✅ | ❌ | ❌ | ❌ | Lombok, QueryDSL, MapStruct |
| `testImplementation` | ❌ | ❌ | ✅ | ❌ | JUnit, Mockito |
| `testRuntimeOnly` | ❌ | ❌ | ✅ | ❌ | H2 (테스트 DB) |
| `developmentOnly` | ❌ | ✅ | ❌ | ❌ | DevTools |

### 의존성 제외 (Exclusion)

```groovy
dependencies {
    implementation('org.springframework.boot:spring-boot-starter-web') {
        exclude group: 'org.springframework.boot', module: 'spring-boot-starter-tomcat'
    }
    implementation 'org.springframework.boot:spring-boot-starter-undertow'
}
```

### 의존성 버전 강제 지정

```groovy
dependencies {
    implementation 'com.fasterxml.jackson.core:jackson-databind:2.15.0'
    
    // 버전 충돌 시 특정 버전 강제
    implementation('com.fasterxml.jackson.core:jackson-core') {
        version {
            strictly '2.15.0'  // 정확히 이 버전만
        }
    }
}
```

---

## 5. 빌드 설정

### bootJar { }
- **역할**: Spring Boot 실행 가능 JAR 파일 설정
- **주요 프로퍼티**: `archiveFileName`, `mainClass`, `layered`

```groovy
bootJar {
    archiveFileName = 'app.jar'  // 생성될 JAR 파일 이름
    archiveVersion = '1.0.0'
    
    mainClass = 'com.example.MyApplication'
    
    // Layered JAR (Docker 이미지 최적화)
    layered {
        enabled = true
    }
    
    // Manifest 설정
    manifest {
        attributes(
            'Implementation-Title': project.name,
            'Implementation-Version': project.version
        )
    }
}

// 일반 JAR 비활성화 (Spring Boot는 bootJar만 사용)
jar {
    enabled = false
}
```

### processResources { }
- **역할**: 리소스 파일 처리 (application.yml 등)
- **사용 케이스**: 빌드 시 변수 치환

```groovy
processResources {
    // application.yml에서 @project.version@ 같은 변수 치환
    filesMatching('**/application.yml') {
        expand(project.properties)
    }
    
    // 또는
    filter { line ->
        line.replace('${version}', project.version.toString())
    }
}
```

### tasks.withType(JavaCompile) { }
- **역할**: Java 컴파일 옵션 설정

```groovy
tasks.withType(JavaCompile) {
    options.encoding = 'UTF-8'
    options.compilerArgs += [
        '-Xlint:unchecked',     // 경고 표시
        '-Xlint:deprecation',   // deprecated 경고
        '-parameters'           // 파라미터 이름 유지 (Spring MVC에서 유용)
    ]
}
```

---

## 6. 테스트 설정

### test { }
- **역할**: 테스트 실행 설정
- **주요 메서드**: `useJUnitPlatform()`, `testLogging { }`, `systemProperty()`

```groovy
test {
    useJUnitPlatform()  // JUnit 5 사용
    
    // 병렬 테스트 (성능 향상)
    maxParallelForks = Runtime.runtime.availableProcessors()
    
    // JVM 옵션
    jvmArgs = ['-Xmx1024m', '-XX:+UseG1GC']
    
    // 시스템 프로퍼티
    systemProperty 'spring.profiles.active', 'test'
    systemProperty 'user.timezone', 'UTC'
    
    // 환경 변수
    environment 'TEST_ENV', 'true'
    
    // 테스트 결과 상세 출력
    testLogging {
        events "passed", "skipped", "failed"
        exceptionFormat "full"
        showStandardStreams = false  // System.out 출력 표시 여부
        showCauses = true
        showStackTraces = true
    }
    
    // 실패 시 즉시 중단
    failFast = false
    
    // 특정 테스트만 실행
    filter {
        includeTestsMatching "*ServiceTest"
        excludeTestsMatching "*IntegrationTest"
    }
}

// 통합 테스트용 별도 Task
tasks.register('integrationTest', Test) {
    useJUnitPlatform {
        includeTags 'integration'
    }
    shouldRunAfter test
}
```

### jacoco (코드 커버리지)

```groovy
jacoco {
    toolVersion = '0.8.11'
}

jacocoTestReport {
    dependsOn test
    
    reports {
        xml.required = true
        html.required = true
        csv.required = false
    }
    
    afterEvaluate {
        classDirectories.setFrom(files(classDirectories.files.collect {
            fileTree(dir: it, exclude: [
                '**/config/**',
                '**/dto/**',
                '**/entity/**',
                '**/*Application*'
            ])
        }))
    }
}

jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                minimum = 0.80  // 80% 커버리지 요구
            }
        }
    }
}
```

---

## 7. 애플리케이션 실행

### bootRun { }
- **역할**: Spring Boot 애플리케이션 실행 설정

```groovy
bootRun {
    // 프로파일 지정
    args = ['--spring.profiles.active=dev']
    
    // 또는
    systemProperty 'spring.profiles.active', 'dev'
    
    // JVM 옵션
    jvmArgs = [
        '-Xmx512m',
        '-Dfile.encoding=UTF-8',
        '-Duser.timezone=Asia/Seoul'
    ]
    
    // 환경 변수
    environment 'DB_URL', 'jdbc:mysql://localhost:3306/mydb'
}
```

---

## 8. 프로퍼티 및 변수

### ext { }
- **역할**: 확장 프로퍼티 정의 (프로젝트 전역 변수)

```groovy
ext {
    set('springCloudVersion', '2023.0.0')
    set('testcontainersVersion', '1.19.3')
    
    // 또는
    lombokVersion = '1.18.30'
    querydslVersion = '5.0.0'
}

dependencies {
    implementation "org.projectlombok:lombok:${lombokVersion}"
    implementation "com.querydsl:querydsl-jpa:${querydslVersion}:jakarta"
}
```

### dependencyManagement { }
- **역할**: Spring Cloud 같은 BOM 관리

```groovy
dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
        mavenBom "org.testcontainers:testcontainers-bom:${testcontainersVersion}"
    }
}

dependencies {
    // 버전 명시 불필요 (BOM에서 관리)
    implementation 'org.springframework.cloud:spring-cloud-starter-config'
    implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'
}
```

---

## 9. QueryDSL 설정 (매우 자주 사용)

```groovy
// 빌드 스크립트 의존성
buildscript {
    ext {
        queryDslVersion = "5.0.0"
    }
}

plugins {
    id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
}

dependencies {
    implementation "com.querydsl:querydsl-jpa:${queryDslVersion}:jakarta"
    annotationProcessor "com.querydsl:querydsl-apt:${queryDslVersion}:jakarta"
    annotationProcessor "jakarta.annotation:jakarta.annotation-api"
    annotationProcessor "jakarta.persistence:jakarta.persistence-api"
}

// QueryDSL 설정
def querydslDir = "$buildDir/generated/querydsl"

querydsl {
    jpa = true
    querydslSourcesDir = querydslDir
}

sourceSets {
    main.java.srcDir querydslDir
}

compileQuerydsl {
    options.annotationProcessorPath = configurations.querydsl
}

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
    querydsl.extendsFrom compileClasspath
}

// clean 시 QueryDSL 생성 파일도 삭제
clean {
    delete file(querydslDir)
}
```

---

## 10. 멀티 모듈 프로젝트

### settings.gradle

```groovy
rootProject.name = 'ecommerce'

include 'common'      // 공통 모듈
include 'domain'      // 도메인 모듈
include 'api'         // API 서버
include 'batch'       // 배치 서버
include 'admin'       // 어드민 서버
```

### 루트 build.gradle

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0' apply false
    id 'io.spring.dependency-management' version '1.1.4' apply false
}

// 모든 서브프로젝트 공통 설정
subprojects {
    apply plugin: 'java'
    apply plugin: 'io.spring.dependency-management'
    
    group = 'com.example.ecommerce'
    version = '1.0.0'
    
    java {
        sourceCompatibility = '17'
    }
    
    repositories {
        mavenCentral()
    }
    
    // 모든 모듈 공통 의존성
    dependencies {
        compileOnly 'org.projectlombok:lombok'
        annotationProcessor 'org.projectlombok:lombok'
        
        testImplementation 'org.springframework.boot:spring-boot-starter-test'
    }
}

// API 서버들에만 적용
configure(subprojects.findAll { it.name in ['api', 'batch', 'admin'] }) {
    apply plugin: 'org.springframework.boot'
    
    dependencies {
        implementation project(':common')
        implementation project(':domain')
    }
}

// 라이브러리 모듈 (common, domain)
configure(subprojects.findAll { it.name in ['common', 'domain'] }) {
    bootJar.enabled = false
    jar.enabled = true
}
```

---

## 11. 프로파일별 빌드

```groovy
def profile = project.hasProperty('profile') ? project.property('profile') : 'dev'

bootJar {
    archiveFileName = "app-${profile}.jar"
    
    // 프로파일별 리소스만 포함
    from("src/main/resources") {
        include "application.yml"
        include "application-${profile}.yml"
        exclude "application-*.yml"
    }
}

// 실행 시: ./gradlew bootJar -Pprofile=prod
```

---

## 12. 커스텀 Task

```groovy
// 버전 정보 출력
tasks.register('printVersion') {
    doLast {
        println "Project: ${project.name}"
        println "Version: ${project.version}"
        println "Java: ${java.sourceCompatibility}"
    }
}

// 의존성 트리 출력 (충돌 확인)
tasks.register('depTree') {
    doLast {
        configurations.runtimeClasspath.resolvedConfiguration.resolvedArtifacts.each {
            println "${it.moduleVersion.id}"
        }
    }
}

// Git 정보 포함
tasks.register('gitInfo') {
    doLast {
        def gitBranch = 'git rev-parse --abbrev-ref HEAD'.execute().text.trim()
        def gitCommit = 'git rev-parse --short HEAD'.execute().text.trim()
        
        println "Branch: ${gitBranch}"
        println "Commit: ${gitCommit}"
    }
}
```

---

## 실무 전체 예제

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0'
    id 'io.spring.dependency-management' version '1.1.4'
    id 'jacoco'
}

group = 'com.example'
version = '1.0.0-SNAPSHOT'
description = 'E-commerce Backend API'

java {
    sourceCompatibility = '17'
}

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

ext {
    springCloudVersion = '2023.0.0'
    queryDslVersion = '5.0.0'
}

dependencies {
    // Spring Boot
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    implementation 'org.springframework.boot:spring-boot-starter-validation'
    
    // Database
    runtimeOnly 'com.mysql:mysql-connector-j'
    
    // Lombok
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    
    // QueryDSL
    implementation "com.querydsl:querydsl-jpa:${queryDslVersion}:jakarta"
    annotationProcessor "com.querydsl:querydsl-apt:${queryDslVersion}:jakarta"
    annotationProcessor 'jakarta.persistence:jakarta.persistence-api'
    annotationProcessor 'jakarta.annotation:jakarta.annotation-api'
    
    // JWT
    implementation 'io.jsonwebtoken:jjwt-api:0.12.3'
    runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.12.3'
    runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.12.3'
    
    // Test
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.security:spring-security-test'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}

tasks.withType(JavaCompile) {
    options.encoding = 'UTF-8'
    options.compilerArgs += ['-parameters']
}

test {
    useJUnitPlatform()
    
    testLogging {
        events "passed", "failed"
        exceptionFormat "full"
    }
}

bootJar {
    archiveFileName = 'app.jar'
}

jar {
    enabled = false
}
```
