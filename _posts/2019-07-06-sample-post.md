---
title: Sample Post
author: Moon
layout: blog
---

## Get Involved

* 하이버네이트를 사용하고 어떤 버그나 이슈를 발견하면 보고해라. 자세한 내용은 [Issue Traker](#http://hibernate.org/issuetracker)를 참조해라.   
`Use Hibernate and report any bugs or issues you find. See Issue Tracker for details.`

* 버그를 수정하거나 개선 사항을 구현하는 것을 시도하세요. 이것 또한 [Issue Tracker](#http://hibernate.org/issuetracker)를 참조하십시오.  
`Try your hand at fixing some bugs or implementing enhancements. Again, see Issue Tracker.`

* 커뮤니티 섹션에 나열된 메일링 리스트, 포럼, IRC 또는 기타 방법을 사용하여 [커뮤니티](#http://hibernate.org/community)와 소통하십시오.  
`Engage with the community using mailing lists, forums, IRC, or other ways listed in the Community section.`

* 이 문서를 개선하거나 번역하는데 도움을 주세요. 관심이 있다면 개발자 메일링 리스트에 문의하십시오.  
`Help improve or translate this documentation. Contact us on the developer mailing list if you have interest.`

* 나머지 조직들에게 하이버네이트의 이점에 대해 알려주세요.  
`Spread the word. Let the rest of your organization know about the benefits of Hibernate.`

> 소스를 통해 하이버네이트 5.1 또는 그 전 버전으로 빌드할 때, JDK 1.6 컴파일러 버그가 있기 때문에 Java 1.7 이 필요하다.  
`When building Hibernate 5.1 or older from sources, you need Java 1.7 due to a bug in the JDK 1.6 compiler.`

## Getting Started Guide
새로운 사용자는 튜토리얼 뿐만 아니라 기본적인 정보를 위해 [Hibernate Getting Started Guide](https://github.com/ootb-kr/ootb-wiki/tree/master/Hibernate/orm/getting-started#hibernate-getting-started-guide)를 먼저 살펴 볼 수 있다. 다양한 주제에 대해 더 깊게 제공하는 [주제별 가이드](http://docs.jboss.org/hibernate/orm/5.4/topical/html_single/)가 있다.  
`New users may want to first look through the Hibernate Getting Started Guide for basic information as well as tutorials. There is also a series of topical guides providing deep dives into various topics.`

> 하이버네이트를 사용하는 것은 SQL에 대해 강한 백그라운드를 가지고 있어야 하는 것은 아니지만, 결국 SQL 문으로 귀결되기 때문에 SQL에 대한 지식은 확실히 많은 도움을 줄거라 확신한다. 데이터 모델링 원칙을 이해하는 것도 중요 할 것입니다. 아래 두개의 자료들은 좋은 출발점으로 생각한다.  
`While having a strong background in SQL is not required to use Hibernate, it certainly helps a lot because it all boils down to SQL statements. Probably even more important is an understanding of data modeling principles. You might want to consider these resources as a good starting point:  `
>  * [Data Modeling Wikipedia definition](http://en.wikipedia.org/wiki/Data_modeling)
>  * [Data Modeling 101](http://www.agiledata.org/essays/dataModeling101.html)
>  
> 단위 작업 [PoEAA](http://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#PoEAA)(파울러 마틴, Pattern of Enterprise Application Architecture) 또는 어플리케이션 트랜잭션과 같은 트랜잭션 및 디자인 패턴의 기본 사항을 이해하는 것도 중요합니다. 이러한 주제들은 문서에서 논의되지만 사전 이해가 도움이 될 것이다.  
`Understanding the basics of transactions and design patterns such as Unit of Work PoEAA or Application Transaction are important as well. These topics will be discussed in the documentation, but a prior understanding will certainly help.`

## 1.1. Overview
<img width="216" alt="orm_1 1 1" src="https://user-images.githubusercontent.com/4423295/58580930-3c946780-8289-11e9-97f1-50a60b5ab885.png">

ORM 솔루션 인 하이버네이트는 위의 다이어그램에서 볼 수 있듯이 자바 애플리케이션 데이터 액세스 레이어와 관계형 데이터베이스 사이에 있다. 자바 애플리케이션은 하이버네이트 API를 사용하여 도메인 데이터를 로드, 저장, 쿼리 등을 수행한다. 여기서는 필수적인 Hibernate API를 소개 할 것이다. 이것은 간단한 소개이며, 나중에 자세히 논의 할 것이다.  
`Hibernate, as an ORM solution, effectively "sits between" the Java application data access layer and the Relational Database, as can be seen in the diagram above. The Java application makes use of the Hibernate APIs to load, store, query, etc its domain data. Here we will introduce the essential Hibernate APIs. This will be a brief introduction; we will discuss these contracts in detail later.`

JPA 프로바이더로서, 하이버네이트는 Java Persistence API 스펙을 구현하고 JPA 인터페이스와 구현체인 하이버네이트 사이의 연관성은 다음 다이어그램에서 시각화 될 수 있습니다.  
`As a JPA provider, Hibernate implements the Java Persistence API specifications and the association between JPA interfaces and Hibernate specific implementations can be visualized in the following diagram:`

<img width="861" alt="orm_1 1 2" src="https://user-images.githubusercontent.com/4423295/58580929-3c946780-8289-11e9-9f5c-069029591aaa.png">

**_SessionFactory (org.hibernate.SessionFactory)_**  
어플리케이션 도메인 모델을 데이터베이스에 매핑하는 쓰레드 세이프 한 (변경 불가한) 표현이다. org.hibernate.Session 인스턴스의 팩토리로서 동작한다. EntityManagerFactory는 SessionFactory와 대응되며, 기본적으로 그 둘은 같은 SessionFactory 구현체로 수렴된다.
`A thread-safe (and immutable) representation of the mapping of the application domain model to a database. Acts as a factory for org.hibernate.Session instances. The EntityManagerFactory is the JPA equivalent of a SessionFactory and basically, those two converge into the same SessionFactory implementation.`

SessionFactory는 생성하는 데 매우 비쌉니다. 따라서 어플리케이션은 지정된 데이터베이스에서 하나의 연관된 SessionFactory 만 가져야합니다. SessionFactory는 Hibernate가 2차 레벨 캐시, 커넥션풀, 트랜잭션 시스템 통합과 같은 모든 세션에서 사용하는 서비스를 관리한다.  
`A SessionFactory is very expensive to create, so, for any given database, the application should have only one associated SessionFactory. The SessionFactory maintains services that Hibernate uses across all Session(s) such as second level caches, connection pools, transaction system integrations, etc.`

**_Session (org.hibernate.Session)_**  
"작업 단위" [PoEAA](http://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#PoEAA)를 개념적으로 모델링한 싱글 쓰레드이면서, 생명주기가 짧은 객체이다.  
`A single-threaded, short-lived object conceptually modeling a "Unit of Work" PoEAA. In JPA nomenclature, the Session is represented by an EntityManager.`

그 이면에는, Hibernate Session은 JDBC java.sql.Connection을 랩핑하고 org.hibernate.Transaction 인스턴스를 위한 팩토리로 작동한다. 어플리케이션 도메인 모델의 영속화 컨텍스트(Persistence Context) (1차 레벨 캐시)를 유지 관리합니다.  
`Behind the scenes, the Hibernate Session wraps a JDBC java.sql.Connection and acts as a factory for org.hibernate.Transaction instances. It maintains a generally "repeatable read" persistence context (first level cache) of the application domain model.`

**_Transaction (org.hibernate.Transaction)_**  
개별적인 물리적 트랜잭션 경계를 구분하기 위해서 어플리케이션에서 사용되는 싱글 쓰레드이면서, 생명주기가 짦은 객체이다. EntityTransaction은 사용하고 있는 근본적인 트랜잭션 시스템 (JDBC 또는 JTA)에서 어플리케이션을 분리하는 추상화 API로서 역할을 한다.  
`A single-threaded, short-lived object used by the application to demarcate individual physical transaction boundaries. EntityTransaction is the JPA equivalent and both act as an abstraction API to isolate the application from the underlying transaction system in use (JDBC or JTA).`
