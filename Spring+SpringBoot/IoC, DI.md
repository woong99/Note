# IoC (Inversion of Control)

스프링에서는 일반적인 Java 객체를 new로 생성하여 개발자가 관리하는 것이 아닌 Spring Container에 맡긴다.

즉, 개발자에서 -> 프레임워크로 <span style="color:tomato">제어</span>의 객체 관리의 <span style="color:tomato">권한이 넘어 갔음</span>으로 <span style="color:tomato">"제어의 역전"</span>이라고 합니다.

---

# DI (Dependency Injection)

### DI 장점

- 의존성으로부터 격리시켜 코드 테스트에 용이하다.
- DI를 통하여, 불가능한 상황을 Mock와 같은 기술을 통하여, 안정적으로 테스트 가능하다.
- 코드를 확장하거나 변경할 때 영향을 최소화한다. (추상화)
- 순환참조를 막을 수 있다.
