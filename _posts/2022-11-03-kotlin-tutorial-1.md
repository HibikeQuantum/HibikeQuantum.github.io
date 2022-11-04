---
layout: single
category: devlog
tags: kotlin, tutorial
toc: true
date: 2022-09-01T09:35:20+09
last_modified_at: 2022-09-01T09:35:22+09
title: 
---

## 시리즈 개요
 회사에서 자바 프로젝트를 배포운영하다보니 자주 코드를 보는데, 다음 시대를 이끌어갈 기술은 뭘까 고민을 하던찰나 코틀린의 여러가지 장점에 대한 설명을 보고 '이거다!' 싶어. 간단한것부터 글로 정리해가고자 시작된 튜토리얼입니다. 문법의 설명은 다른 사이트가 많을테니 목적성을 가지고 글 한나 하나를 적어보려고 합니다. 시리즈 첫번째 주제는 JVM으로 간단한 코틀린 프로그램 실행.

## 환경
MAC M1 Montery 12.4, JVM 18.0.2, Gradle 7.5.1, IntelliJ CE
![](/assets/img/SCR-20221103-uqx.png){:.align-center}
Gradle 이 지원하는 JVM을 써야한다. JVM 설치는 IntelliJ의 프로젝트 생성 화면에서 클릭으로도 할 수 있다.
{: .caption}

## JVM에서 코틀린 프로그램 실행
![](/assets/img/SCR-20221103-uyb.png){:.align-center}
프로젝트 생성을 위한 기본설정
{: .caption}

다음의 값을 설정해준다.  
1) 프로젝트 위치, 이름 확인 2) 코틀린 프로젝트 3) Gradle 시스템 4) JDK 선택 5) 'Add sample code'  

![](/assets/img/SCR-20221103-uxk.png){:.align-center}
JDK19를 쓰고 싶었지만 Gradle이 지원을 하지 않아서 16을 선택했다.
{: .caption}

![](/assets/img/SCR-20221103-v1u.png){:.align-center}
생성된 프로젝트
{: .caption}

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
    kotlin("jvm") version "1.7.10"
    application
}

group = "org.example"
version = "1.0-SNAPSHO"

repositories {
    mavenCentral()
}

dependencies {
    testImplementation(kotlin("test"))
}

tasks.test {
    useJUnitPlatform()
}

tasks.withType<KotlinCompile> {
    kotlinOptions.jvmTarget = "1.8"
}

application {
    mainClass.set("MainKt")
}
```
{:.align-center}
build.gradle.kts
{: .captionC}
Gradle은 앱을 빌드 및 구성해주는 시스템[참조블로그](https://mkil.tistory.com/479). DSL(Domain Specific Lanuage)로 쓰인 'build.gradle.kts'는 무엇을 어떻게 빌드할지 정의하는 레시피다. this 항목들은 `org.gradle`패키지를 가르키고 있다. 빌드 시스템이 동작하면 gradle이 먼저 선행되 JVM과 Dependencies를 소화한 뒤 `Main.kt`를 읽어들이게 된다. 빌드 결과물은 `target` 디렉토리에 저장되고 JVM 위에서 실행된다.


![](/assets/img/SCR-20221104-el6.png){:.align-center}
{: .caption}
`RUN > Edit Configuration`에서 프로그램 실행환경을 설정한다. 1) MainApp이란 이름으로 2) Main.kt 프로그램을 실행하도록 패키지를 선택했다. 이제 `RUN > Run MainApp`을 하면 프로그램이 실행된다.

![](/assets/img/SCR-20221104-evo.png){:.align-center}
글에 적진 않았지만 leadln()을 이용해 입력을 받고 출력하는 코드를 추가했다.
{: .caption}

이걸로 프로젝트 생성부터 실행까지 진행했다. 다음부터는 자바를 하는 이유인 Spring 을 코틀린으로 작성해서 서비스 해보려고 한다.