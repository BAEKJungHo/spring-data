📚 ___Table of Contents___

- [Working with Spring Data Repositories](https://github.com/BAEKJungHo/spring-data#working-with-spring-data-repositories)
- [Spring Data JPA](https://github.com/BAEKJungHo/spring-data#spring-data-jpa)
- [Spring Data JDBC](https://github.com/BAEKJungHo/spring-data#spring-data-jdbc)

# Working with Spring Data Repositories

> https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#repositories

- Spring Data Repository 추상화의 목표는 다양한 영속성 저장소에 대한 데이터 접근 계층을 구현하는 데 필요한 상용구 코드(boilerplate)의 양을 크게 줄이는 것이다.
- __쉽게 말해, 데이터를 조회하기 위해 Persistence Layer 인 Repository 를 구현하기 위해서 필요한 boilerplate 를 줄이는 것이 목적이다.__
- Spring Data JPA 에서 JpaRepository 는 Infrastructure Layer 에 속하며, 그것을 상속받아서 구현한 Repository 는 Persistence Layer 에 속한다.

## Core concepts

- Spring Data Repository 추상화의 핵심 인터페이스는 `Repository Inteface` 이다.
  - ```java
    @Indexed
    public interface Repository<T, ID> {}
    ```
- _Spring Data JPA Repository Architectures_
  - `J`paRepository (interface)
    - `P`agingAndSortingRepository (interface)
      - `C`rudRepository (interface)
        - `R`epository (interface)
    - `Q`ueryByExampleExecutor (interface)
- __Repository<T, ID>__
  - T is `Domain class`
  - ID is `Identifier`
  - __작업할 유형을 캡처__ 하고, __인터페이스를 확장하는데 도움이 되는__ `마커 인터페이스(Marker Interface)` 역할을 한다.
  - 특정 기술을 추상화한 JpaRepository, MongoRepository 같은 Persistence 를 제공한다. 이러한 인터페이스들은 `CrudRepository` 를 상속 받고 있다. 
- __PagingAndSortingRepository<T, ID>__
  - 페이지 접근을 용이하게 해주는 추상화된 인터페이스
- __Derived Count Query__
  - CrudRepository 를 상속 받아서 새로운 인터페이스를 만들 수 있다.
  - ```java
    interface UserRepository extends CrudRepository<User, Long> {
      long countByLastname(String lastname);
    }
    ```

### [Spring Data 를 사용하기 위한 4단계가 존재](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#repositories.query-methods)

- __Interface 구현__
  - ```java
    interface PersonRepository extends Repository<Person, Long> { … }
    ```
- __쿼리 메서드 선언__
  - ```java
    interface PersonRepository extends Repository<Person, Long> {
      List<Person> findByLastname(String lastname);
    }
    ```
- __Java 기반 설정__
  - ```java
    @EnableJpaRepositories
    class Config { … }
    ```
- __xml 기반 설정__
  - ```xml
    <jpa:repositories base-package="com.acme.repositories"/>
    ```
- __Repository 를 주입 받아서 사용__
  - ```java
    class SomeClient {
        private final PersonRepository repository;

        SomeClient(PersonRepository repository) {
          this.repository = repository;
        }

        void doSomething() {
          List<Person> persons = repository.findByLastname("Matthews");
        }
     }
     ```

# Spring Data JPA

> https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference

- https://github.com/BAEKJungHo/JPA
- https://github.com/BAEKJungHo/JPA-and-Test-Code-Writing-Practice

# Spring Data JDBC

> https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference

## JDBC Repositories

Spring Data JDBC 또한 `Spring Data Repositories` 의 Core Concepts 를 기반으로 한다.

## Why Spring Data JDBC?

- JPA 의 경우에 개발자를 도와주기 위한 Lazy Loading 같은 많은 것들을 제공한다. 
- 하지만 JPA 에서는 개념적으로 정말 간단한 것이 오히려 어려워 지기도 한다.

Spring Data JDBC 의 목표는 다음과 같다.

- If you load an entity, SQL statements get run. Once this is done, you have a completely loaded entity. No lazy loading or caching is done.
- If you save an entity, it gets saved. If you do not, it does not. There is no dirty tracking and no session.
- There is a simple model of how to map entities to tables. It probably only works for rather simple cases. If you do not like that, you should code your own strategy. Spring Data JDBC offers only very limited support for customizing the strategy with annotations.

## Domain Driven Design and Relational Databases

- 모든 `Spring Data` 와 관련된 모듈들은 DDD 의 `repository`, `aggregate` 및 `aggregate root` 개념에서 영감을 받았다. 
- Repository 는 특정 유형의 Aggregate 의 컬렉션 처럼 보이는 영속성 저장소를 추상화 한 것이다.
- 일반적으로 Spring Data 의 경우, Repository 는 Root Aggregate 에 해당한다.

## Auditing

- __package__
  - org.springframework.data.auditing;
- __class__
  - AnnotationAuditingMetadata

내부적으로 Reflection 을 사용한다.

```java
/**
 * Creates a new {@link AnnotationAuditingMetadata} instance for the given type.
 *
 * @param type must not be {@literal null}.
 */
private AnnotationAuditingMetadata(Class<?> type) {

  Assert.notNull(type, "Given type must not be null!");

  this.createdByField = Optional.ofNullable(ReflectionUtils.findField(type, CREATED_BY_FILTER));
  this.createdDateField = Optional.ofNullable(ReflectionUtils.findField(type, CREATED_DATE_FILTER));
  this.lastModifiedByField = Optional.ofNullable(ReflectionUtils.findField(type, LAST_MODIFIED_BY_FILTER));
  this.lastModifiedDateField = Optional.ofNullable(ReflectionUtils.findField(type, LAST_MODIFIED_DATE_FILTER));

  assertValidDateFieldType(createdDateField);
  assertValidDateFieldType(lastModifiedDateField);
}
```

코틀린을 사용할 때, Super class 를 만들어서 시스템 변수들을 몰아두고, 하위 클래스에서 상속해서 사용하는 경우(이때, Super class 의 생성자가 아닌 필드방식으로 선언되어있고 getter, setter 제공)
혹은 자식 클래스에서 상속을 사용하지 않고 생성자의 파라미터로 사용하는 경우 둘 다, Auditing 기능이 작동한다.

따라서, 아마 2가지 방식으로 리플렉션이 일어나지 않을까 생각한다.

- 생성자를 통해서 객체를 만든 다음 바인딩
- setter 를 활용하여 리플렉션 사용

## References

- https://velog.io/@janeljs/Spring-Data-JDBC-1
- https://javabom.tistory.com/57
- [Spring Data Pagination](https://wonit.tistory.com/483)
- [Spring Data JDBC Pagination](https://javabydeveloper.com/spring-data-jdbc-pagination-and-sorting-example/)
