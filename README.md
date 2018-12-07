# SpringBoot2 + WebFlux

1. Mono와 Flux 이해(RxJAVA 책 추천)
- Mono, Flux : ReactiveStream스펙으로 개발된 Reactor라이브러리 구현체 Publisher, Subscriber
- Mono : Element 1개를 받으며, 한번의 Subscribe이벤트만 사용 가능
- Flux : Element N개를 받으며, 여러번의 Subscribe이벤트 사용 가능



2. @Annotation기반과 Router&Handler Function기반의 WebFlux
- @Annotation : View를 리턴하며 View에 Model을 쉽게 넘기고 싶은 경우, @Annotation 이용(model을 쉽게 넘길 수 있음)
- Router&Handler : ServerResponse응답을 직접 전달하고 싶은 경우 RouterFunction기반의 기술 이용(요즘 api 서버로 쓰는 용도로 유행)
- 결과 : 
TemplateEngine(Freemarker, thymeleaf) 사용시 View요청을 받는 경우 @Annotation 패턴을 이용
HandlerFunction을 통해 View와 requestParameter, Model을 원활하게 이용하는 방법을 원했으나, 현재 방법으로는 찾기 힘듬
(RequestParameter자체를 Mono타입으로 받기때문에 Repository까지 Reacive를 지원하는 ReactiveMongoRepository를 이용해야 쉽게 가능) 

* @Annotation은 Controller, FunctionHandler는 Service로 이용해서 개발



3. Netty서버 기반으로 war(Servlet기반)가 아닌 jar로 배포 가능
- jar를 실행 시 NettyWebServer start()가 실행되어 EmbededServer 운용 가능



4. ViewTemplate(thymeleaf)
- thymeleaf를 이용시 stream형태로 rendering이 가능
- Freemarker, Mustache는 
