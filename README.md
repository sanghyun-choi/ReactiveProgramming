# SpringBoot2 + WebFlux

1. Mono와 Flux 이해(RxJAVA 책 추천)
- Mono, Flux : ReactiveStream스펙으로 개발된 Reactor라이브러리 구현체 Publisher, Subscriber
- Mono : Element 1개를 받으며, 한번의 Subscribe이벤트만 사용 가능
- Flux : Element N개를 받으며, 여러번의 Subscribe이벤트 사용 가능

2. @Annotation기반과 Router&Handler Function기반의 WebFlux
- @Annotation : View를 리턴하며 View에 Model을 쉽게 넘기고 싶은 경우, @Annotation 이용(model을 쉽게 넘길 수 있음)
- Router&Handler : ServerResponse응답을 직접 전달하고 싶은 경우 RouterFunction기반의 기술 이용(요즘 api 서버로 쓰는 용도로 유행)

3. Netty서버 기반으로 war(Servlet기반)가 아닌 jar로 배포 가능
- jar를 실행 시 NettyWebServer start()가 실행되어 EmbededServer 운용 가능


