
### Resolver
특정 요청이나 데이터를 해석하고 처리하는 역할을 하는 컴포넌트.

### 종류
- ViewResolver
- HandlerMethodArgumentResolver
- LocaleResolver

### ViewResolver
> 요청에 대한 결과를 어떤 뷰로 렌더링할지 결정합니다.
> JSP에서 많이 활용됩니다.


### HandlerMethodArgumentResolver
> 컨트롤러 메서드의 매개변수를 해석하고 적한 값을 바인딩 해주는 역할을 합니다.
> @RequestParam, @RequestBody 과 같은 어노테이션이 있는 메서드 매개변수에 적절한 값을 주입하는데 사용 됩니다.


### LocaleResolver
> 크라이언트의 요청에 따라 적절한 언어 또는 로케일을 설정하는 데 사용됩니다.
> SessionLocaleResolver or CookieLocaleResolver 등의 클래스들이 존재하며, 다국어 지원을 구현하는데 사용할 수 있습니다.

공통점을 살펴 보자면, 요청에 대한 액션을 취하는 것 인대, controll나 service단에서 비슷해보이는 로직들을 공통로직 화한 클래스들이라고 보면 된다.
