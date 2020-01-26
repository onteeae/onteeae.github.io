---
title: "Arcitectur Styles and the Design of Network-based Software Architectures"
date: 2020-01-24 20:00:00 -0400
categories: Translation
---
이 글은 Roy Fielding의 논문 <Architecture Styles and the Design of Network-based Software Architectures>을 번역, 요약한 글이다.

### Abstraction
이 논문은 네트워크 기반의 어플리케이션 소프트웨어의 아키텍처 설계를 평가하고, 이해하기 위한 동기에서 쓰여졌다.
이를 통해 바람직한 네트워크 어플리케이션 아키텍쳐의 기능적(fuctional), 성능(performance), 사회적(social) 제약 사항이 무엇인지를 알아낼 수 있을 것이다.
이 논문은 스타일(style)이라는 개념 통해 소프트웨어 아키텍처를 이해할 수 있는 프레임웍을 제안하고, 
어떻게 스타일이 네트워크 기반의 어플리케이션의 아키텍처를 가이드(guide)할 수 있는지 보여줄 것이다.
그 후 REST 아키텍처를 제안하는데, REST 아키텍처는 Component Interaction의 확장가능성(Scalability), 인터페이스의 일반/보편성(Generality),
Component들의 독립적인 배포, Latency를 줄이기 위한 중간 Component, 보안 강화, 그리고 Legacy 시스템의 캡슐화를 강조한다.


0. Introduction
현대적인 소프트웨어 시스템의 복잡도가 컴포넌트화된(componentized) 시스템을 필요로 하게됨.
컴포넌트화 된 시스템이란, 특정한 기능을 수행하기 위한 실제 구현이 서로 통신하는 독립적인 요소로 나누어 지는 것을 말함.
소프트웨어 아키텍처 연구는 1. 어떻게 시스템을 나누고, 2. 어떻게 요소들이 서로 통신하고 어떻게 정보들이 통신되는지, 3. 어떻게 시스템의 요소들이 서로 독립적으로 진화하는지 조사하는 과정
하지만 좋은 아키텍처는 아무것도 없이 생기는 것이 아니며, 여러 제약 사항이 있는 Specific한 분야위에서 만들어 지는것.
그런 면에서 이 논문은 소프트웨어와 네트워킹의 교차점을 탐구함.

1. Software Architecture
이 장은 논의를 진행하기 위한 여러 용어를 정의하는 부분.
1-1. Run-Time Abstraction
소프트웨어 아키텍처의 중심에는 추상화가 있다: 캡슐화를 통해 시스템의 디테일을 숨기는 것.
1-2. Element
한 번 만들어지면, 아키텍처는 그렇게 만들어진 이유 없이도 잘 동작함 -> Rationale을 제외함.
    1-2.1 Components
    요소(Component)는 소프트웨어 instruction의 추상화 단위이며, 인터페이스를 통해 데이터의 Transforamtion을 제공하는 내부상태.
    1-2-2. Connectors
    Connectors는 Component간의 통신(communication), coordination, 협동(cooperation)을 중재하는 추상화 메커니즘
    1.2.3. Data
    Data는 Connector를 통해 Component에서 Component로 전송되는 정보요소.
    Component안에 숨겨져있거나 영속적으로 존재하는 정보는 포함하지 않는다.
1.3 Configuration
Runtime 중 Component/Connector/Data간의 아키텍처적인 관계의 구조(Structure of Architectural Relationship).
상호작용하는 Component와 Connector들의 집합
1.4 Properties
소프트웨어 아키텍처의 특성(Property)란 시스템안의 Comopnent, connector, data의 선택과 arrangement에서 비롯됨.
아키텍처 설계의 목표는 아키텍처의 특성이 요구사항의 superset이 되도록 하는 것.
1.5 Styles
한 아키텍처적 스타일이란 그 방식을 따를 때 1)요소들의 역할과 기능을 그리고 2) 요소들의 관계를 제한하는 아키텍처적 제약사항이다.
스타일은 아키텍처 요소에 대한 중요한 결정을 캡슐화 하고 요소들과 그 관계의 제약을 강조한다.
새로운 아키텍처는 특정한 스타일의 instance라고 정의할 수 있다.
네트워크 기반의 어플리케이션에 대한 적절한 스타일을 고르는 것은 problem domain에 대한 이해를 필요로한다.
전통적인 건물 건축에서의 스타일이란 설계에 가해진 제약사항.
1.6 Partterns and Pattern Languages
디자인 패턴이란 중요하고 반복되는 system construct.
턴/패턴언어는 다양한 패턴으로 이루어질 수 있고, 객체들간의 바람직한 상호작용의 집합을 구현하기 위한 레시피로 생각 할 수 있다.

