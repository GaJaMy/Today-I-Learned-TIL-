# 클린 아키텍쳐
로버트 C. 마틴이 제안한 소프트웨어 설계 원칙으로, 관심사 분리를 통해 시스템을 유연하고, 유지보수하기 쉬우며, 테스트하기 용이하게 만드는 것을 목표로 한다. 여러 계층으로 시스템을 나누고 계층 간의 의존성을 최소화하며, 핵심 비즈니스 로직을 외부 기술에 종속되지 않도록 설계하는 것이 특징

## 의존 규칙
클린 아키텍쳐의 의존 규칙은 안쪽을 향해야 한다. 안쪽의 원은 바깥쪽 원에 대해 알 수 없어야 한다.

![클린 아키텍쳐 이미지](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdna%2FEeyCW%2Fbtq4oQ8NtZa%2FAAAAAAAAAAAAAAAAAAAAAKjuYsEkXUhU1MIRWKJZ7CQHonbwLW2KXfxCEPIpE-lm%2Fimg.jpg%3Fcredential%3DyqXZFxpELC7KVnFOS48ylbz2pIh7yKj8%26expires%3D1759244399%26allow_ip%3D%26allow_referer%3D%26signature%3DDYxp%252BqP0AVZKnsxyq1QSw5L%252Bz3E%253D)

