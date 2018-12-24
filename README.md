# SpringBoot2 + WebFlux

1. Mono와 Flux 이해(RxJAVA 책 추천)
- Mono, Flux : ReactiveStream스펙으로 개발된 Reactor라이브러리 구현체 Publisher, Subscriber
- Mono : Element 1개를 받으며, 여러 Subscribe이벤트 사용 가능
- Flux : Element N개를 받으며, 여러 Subscribe이벤트 사용 가능
 
2. @Annotation기반과 Router&Handler Function기반의 WebFlux
- @Annotation : View를 리턴하며 View에 Model을 쉽게 넘기고 싶은 경우, @Annotation 이용(model을 쉽게 넘길 수 있음)
- Router&Handler : ServerResponse응답을 직접 전달하고 싶은 경우 RouterFunction기반의 기술 이용(요즘 api 서버로 쓰는 용도로 유행)
- 결과 : 
TemplateEngine(Freemarker, thymeleaf) 사용시 View요청을 받는 경우 @Annotation 패턴을 이용
HandlerFunction을 통해 View와 requestParameter, Model을 원활하게 이용하는 방법을 원했으나, 현재 방법으로는 찾기 힘듬
(RequestParameter자체를 Mono타입으로 받기때문에 Repository까지 Reacive를 지원하는 ReactiveMongoRepository를 이용해야 쉽게 가능)
* ServerRequest.bodyToMono는 handler 내부 로직에서 사용되어서 subscribe이용 불가능, Blocking되는 이슈로 지원불가, return에서 사용해야 함
또한, ServerRequest.bodyToMono는 subscribe가 2번이상 불가하기 때문에 Mono.zip이용할 경우 param을 여러군데서 사용시 사용 불가, subscribe가 2번 이상 일어날 수 있기 때문.
(https://stackoverflow.com/questions/48867886/spring-webflux-netty-handler-cant-parse-serverrequest-containing-json-larger)
* Handler방식을 이용하여 View 리턴 방식을 제공할 경우 ServerResponse.render를 통해 제공 가능, 이점은 모르겠음.

3. Netty서버 기반으로 war(Servlet기반)가 아닌 jar로 배포 가능
- jar를 실행 시 NettyWebServer start()가 실행되어 EmbededServer 운용 가능
- Servlet을 이용하지 않고 웹 http서버 운용이 쉽게 가능

4. ViewTemplate(thymeleaf)
- thymeleaf를 이용시 stream형태로 rendering이 가능(response시 stream형태로 계속 출력, 대용량일 경우 메모리 이슈를 제거할 수 있음)
- Freemarker, Mustache는 stream형태의 datasource 제공 x
- template엔진을 이용할 경우 thymeleaf가 좋아보임

5. TestCode작성
- @WebFluxTest 어노테이션을 활용하여 WebTestClient를 통해 Request 테스트 쉽게 가능
- Reactor 코드를 테스트 하고 싶다면 StepVerfier를 이용하여 테스트 코드를 작성(io.projectreactor / reactor-test dependency 추가)

6. subScribeOn과 publishOn 차이
- subScribeOn : subscribe로 구독을 시작할 때 실행될 Thread를 지정할 수 있다. 2번 사용불가, 처음 시작하는게 고정
- publishOn : 선언 후 이후에 실행되는 연산자는 publishOn에서 지정한 Thread에서 실행되게 할 수 있다. 다중 사용 가능

7. Cold와 Hot 개념
- Cold : 일반적으로 subscribe하면 선언된 모든 Mono, Flux 연산자를 통해 데이터를 처음부터 발행한다.
- Hot : subscribe를 하지 않아도 publisher로 부터 데이터 발행이 진행된다. 따라서 계속 데이터가 발행될 수 있다. Mono나 Flux의 publish()연산자를 통해 ConnectableFlux를 얻어 autoConnect, share등의 연산자를 이용하여 데이터를 발행한다.

8. ServerRequest 활용
- GET : request.pathVariable 활용하여 값을 가져옴
- POST : request.bodyToMono를 활용하여 값을 가져옴(raw타입, 즉 json)
- POST - application/x-www-form-urlencoded : BodyExtractors.toFormData를 활용하여 값을 가져와야 함
