## 자동 라이리브러리 만들기

우리가 만든 라이브러리를 사용해주는 고마운 고객 개발자를 위해, 프로젝트에 라이브러리를 추가만 하면 모든 구성이 자동으로 처리되도록 해보자. 

쉽게 이야기해서 스프링 빈들이 자동으로 등록되는 것이다. 여기에 추가로 `memory=on` 옵션도 적용할 수 있게 해보자.

이렇게 하려면 메모리 라이브러리의 기능을 업그레이드 해야한다. 기존 프로젝트를 유지하기 위해 프로젝트를 복사하고 일부 수정하자

`memory-v1` 프로젝트를 복사해서 `memory-v2` 를 만들자. 

**settings.gradle - 수정**

```
rootProject.name = 'memory-v2' //v1 -> v2로 수정 
```

### 자동 구성 추가

```java
@AutoConfiguration
@ConditionalOnProperty(name = "memory", havingValue = "on")
public class MemoryAutoConfig {

    @Bean
    public MemoryFinder memoryFinder() {
        return new MemoryFinder();
    }

    @Bean
    public MemoryController memoryController() {
        return new MemoryController(memoryFinder());
    }
}

```

* `@AutoConfiguration`
  * 스프링 부트가 제공하는 자동 구성 기능을 적용할 때 사용하는 애노테이션이다. 
* `@ConditionalOnProperty`
  * `memory=on` 이라는 환경 정보가 있을 때 라이브러리를 적용한다. (스프링 빈을 등록한다.) 
  * 라이브러리를 가지고 있더라도 상황에 따라서 해당 기능을 켜고 끌 수 있게 유연한 기능을 제공한다.

#### 자동 구성 대상 지정

이 부분이 중요하다. 스프링 부트 자동 구성을 적용하려면, 다음 파일에 자동 구성 대상을 꼭 지정해주어야 한다.

폴더 위치와 파일 이름이 길기 때문에 주의하자

**파일 생성** 

`src/main/resources/META-INF/spring/

org.springframework.boot.autoconfigure.AutoConfiguration.imports` 

**org.springframework.boot.autoconfigure.AutoConfiguration.imports**

```
 memory.MemoryAutoConfig
```

앞서 만든 자동 구성인 `memory.MemoryAutoConfig` 를 패키지를 포함해서 지정해준다.

스프링 부트는 시작 시점에 `org.springframework.boot.autoconfigure.AutoConfiguration.imports` 의 정보를 읽어서 자동 구성으로 사용한다. 

따라서 내부에 있는 `MemoryAutoConfig` 가 자동으로 실행된다.

### 빌드하기

* 다음 명령어로 빌드하자.
  * `./gradlew clean build`
* 빌드 결과
  * `build/libs/memory-v2.jar`

스프링 부트 자동 구성 기능이 포함된 `memory-v2.jar` 를 이제 프로젝트에 적용해보자.

[다음 내용](https://github.com/sunlike0508/spring-memory-project-v2)
