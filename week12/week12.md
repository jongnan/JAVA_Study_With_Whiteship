# 애노테이션

### 👩🏼‍💻목표

> 자바의 애노테이션에 대해 학습하세요

## 애노테이션

애노테이션은 Java 5부터 새롭게 추가된 요소로, 코드에 **메타 데이터**를 표현하는 것이 주 목적이다(다양한 목적으로 사용). 표현은 앞에 **`@`를 붙혀 사용이 가능**하다. 애노테이션은 클래스, 메서드, 변수, 매개 변수 및 패키지에 붙힐 수 있다. 이렇게 붙혀진 애노테이션은 바이트 코드에 포함되며, Reflection을 통해 자세하게 읽을 수 있다.

> **메타 데이터**
>
> 데이터에 대한 데이터로 데이터 그 자체가 아닌 해당 데이터의 속성을 설명하는 데이터 - [정보통신기술용어해설](http://www.ktword.co.kr/abbr_view.php?m_temp1=1195)

### Built-in 애노테이션

자바에서는 몇가지 기본적으로 제공하는 애노테이션이 존재한다.

* @Override

  > 해당 메서드는 오버라이드(재정의)가 되었음을 알린다. 만약 상위 클래스/인터페이스에서 해당 메서드를 찾을 수 없다면 컴파일 에러

* @Deprecated

  > 해당 메서드는 더 이상 사용되지 않음을 표시한다. 사용시에는 컴파일 경고를 발생시킨다.

* @SuppressWarning

  > 컴파일 경고를 무시하도록 한다.

* @SafeVarargs

  > Java 7부터 지원 가능하며 가변 인자의 매개변수를 사용할 때 경고를 무시한다.

* @FuntionalInterface

  > Java 8부터 지원 가능하며 함수형 인터페이스를 지정한다. 만약, 메서드가 존재하지 않거나 한개 이상의 메서드(default 메서드 제외)가 존재한다면 컴파일 오류를 발생 시킨다.

### Meta 애노테이션

자바에서 기본적으로 제공하는 애노테이션을 보면 그 안에 또 다른 애노테이션이 존재하는 것을 볼 수 있다.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```

`@Override` 안에 `@Target`, `@Retention`이라는 애노테이션을 볼 수 있다. 바로 **메타 애노테이션**으로 이들을 통해 커스텀 애노테이션을 만들 수 있다. 어떠한 종류가 있는지 살펴보자.

* @Retention

  > 해당 애노테이션이 어떻게 저장될지에 대해 지정한다.
  >
  > * SOURCE → 컴파일시에 애노테이션 정보가 사라짐(바이트코드 존재 X)
  > * CLASS → 클래스 파일에 존재하고 컴파일러에 의해 사용 가능, 런타임 시에 사라짐
  > * RUNTIME → 애노테이션의 정보를 가상 머신에서 참조가 가능, Reflection에 의해 사용

* @Documented

  > 문서 안에 해당 애노테이션의 존재를 표기하도록 지정한다.

* @Target

  > 해당 애노테이션이 적용되는 요소를 제한하기 위해 표시한다.
  >
  > * 종류
  >
  >   | 타입            | 대상                       |
  >   | --------------- | -------------------------- |
  >   | PACKAGE         | 패키지                     |
  >   | TYPE            | 클래스, 인터페이스, 열거형 |
  >   | ANNOTATION_TYPE | 애노테이션 타입            |
  >   | CONSTRUCTOR     | 생성자                     |
  >   | FIELD           | 멤버 변수                  |
  >   | LOCAL_VARIABLE  | 지역 변수                  |
  >   | MEHTOD          | 메서드                     |
  >   | PARAMETER       | 매개 변수                  |
  >   | TYPE_PARAMETER  | 매개 변수 타입             |
  >   | TYPE_USE        | 타입 사용                  |

* @Inherited

  > 애노테이션의 상속을 가능하게 한다.

* @Repeatable

  > Java 8부터 지원 가능하며 연속적으로 같은 애노테이션을 선언할 수 있도록 한다.

### 정의하기

애노테이션을 정의하는 방법은 굉장히 단순하다.

```java
public @interface 애노테이션이름 {}
```

위와 같이 `@interface` 키워드를 사용하여 정의할 수 있다. 애노테이션의 경우 요소를 가질 수 있으며, 이들은 없어도 되고 하나 이상 가질 수 있다.

```java
@MyAnnotation(name="jongnan")

public @interface MyAnnotation {
  String name();
}
```

요소들은 몸체가 없는 메서드 형태로 기본값을 지정할 수 있다.(default 키워드 사용) 만약 기본값을 지정하지 않은 요소가 있다면 컴파일 에러가 발생한다.

<br>

## 애노테이션 프로세서

애노테이션 프로세싱은 **컴파일 시간에 애노테이션들을 스캐닝하고 프로세싱하는 javac에 속한 빌드툴**이다. 이를 통해 특정 애노테이션들의 애노테이션 프로세서를 만들어 등록이 가능하다. 이러한 프로세서는 자바 코드 혹은 바이트 코드를 받아 파일(보통 .java 파일)을 생성 가능하다. 즉, **새로운 코드의 삽입이 가능**하다는 얘기다(이미 만들어진 자바 파일은 불가능). 

### 프로세서 API

모든 프로세서들은 `AbstractProcessor` 를 상속 받아야 한다. 이 안에 오버라이드 함수들이 존재하는데 잠깐 살펴보고 가자.

* init(ProcessingEnvironment env)

  > 프로세서의 처리 환경을 초기화 한다.

* process(Set<? extends TypeElement> annotations, RoundEnvironment env)

  > 각 프로세서의 `main()` 메서드의 역할을 한다. 이곳에 scanning, evaluating, annotation processing, 자바 파일 생성을 위한 코드를 작성한다. `RounEvironment`를 통해 특정 애노테이션을 찾을 수 있다.

* getSupportedAnnotationTypes()

  > 프로세서가 처리할 애노테이션들을 명시한다.

* getSupportedSourceVersion()

  > 특정 자바 버전을 명시하는데 사용한다. 보통은 `SourceVersion.latestSupported()`를 리턴한다.

### 등록

이렇게 만든 프로세서를 실행만 한다고 해서 컴파일러에 자동으로 등록되는 것은 아니다. 먼저 META-INF/services 안에 `javax.annotation.processing.Processor` 라고 불리우는 파일을 생성하고 이를 .jar 파일로 패키징을 해야한다. 새로 생성한 파일의 내용은 줄바꿈을 기준으로 사용하는 프로세서들의 Full Qualified Class Name을 적는다.

<br>

---

### Reference

* [Java Annotation과 Reflection이란?](https://qssdev.tistory.com/27)
* [자바 어노테이션(Java Annotations)](https://jdm.kr/blog/216)
* [Java Annotation이란?](https://nesoy.github.io/articles/2018-04/Java-Annotation)
* [Java Annotations - JENKOV.COM](http://tutorials.jenkov.com/java/annotations.html#create-your-own-java-annotations-video-tutorial)
* [Annotation Processing 101 (번역)](https://medium.com/@jason_kim/annotation-processing-101-%EB%B2%88%EC%97%AD-be333c7b913)

