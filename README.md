# SpringBoot2 + WebFlux

1. Mono와 Flux 이해(RxJAVA 책 추천)
- Spring5에서는 request Flow를 Mono형태로 처리한다. 즉 methodParameter도 Mono타입, response도 Mono타입
- Mono, Flux : ReactiveStream스펙으로 개발된 Reactor라이브러리 구현체로 Publisher, Subscriber를 쉽게 개발 가능
- Mono : Element 1개를 받으며, 여러 Subscribe이벤트 사용 가능
- Flux : Element N개를 받으며, 여러 Subscribe이벤트 사용 가능

<br/>

2. @Annotation기반과 Router&Handler Function기반의 WebFlux
- @Annotation : View리턴(Model을 쉽게 넘기고 싶은 경우), @Annotation이용(기존 프로그래밍 방식에 유리)
- Router&Handler : ServerResponse응답을 직접 전달하고 싶은 경우 RouterFunction기반의 기술 이용(선언형 프로그래밍,함수형)
- 결과 : 
TemplateEngine(Freemarker, thymeleaf)사용시 View요청을 받는 경우 기존 @Annotation방식을 이용하여 쉽게 적용
HandlerFunction을 통해 requestParameter자체를 Mono타입 or 일반 parameterType으로 가져올 수 있음
* ServerRequest.bodyToMono는 리액티브 프로그래밍에서 subscribe가 한번만 가능하다. 따라서 handler내부 로직에서 별도로 사용은 불가능하며 그 이유는 Blocking되는 이슈로 지원불가하다고 함, handler return에서 필수적으로 사용해야 함
또한, ServerRequest.bodyToMono는 subscribe가 2번이상 불가하기 때문에 리턴 로직에서 Mono.zip을 이용할 경우 bodyToMono로 여러군데서 사용시 사용 불가, subscribe가 2번 이상 일어날 수 있기 때문이다. 따라서 bodyToMono 생성 후 새로운 Mono타입으로 생성하여 이용해야 한다.

(https://stackoverflow.com/questions/48867886/spring-webflux-netty-handler-cant-parse-serverrequest-containing-json-larger)
* Handler방식을 이용하여 View 리턴 방식을 제공할 경우 ServerResponse.render를 통해 제공 가능, 이 때 데이터는 map형태로 제공

<br/>

3. Netty서버 기반으로 war(Servlet기반)가 아닌 jar로 배포 가능
- jar를 실행 시 NettyWebServer start()가 실행되어 EmbededServer 운용 가능
- Servlet을 이용하지 않고 웹 http서버 운용이 쉽게 가능
- 스레드 개수를 제한하여 많은 양의 request 처리 가능(EventLoop 형태)
- request요청을 받는 mainWorker와 jobWorker를 달리하여 처리

<br/>

4. ViewTemplate(thymeleaf)
- thymeleaf를 이용시 stream형태로 rendering이 가능(response시 stream형태로 계속 출력, 대용량일 경우 메모리 이슈를 제거할 수 있음)
- Freemarker, Mustache는 stream형태의 datasource 제공 x
- template엔진을 이용할 경우 thymeleaf가 좋아보임

<br/>

5. TestCode작성
- @WebFluxTest 어노테이션을 활용하여 WebTestClient를 통해 Request 테스트 쉽게 가능
- Reactor 코드를 테스트 하고 싶다면 StepVerfier를 이용하여 테스트 코드를 작성(io.projectreactor / reactor-test dependency 추가)

<br/>

6. subScribeOn과 publishOn 차이
- subScribeOn : subscribe로 구독을 시작할 때 실행될 Thread를 지정할 수 있다. 2번 사용불가, 처음 시작하는게 고정
- publishOn : 선언 후 이후에 실행되는 연산자는 publishOn에서 지정한 Thread에서 실행되게 할 수 있다. 다중 사용 가능

<br/>

7. Cold와 Hot 개념
- Cold : 일반적으로 subscribe하면 선언된 모든 Mono, Flux 연산자를 통해 데이터를 처음부터 발행한다.
- Hot : subscribe를 하지 않아도 publisher로 부터 데이터 발행이 진행된다. 따라서 계속 데이터가 발행될 수 있다. Mono나 Flux의 publish()연산자를 통해 ConnectableFlux를 얻어 autoConnect, share등의 연산자를 이용하여 데이터를 발행한다.

<br/>

8. ServerRequest 활용
- GET : request.pathVariable, request.queryParam, request.queryParams(.toSingleMap이용) 활용하여 값을 가져옴
- POST : request.bodyToMono를 활용하여 값을 가져옴(raw타입, 즉 json)
- POST - application/x-www-form-urlencoded : BodyExtractors.toFormData를 활용하여 값을 가져와야 함
(MultiValueMap형태이기때문에 (key,List) 구조의 Map으로 저장됨, toSingleMap으로 변환하여 Map형태로 제공 가능)

<br/>

9. security + jwt 기반 인증 활용
- 기존 spring-security는 ThreadLocal방식을 활용하여 같은 thread or 자식 thread에게만 security principal 데이터 제공
- webflux는 ThreadLocal 이용 불가(thread에 제한적이지 않음), 이러한 이유로 ServerWebExchange에 속성으로 저장하며 내부적으로 Reactor Context활용)
- authManager, userDetails, userDetailService, springSecurityFilterChain등 기술 사용 스펙은 비슷
- jwt를 활용하여 인증 구현(Header에 데이터 저장, 쿠키도 이용 가능할 듯함)











