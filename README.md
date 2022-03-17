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
- __Spring Data JPA Repository Archtiectures__
  - JpaRepository (interface)
    - PagingAndSortingRepository (interface)
      - CrudRepository (interface)
        - Repository (interface)
    - QueryByExampleExecutor (interface)
- __Repository<T, ID>__
  - T is `Domain class`
  - ID is `Identifier`
  - __작업할 유형을 캡처__ 하고, __인터페이스를 확장하는데 도움이 되는__ `마커 인터페이스(Marker Interface)` 역할을 한다.
  - 특정 기술을 추상화한 JpaRepository, MongoRepository 같은 Persistence 를 제공한다. 이러한 인터페이스들은 `CrudRepository` 를 상속 받고 있다. 

# Spring Data JDBC

> https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference

## JDBC Repositories


