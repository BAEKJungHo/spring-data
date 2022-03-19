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
