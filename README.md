# SpringBoot2 + WebFlux Reactive


1. Mono와 Flux 이해(RxJAVA 책 추천)
- Mono, Flux : ReactiveStream스펙으로 개발된 Reactor라이브러리 Publisher, Subscriber 구현체(Spring5에서 Reactive Framewokr으로 이용)
- Mono : Element 1개를 받으며, 한번의 Subscribe 이벤트만 사용 가능
- Flux : Element N개를 받으며, 여러번의 Subscribe 이벤트 사용 가능

2. @Annotation 기반의 Flux, Router & Handler Function기반의 Flux
- View를 사용하여 Model을 넘기고 싶은 경우, @Annotation 이용(model을 쉽게 넘길 수 있음)
- JSON 형태의 Response응답을 통해 전달하고 싶은 경우 RouterFunction기반의 기술 이용 
