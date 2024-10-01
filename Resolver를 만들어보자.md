
Resolver를 만들고 싶은 욕구와 필요하다는 느낌이 강하게 온다.
그럼 만들어야지~!~!

### 상황
Spring에서 queryString을 받을 때 Controller의 메서드의 기본 형태는 아래와 같다.

```java
@GetMapping("")
public SampleListResponseDto gajyeowaList(@RequestParam String name, @RequestParam Integer age) {
	return service.gayeowaList(name, age);
}
```

> @RequestParam을 사용하는 것인데, 위의 코드 두개 만 치더라도 생각보다 귀찮았다.
> argument가 10개만 된다고 생각해보자, 이보다 완벽한 끔찍은 있을 수 없다. 
> 또한, 검색조건 하나더 추가 했다고 생각해보자, controller 수정, service 수정 해야 한다.

고래서 아래와 같은 형태로 많이 쓴다.

```java
@GetMapping("")
public SampleListResponseDto gajyeowaList(@RequestParam  Map<String, Object> queryMap) {
	return service.gayeowaList(queryMap);
}
```

> Map을 사용하여서 parameter를 유현하게 받고 검색조건이 추가되 더라도 유연하게 받을 수 있다.
> 그러나 Map으로 service에 넘기면 service 로직에서 Map에 있는 데이터를 가져올때 
> 	1. 매직스트링으로 꺼내와야 한다.
> 	2. value의 type이 Object이다. (다루고 싶은 type으로 변환하는 작업이 필요하다.)
> 	3. 너무 유연하다.

고래서 또 이런 형태를 전환 하였다.
```java
```java

@GetMapping("")
public SampleListResponseDto gajyeowaList(@RequestParam  Map<String, Object> queryMap) {

//    JsonMapper mapper = JsonMapper.builder()  
	JsonMapper mapper = JsonMapper.builder()
	.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false).build();

	SampleListRequestDto requestDto = 
	mapper.convertValue(queryMap, SampleLsitRequsetDto.class)

	return service.gayeowaList(queryMap);
}
```

> Map → Dto 로 변환 하여 service에 넘겨주었다.
> DTO를 사용한다면, Setter에 가벼운 method 정도는 넣을 수도 있고, 
> service에서 value를 꺼내올때, getMethod를 사용해서 가져올 수 도 있다.
> 형태가 괜찮아 보이는데, 저 JsonMapper로 convertValue시 매핑하는 정책을 적어주는 것이 외워도 볼려고 하고 이해하고 외워도 봤는데, 결국 복붙이 답이었다. 
> 좀 더 컴팩트 하게 할 수 있지 않을까 그리고 DTO에서 validation이 필요할때 service까지 가지 않고 할 수 있지 않을까?? (Like @RequestBody)


##### 고래서 Resolver를 만들어 볼려고 한다!!!!!
### HandlerMethodArgumentResolver
요거를 커스텀 구현할 것이다.
요게 무어냐면 요 게시글에 정리해 두었다. 요거보면 된다.


##### 자 우선 원하는 요구사항을 정리해보면
> 📌 Controller 메서드 argument DTO 앞에 '@' 어노테이션을 붙이면 queryString이 DTO에 매핑된다.
> 📌 매핑시 @JsonProperty, @JsonIgnore등이 잘 작동된다.
> 📌 @NotNull, @Size 등과 같은 Validate 어노테이션이 잘 작동된다.
###### 정리 끝 이제 만들자💪💪
#### @Annotation 만들기
resolver 작동 트리거인 Annotation을 만들거다.
```java
@Retention(RetentionPolicy.RUNTIME)  
@Target(ElementType.PARAMETER)  
public @interface QueryString {  
  
  boolean required() default true;  
  
}
```

우선 만듦