2. Network-based Application Architectures
이 논문은 Component들의 상호작용이 네트워크 커뮤니케이션으로써 구현되어있는 소프트웨어 아키텍처의 가장 높은 추상화 단계를 다룬다.
2.1.1 Network based vs Distributed
네트워크 기반의 아키텍처와 소프트웨어 아키텍처의 가장 기본적인 차이는 Component들간의 커뮤니케이션이 메시지 전달로 이루어진다는 것이다.
분산 시스템은 사용자에게 일반적인, 중앙화된(그러나 독립적인 여러개의 머신에서 돌아가는) 시스템에서 시스템이 돌아간다는 것 처럼 보인다.
반면에 네트워크 기반의 시스템은 네트워크를 통한 operation이 가능하고, 반드시 유저에게 투명한 방식일 필요는 없다는 것이다.
특히, 네트워크 사용은 추가적인 전송 비용을 요하기 때문에, 어떤 행동이 네트워크를 수반하고 어떤 행동이 로컬에서 수행되는지 구분될 필요도 있다.
2.1.2. Application Software vs Networking Software
또 우리는 이 논문의 논점에서 OS/네트워킹 소프트웨어/시스템 서포트를 위해서만 네트워크를 사용하는 어플리케이션 아키텍처를 배제할 것이다.
어플리케이션 소프트웨어는 전반적인 시스템의 추상화 레벨인데, 그 것은 유저 액션의 목적이 기능적인 아키텍처적 특성(functional architectural property)로 표현될 수 있다는 것이다.
이것은 네트워킹 추상화와는 차이가 있는데, 그 것의 목적은 한 장소에서 다른 장소로 비트들을 옮기는 데에 목적이 있지 왜 옮기는지에는 관심이 없다.
어플리케이션 레벨에서야 1) 유저 액션 당 interaction의 수, 2) 어플리케이션 상태의 위치, 3) 모든 data stream의 유효 throughput 4) 유저 액션 당 communication의 크기
를 기반으로 trade-offs를 평가할 수 있다.
2.2 Evaluating the Design Application Architecture
이 논문의 목적 중 하나는 주어진 어플리케이션 도메인에서 가장 적절한 아키텍처를 만들거나 선택하는 데에 design guidance를 주는 것이다.
아키텍처는 아키텍처적 디자인의 구현이지, 디자인 자체가 아니라는 것을 염두해야한다.
아키텍처는 런타임 특성으로 평가될 수도 있지만, 우리는 구현하기 전에 설계로부터 평가할 수 있는 걸 선호할 것이다.
아키텍처적 디자인을 평가하기 위해선, 우리는 그것이 시스템에 가하는 제약사항의 근거를 살펴보아야 하고, 또 그러한 제약사항으로부터 나오는 특성과 목적을 비교해보아야한다.
다음과 같은 방식으로 평가 할 수 있는데,
1) 첫 번째 방식은 어플리케이션의 기능적 요구사항을 만족하는지.
2) 시스템에 필요하다고 여겨지는 비 기능적 특성을 각각의 아키텍처가 어느 정도로 지원하는지.
이러한 요구/필요 사항들로 각 아키텍처의 Decision Tree를 만들어 평가 할 수 있다.
2.3 Architectural Properties of Key Interest
2.3.1 Performance
네트워크 기반 어플리케이션의 성능은 1)요구사항에, 2)상호작용 방식에, 3) 아키텍처의 실제 구현에, 4) 각 Component의 실제 구현에 의해서 결정된다.
2.3.1.1 Network Performance
Throughput : 어플리케이션 데이터와 오버헤드를 포함한 정보가 Component간의 교환되는 속도
Overhead : initial setup과 per-interaction으로 나누어짐.
Bandwidth : 주어진 네트워크 링크의 가능한 최대 throught
Usable bandwidth : Bandwidth에서 어플리케이션이 실제로 사용가능한 비율
스타일은 interaction per user action, 데이터 요소의 granualrity에 영향을 주기때문에 네트워크 성능에 영향을 줌
2.3.1.2 User-perceived Performance
Latency : 레이턴시는 첫 입력과 첫 응답 사이의 간격
이는 여러 요소로 나누어지는데,
1) application이 입력 event를 감지하는 시간
2) Component간의 상효작용을 set-up하는 시간
3) Component간의 각 상호작용을 전달하는 시간
4) 각 Component들이 각 상호작용을 처리하는 시간
5) 통신을 끝내고 결과를 처리하여 사용가능한 결과를 렌더링 하기 전 까지의 시간
여기서 3과 5만이 실제 네트워크 커뮤니케이션을 반영함.
Completion : 어플리케이션 Action을 완성하는데 드는 시간.
중요한 점은 보통 Latency를 최적화 하면 Completion Time을 악화시킴.
2.3.1.3 Network Efficiency
