# ECS
Entity Component System의 약자로 기본적인 개념은 객체(Entity)의 데이터(Component)를 배열로 모아서 하나의 데이터 처리 클래스(System)를 통해 기존의 OOP보다 더욱 빠르게 데이터를 처리할 수 있도록 하는 개념이다.

## Entity
하나의 객체며, 단순히 Component들의 집합으로 표현된다.

## Component
객체를 표현하는 필드들의 집합으로, 객체마다 여러 Component가 있을 수 있다.

### Archetype
Archetype은 특정 컴포넌트 조합에 대한 배열의 묶음으로,
각각의 Archetype은 Chunk 단위로 나누어 관리된다.

예를 들어 컴포넌트 A, B, C가 있다면
Archetype은 A, B, AB, C, AC, BC, ABC 총 7가지의 Archetype이 존재할 수 있다.

여기에 AB Archetype을 예로 든다면
AB Archetype은
A컴포넌트 배열과 B컴포넌트 배열을 가지게 된다.

#### Chunk
Archetype내에서 데이터를 저장하는 단위로 보통 16kb와 같이 정해진 크기로 나누어 저장이 된다.

## System
특정 Component 또는 Component 조합에 대한 데이터 처리 메소드를 제공하는 클래스로, 실질적인 데이터 변경은 System에서 이루어진다.

# Unity Entities
유니티의 Entities 패키지는 유니티 내에서 ECS 구조를 쉽게 사용할 수 있도록 만들어진 패키지로써, ECS 외에도 [[Unity DOTS - Job System|Job System]]이나 [[Unity DOTS - Burst Compiler|Burst Compiler]]를 이용하여 ECS의 효과를 극대화 할 수 있도록 한다.