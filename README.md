π ___Table of Contents___

- [Working with Spring Data Repositories](https://github.com/BAEKJungHo/spring-data#working-with-spring-data-repositories)
- [Spring Data JPA](https://github.com/BAEKJungHo/spring-data#spring-data-jpa)
- [Spring Data JDBC](https://github.com/BAEKJungHo/spring-data#spring-data-jdbc)

# Working with Spring Data Repositories

> https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#repositories

- Spring Data Repository μΆμνμ λͺ©νλ λ€μν μμμ± μ μ₯μμ λν λ°μ΄ν° μ κ·Ό κ³μΈ΅μ κ΅¬ννλ λ° νμν μμ©κ΅¬ μ½λ(boilerplate)μ μμ ν¬κ² μ€μ΄λ κ²μ΄λ€.
- __μ½κ² λ§ν΄, λ°μ΄ν°λ₯Ό μ‘°ννκΈ° μν΄ Persistence Layer μΈ Repository λ₯Ό κ΅¬ννκΈ° μν΄μ νμν boilerplate λ₯Ό μ€μ΄λ κ²μ΄ λͺ©μ μ΄λ€.__
- Spring Data JPA μμ JpaRepository λ Infrastructure Layer μ μνλ©°, κ·Έκ²μ μμλ°μμ κ΅¬νν Repository λ Persistence Layer μ μνλ€.

## Core concepts

- Spring Data Repository μΆμνμ ν΅μ¬ μΈν°νμ΄μ€λ `Repository Inteface` μ΄λ€.
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
  - __μμν  μ νμ μΊ‘μ²__ νκ³ , __μΈν°νμ΄μ€λ₯Ό νμ₯νλλ° λμμ΄ λλ__ `λ§μ»€ μΈν°νμ΄μ€(Marker Interface)` μ­ν μ νλ€.
  - νΉμ  κΈ°μ μ μΆμνν JpaRepository, MongoRepository κ°μ Persistence λ₯Ό μ κ³΅νλ€. μ΄λ¬ν μΈν°νμ΄μ€λ€μ `CrudRepository` λ₯Ό μμ λ°κ³  μλ€. 
- __PagingAndSortingRepository<T, ID>__
  - νμ΄μ§ μ κ·Όμ μ©μ΄νκ² ν΄μ£Όλ μΆμνλ μΈν°νμ΄μ€
- __Derived Count Query__
  - CrudRepository λ₯Ό μμ λ°μμ μλ‘μ΄ μΈν°νμ΄μ€λ₯Ό λ§λ€ μ μλ€.
  - ```java
    interface UserRepository extends CrudRepository<User, Long> {
      long countByLastname(String lastname);
    }
    ```

### [Spring Data λ₯Ό μ¬μ©νκΈ° μν 4λ¨κ³κ° μ‘΄μ¬](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#repositories.query-methods)

- __Interface κ΅¬ν__
  - ```java
    interface PersonRepository extends Repository<Person, Long> { β¦ }
    ```
- __μΏΌλ¦¬ λ©μλ μ μΈ__
  - ```java
    interface PersonRepository extends Repository<Person, Long> {
      List<Person> findByLastname(String lastname);
    }
    ```
- __Java κΈ°λ° μ€μ __
  - ```java
    @EnableJpaRepositories
    class Config { β¦ }
    ```
- __xml κΈ°λ° μ€μ __
  - ```xml
    <jpa:repositories base-package="com.acme.repositories"/>
    ```
- __Repository λ₯Ό μ£Όμ λ°μμ μ¬μ©__
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

Spring Data JDBC λν `Spring Data Repositories` μ Core Concepts λ₯Ό κΈ°λ°μΌλ‘ νλ€.

## Why Spring Data JDBC?

- JPA μ κ²½μ°μ κ°λ°μλ₯Ό λμμ£ΌκΈ° μν Lazy Loading κ°μ λ§μ κ²λ€μ μ κ³΅νλ€. 
- νμ§λ§ JPA μμλ κ°λμ μΌλ‘ μ λ§ κ°λ¨ν κ²μ΄ μ€νλ € μ΄λ €μ μ§κΈ°λ νλ€.

Spring Data JDBC μ λͺ©νλ λ€μκ³Ό κ°λ€.

- If you load an entity, SQL statements get run. Once this is done, you have a completely loaded entity. No lazy loading or caching is done.
- If you save an entity, it gets saved. If you do not, it does not. There is no dirty tracking and no session.
- There is a simple model of how to map entities to tables. It probably only works for rather simple cases. If you do not like that, you should code your own strategy. Spring Data JDBC offers only very limited support for customizing the strategy with annotations.

## Domain Driven Design and Relational Databases

- λͺ¨λ  `Spring Data` μ κ΄λ ¨λ λͺ¨λλ€μ DDD μ `repository`, `aggregate` λ° `aggregate root` κ°λμμ μκ°μ λ°μλ€. 
- Repository λ νΉμ  μ νμ Aggregate μ μ»¬λ μ μ²λΌ λ³΄μ΄λ μμμ± μ μ₯μλ₯Ό μΆμν ν κ²μ΄λ€.
- μΌλ°μ μΌλ‘ Spring Data μ κ²½μ°, Repository λ Root Aggregate μ ν΄λΉνλ€.

## Auditing

- __package__
  - org.springframework.data.auditing;
- __class__
  - AnnotationAuditingMetadata

λ΄λΆμ μΌλ‘ Reflection μ μ¬μ©νλ€.

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

μ½νλ¦°μ μ¬μ©ν  λ, Super class λ₯Ό λ§λ€μ΄μ μμ€ν λ³μλ€μ λͺ°μλκ³ , νμ ν΄λμ€μμ μμν΄μ μ¬μ©νλ κ²½μ°(μ΄λ, Super class μ μμ±μκ° μλ νλλ°©μμΌλ‘ μ μΈλμ΄μκ³  getter, setter μ κ³΅)
νΉμ μμ ν΄λμ€μμ μμμ μ¬μ©νμ§ μκ³  μμ±μμ νλΌλ―Έν°λ‘ μ¬μ©νλ κ²½μ° λ λ€, Auditing κΈ°λ₯μ΄ μλνλ€.

λ°λΌμ, μλ§ 2κ°μ§ λ°©μμΌλ‘ λ¦¬νλ μμ΄ μΌμ΄λμ§ μμκΉ μκ°νλ€.

- μμ±μλ₯Ό ν΅ν΄μ κ°μ²΄λ₯Ό λ§λ  λ€μ λ°μΈλ©
- setter λ₯Ό νμ©νμ¬ λ¦¬νλ μ μ¬μ©

## References

- https://velog.io/@janeljs/Spring-Data-JDBC-1
- https://javabom.tistory.com/57
