# Pagination in Spring Data

- __페이지네이션 처리 방법은 크게 두 가지__
  - 데이터베이스에 접근하여 쿼리를 사용하는 경우
  - List 자료구조에 담긴 데이터를 Page 객체로 변환 하는 경우
- __클라이언트에 응답해야할 목록__
  - 컨텐츠의 전체 개수(Ex. totalCount)
  - 현재 페이지 번호(0 부터 시작)
  - 한 페이지에 몇개씩 보여줄 것인지(Ex. pageSize)
  - 컨텐츠(List 자료구조에 담긴 데이터)
  - 등등(필요에 따라서 추가 되거나 삭제)

> Spring Data 를 사용한다면 어쨋든 둘다 Pageable 객체를 사용할 것이다.

## Using Query

> 데이터베이스에 접근하여 쿼리를 사용하는 경우

```kotlin
@GetMapping("/resources")
fun showResources(
    @PageableDefault pageable: Pageable
): ResponseEntity<PageResponse<ResourceDto>> {
    val body = service.findAllResourceWithPage(pageable).let(::PageResponse)
    return ResponseEntity.ok(body)
}
```

> 데이터베이스에 접근하여 쿼리를 사용 하여 서비스가 Page 객체를 반환하는 경우에는 메서드명의 Suffix 에 WithPage 같은 것을 붙여주는 것이 좋다고 생각한다. 페이지네이션은 Presentation Layer 에 밀접한 로직이라고 생각한다. 

## List to Page 

> List 자료구조에 담긴 데이터를 Page 객체로 변환 하는 경우

```kotlin
@GetMapping("/resources")
fun showResources(
    @ModelAttribute request: resourceDto,
    @PageableDefault pageable: Pageable
): ResponseEntity<PageResponse<ResourceDto>> {
    val response = service.findAllResource(request)
    val body = PageFactory(response, pageable).create().let(::PageResponse)
    return ResponseEntity.ok(body)
}
```        

- Page 객체를 컨트롤러로 꺼냈을때의 장점은, 클라이언트에서 페이징을 사용하지 않기로 되었을때, 비지니스로직이 들어있는 서비스 코드를 수정할 필요가 없다. 컨트롤러만 수정하면 된다.

## Resource

```java
data class PageResponse<T>(
    val totalCount: Long,
    val page: Int,
    val pageSize: Int,
    val items: List<T>
) {
    constructor(page: Page<T>): this(
        totalCount = page.totalElements,
        page = page.number,
        pageSize = page.size,
        items = page.toList())
}
```

```java
data class PageFactory<T>(
    val items: List<T>,
    val pageable: Pageable
) {
    fun create(): Page<T> {
        val from = pageable.offset.toInt()
        val to = Integer.min((pageable.offset + pageable.pageSize).toInt(), items.size)

        if (from > to) {
            throw PageIndexOutOfBoundsException(from, to)
        }

        return PageImpl(items.subList(from, to), pageable, items.size.toLong())
    }
}
```

## References

- [Spring Data Pagination](https://wonit.tistory.com/483)
- [Spring Data JDBC Pagination](https://javabydeveloper.com/spring-data-jdbc-pagination-and-sorting-example/)
- https://tecoble.techcourse.co.kr/post/2021-08-15-pageable/
- [List to Page](https://stackoverflow.com/questions/37749559/conversion-of-list-to-page-in-spring)
- https://www.bezkoder.com/spring-boot-pagination-filter-jpa-pageable/
