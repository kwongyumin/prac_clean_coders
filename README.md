# 클린 코더스

## SOLID FOUNDATION

### 목차

For further reference, please consider the following sections:

* The Source Code is the Design
* Design Smells
  * Rigidity / Fragility / Immobility
* Needless Complexity
* Code Rot
  * Procedural
* What is OOP ? 


#### Rigidity
정의
* 시스템의 의존성으로 인해 변경하기 어려워지는 것

Rigid 하게 하는 원인 
* 많은 시간이 소요되는 테스트와 빌드
* 전체 리빌드를 유발하는 아주 작은 변화

테스트와 리빌드 시간을 줄이면
* Rigidity 를 줄이고
* 수정이 용이해짐

#### Fragility
* 한 모듈의 수정이 다른 모듈에 영향을 미칠 때 
* ex. 자동차를 SW 로 제어 
  * 라디오 버튼을 수정하는데 자동창문이 영향을 받는 경우
* 해결책
  * 모듈간의 의존성을 제거하는 것

#### Immobility
* 모듈이 쉽게 추출되지 않고 재사용 되지 않는 경우
* ex. 로그인 모듈이 특정 DB 의 schema 를 사용하고 , 
특정 UI skin 을 사용하는 경우 
  * 이 로그인 모듈은 다른 시스템에서 재사용하지 못할 것
  * 이 로그인 모듈은 immobile 하다
* 해결책
  * DB, UI, Framework 등과 결합도를 낮추는 것

#### Viscosity (점성)
* 빌드/ 테스트 같은 필수 오퍼레이션들이 오래 걸려 수행이 어렵다면
그 시스템은 역겨운 것
  * 체크인, 체크아웃, 머지 등은 비용이 크고 역겨움
  * 여러 레이어를 가로질러 의존성을 갖는 것은 역겨움
* 항상 같은 역겨움의 원인
  * Irresponsible tolerance(무책임한 용인)
* 강하게 coupling 된 시스템은 테스트 , 빌드 , 수정을 어렵게 함.
* 해결책
  * dependency 는 유지한 채로 decoupling 하는 것

#### Needless Complexity
* SW 설계의 이슈 
  * 미래를 어떻게 다루나
* 현재 요구사항만 구현 vs 미래를 예측하여 구현 
* 앞으로의 확장을 고려하여 설계
  * 불필요하게 시스템은 복잡해짐
  * 개발자는 현재를 제어할 수 없음
* 불필요한 복잡함 : 강한 커플링
* 해결방법
  * 현재 요구 사항에 집중


## SRP(Single Responsibility Principle)

### 목차
* Responsibility
    * It's about Users/ Roles
    * Collision
    * Fan Out
    * Collocation is Coupling
* SRP
* Solution
* Case Study
* Faking It

#### Responsibility
* 클래스는 하나의 책임을 가져야한다.
* 책임은 무엇인가? 

```java
class EmployeeImpl {
  // calculate Pay()  <- Policy (actor)
  // save ()          <- Architect
  // describeEmployee() : String  <- Operations
  //  ... +
}

```

+ 메서드가 추가된다면 ?... ex. findById
* save / findById - 같은 부류 (DB 접근) 단, 책임이 증가하는 것은 아님.
* 부류는 메소드의 Client 에 의해 결정
* 누가 해당 메소드의 변경을 유발하는 사용자인가

#### Responsibility - It's About Users

* SRP 는 사용자에 관한 것
* 책임
  * SW 의 변경을 요청하는 특정 사용자들에 대해 클래스 /함수가 갖는 것
  * "변경의 근원" 으로 볼 수 있음.
* Employee 클래스의 변경을 요구하는 사용자들 
  * Actor : 서로 다른 Needs , Exception 을 가짐

* User 들을 그들이 수행하는 Role 에 따라 나눠야한다.
* User 가 특정 Role 을 수행할 때 Actor 라고 부른다.
  * 책임은 개인이 아니라 액터와 연관
* Employee 클래스에는 3개의 액터가 있다
  * Policy , Architect , Operations 등

* Responsibility 
  * 특정 액터의 요구사항을 만족시키기 위한 일련의 함수의 집합
* Actor 의 요구사항 변경이 일련의 함수들의 변경의 근원이 된다. 

#### Responsibility - Two Values of SW
* Primary and Secondary Values
* Secondary values of SW is it's behavior
  * 현재의 SW 가 현재 사용자의 현재 요구사항을 만족하는가 ?
* Primary Value of SW
  * 지속적으로 변화하는 요구사항을 수용하는 것 
  * 대부분의 SW 의 경우 현재의 요구사항을 잘 만족하지만 변경하긴 어렵다.

#### Responsibility - Collision
* Primary Value 저하
  * Policy Actors : business rule 의 변경을 필요로 함.
  * Architecture Actors : DB Schema 변경을 필요로 함.
  * Operations Actors : Business rule 변경을 원함.
  * 동일 모듈의 변경 , Merge 충돌 , Source Repo 충돌
  
#### Responsibility - Fan Out
* Employee 는 너무 많은 것을 안다.
  * Business rules 
  * DB 
  * Reports, formatting
* 많은 책임을 갖는다.
* 각각의 책임은 Employees 가 다른 클래스들을 사용하도록 한다.
* Employee 클래스에 거대한 Fan Out 이 존재
* 변경에 민감 ,Employee User 는 더 민감
* Fan Out 을 제한하는 것이 좋다.
  * 좋은 방법은 책임을 최소화 하는 것.

#### Responsibility - Collocation is Coupling
* Operation Actor 가 새로운 리포트 기능을 필요
  * 새로운 리포트 기능도 Employee 클래스에 추가
  * 기존 책임 (Policy , Architecture )에는 변경이 없음에도
  새로운 리포트가 추가되어 Employee 클래스가 변경
  * 새로운 리포트 기능이 Employee 클래스에 추가되면 이 기능을 필요로 하지 않는
  Employee 클래스를 사용하는 모든 클래스들이 다시 컴파일 / 배포 되어야한다.
  * 모든 액터들이 영향을 받게 된다.

#### SRP 
* 모듈은 하나 / 반드시 하나의 변경 사유를 가져야 한다.
* One and only one responsibility
  * 동일한 이유로 변경되어야 하는 것들은 동일 모듈에 , 
  * 다른 이유로 변경되어야 하는 것들은 다른 모듈에

* 시스템 설계
  * Actor 파악에 주의해야함.
  * Actor 들을 serve 하는 책임들을 식별
  * 책임을 모듈에 할당 
    * 각 모듈이 반드시 하나의 책임을 갖도록 유지하면서 
  * 분리의 이유 
    * 다른 이유로 인해 변경되고, 다른 때에 변경되기 때문

* 해결책 
  * 3개의 Actor , 3 개의 Responsibility 하나의 클래스 
  * 어떻게 책임을 분리하나 ? 

#### Solution

#### Solution - Inverted Dependencies
* OOP 에서 이런 의존성을 다루는 전략
  * 클래스를 인터페이스와 클래스로 분리
  * Actor 를 클래스에서 Decouple
    * 모든 Actor 들이 하나의 인터페이스에 coupled
    * 하나의 클래스에 구현되어 구현도 coupled 

#### Solution - Extract Classes 
* 3개의 책임을 분리 하는 방법 : 3개의 클래스로 분리
* 결과
  * Actor 들은 분리된 3개의 클래스에 의존 
  * 3개의 책임에 대한 구현은 분리
  * 하나의 책임의 변경에 다른 책임에 영향 안미침
* 문제점 
  * transitive dependency , Employee 의 개념이 3개의 조각으로 분리

#### 2개의 방법을 잘 합친다면 ? ... 

#### Solution - Facade 
* put all 3 function famillies into a facade class 
* Facade delegates to the 3 different implementations
* 장점
  * 어디에 구현되었는지 찾기 쉽다.
* 단점
  * Actor 들은 여전히 Coupled

### Faking It
* 실제로 한 것은 ...
  * 테스트 작성 -> 통과
  * 동작 함수 생성 -> 설계 전 까지 모든 함수에 대한 리팩토링
  * 완전한 동작을 위하여 모든 동작들이 테스트에 성공하도록 설계 적용
  * 이 후 아키텍쳐 확인 -> 작성된 ㅔㅌ스트가 보여준 설계의 80% 유도
  * 테스트가 각 책임을 식별하는 것을 도움.
  * unit test 가 확보된 후에 무차별적 리팩토링 수행 -> 디자인 향상을 위해서
  * 이런 후에만 각 액터들이 식별되며 , 클래스들을 패키지 단위로 분리
  * 다이어그램 생성 ,..


## OCP(Open Closed Principle)

### 목차
* Open and closed
* Copy / POS Example
* OCP 가 가능한 것 인가?
* A Smelly Design
* Rigidity / Fragility / Immobility Problem
* De-Orderizing the Design
* The Lie
* Two Solutions
* Agile Design Example


#### Copy Example
* Copy Module 을 컴파일 하지않고, Low Level Details를 변경할 수 있다.
* Abstraction and Inversion
  * insert abstract interface between copy and device
  * cause the inverted dependencies

```java
class Copy {}

interface Reader{}
interface Writer{}
// IOC / DI
class KeyboardReader{}
class PunchReader{}
class PrinterReader{}
```

```java

// writePrinter, readKeyBoard 조건이 늘어나는 경우 ..copy 라는 high-level 을 수정해야함.
class Copy {
  public void copy() {
    Integer c;
    while ((c = readKeyBoard()) != null) {
      writePrinter(c);
    }
  }

  private void writePrinter(int c) {}

  private Integer readKeyBoard(int c) {
        return null;
  }
}
```
```java
// 변경이 필요한 부분에 Abstraction 
interface Reader {
    Integer getChar();
}

interface Writer {
  void putChar(Integer c);
}

class Copy {
  public void copy(Reader reader, Writer writer) {
    Integer c;
    while ((c = reader.getChar()) != null) {
      writer.putChar(c);
    }
  }
  
  class KeyboardRedaer implements Reader {
    public Integer getChar() {
      return null;
    }    
  }
  
  class PrinterWriter implements Writer {
    public void putChar(Integer c) {
    }
  }
}
```
#### Copy Example
* 디바이스가 추가되면 해당 디바이스를 담당하는 클래스를 추가
  * Open for extension
* 하지만 copy 로직의 수정은 발생하지 않는다
  * Closed for modification

#### Is This Possible ?
* OCP 를 준수하면 Modification 을 완벽하게 제거할 수 있나?
  * 이론적으로는 가능하다.
  * But 비실용적
* Problems 
  * main partition
  * Crystal ball problem
    * 미래를 볼 수 있는 수정구?.. 
    * 확장을 준수하기 위해, 미래에 필요한 interface 를 모두 설계할 수 없다..

#### A Smelly Design - 비용출력 
```java
class smellyDesignExample {
  public void printReport(ReportPrinter printer) {
    int total = 0;
    int mealExpenses = 0;

    printer.print("Expenses " + getDate() + "\n");

    for(Expense expense : expenses) {
       if (expense.type == BREAKFAST || expense.type == DINNER)
           mealExpenses += expense.amount;
       
       String name = "TILT";
       switch (expense.type) {
         case DINNER:
             name = "Dinner";
             break;
         case BREAKFAST:
             name = "Breadfase";
             break;
         case CAR_RENTAL:
             name = "Car_Rental";
             break;
       }
       // printer.print(String.format("계산식"))
      total += expense.amount;
    }
    // 결과 출력 ...
  }
}
```
* 꽤 복잡. 이해를 해야하며 , 유닛 테스트 존재
* 문제
  * SRP 위반
    * 비즈니스 규칙 + 메시지 생성 + 포맷팅
  * OCP 위반
    * 비즈니스 규칙을 확장하려면 Modify 가 필요
    * 메시지 생성 / 포맷팅을 확장하려면 Modify 가 필요
  * meal type 추가 시 - Rigidity
  * Fan-out Problem - Fragility
    * 가장 먼저 제거해야할 대상 !!
  * 근본적인 비즈니스 변경 가정 - Immobility
    * 고객에게 회계 시스템을 제공하기 위한 모든 기능을 제공하는 기업용 솔루션
    * 새로운 타겟 마켓 등장 
    * 시스템을 컴포넌트로 분리 결정
    * 작은 플러그인 (식대, 특별 점심식대, 자동차 렌트 등) 비용 청구 결정
    * 진짜로 원하는 것만 jar 를 별도 판매 / 배포 
      * but, switch / if 문장들에 의존하고 있어 분리 불가!..


* De-Orderizing the Design
  * https://github.com/unclebob/Episode-10-ExpenseReport.git 
  * https://github.com/msbaek/expense
  
####The Lie
  * 고객이 주말 , Transportation 관련된 새로운 기능을 요구하면 대책이 없다.
  * 아무도 이런 애길 해주지 않아서다. 만일 미리 애기해줬다면 이런 요구사항을
  수용할 수 있도록 Abstraction 을 적용했을 것이다.
  * 내가 알았더라면 OCP 를 준수하다록 했을텐데 , 새로운 기능을 수정없이
  확장할 수 있도록 설계했을 것이란 말이다. 미리 알았다면...
  * OCP 는 앞으로 어떤 확장이 필요할 지 알아야만 제대로 할 수 있다는 말인가 ?
  * 아무리 잘 찾고, 잘 예측해도 고객은 반드시 당신이 준비하지 못한 것에 대한
  기능 추가 / 변경을 요구한다..
  * 미래의 변경으로 부터 보호 받도록 Abstraction 을 적용하여 설계하는 것은 쉽다.
  만일 미래에 어떤 변경이 있을지 알 수 있다면 말이다. 하지만 우린 그런 미래를
  알 수 있는 Crystal ball 이 없다.
  * 고객은 우리가 놓치고 준비하지 않은 부분에 대해서 변경을 요구하는 능력을
  가지고 있다.
  * OCP, ODD 는 당신이 미래를 예측할 수 있을 때만 해당 기능을 보호할 수 있다.

#### Two Solution
  * 어떻게 해야하나?
    * Big Design Up Front 과도한 설계
      * 조심스럽게 고객과 문제 영역을 고찰
      * 고객의 요구사항을 예측하여 도메인 모델 설계
      * OCP 가 가능하도록 도메인 모델에 추상화 적용
      * 변경될 가능성이 있는 모든 것들에 대한 청사진을 얻을 때 까지 헛된 짓 계속..
    * But, 필요치 않는 추상화로 인해 무겁고 복잡한 쓰레기 설계가 만들어짐.
    * 추상화는 유용하고 강력한 만큼 비용도 크다.
    
#### Agile Design
  * 실행 가능한 결정을 내린다.
  * 최대한 빨리 고객의 요구사항을 끌어낼 수 있는 가장 단순한 일을 한다.
  * 고객은 결과물에 대해 요구사항 변경을 시작
  * 어떤 변경이 요구되는지 알게된다.
  * 변화에 대한 가장 좋은 예측은 변화를 경험하는 것
  * 모든 종류의 변경을 완벽하게 예측하고, 점진적으로 추상화 적용
  * 변경을 요구할 때 까지 기다린다.
  * 주 단위 간격의 무엇인가를 Deliver 한다.
  * 고객이 변경을 요구 시 , 코드를 리팩토링한다.

#### Agile Design in Practice
  * BDUF 와 Agile 두 극단 사이에 살고 있는 중.
  * No DUF 또한 피해야한다.
  * 사전 설계는 가치있는 것.
  * 작은 상세까지는 아니지만 기본 모양은 수립되어야한다.
  * 과한 생각은 유지보수 비용이 높은 불필요한 추상화를 만들게된다.
  * 자주 Deilver 하고 , 고객의 요구사항 변화에 기반하여 리팩토링한다.
  * 이럴 때야말로 OCP 가 진가를 발휘한다.
  * 간단한 도메인 모델 없이 진행하면 혼란한 구조 유발!.

#### Reprise
  * 마술이 아니라 공학이다.
  * OCP 를 완벽하게 준수하는 것은 불가능하다. 모든 것을 생각해낼 수 없다.
  * 철저한 규칙을 준수하고 조심해도 결국 고객은 시스템 전반에 걸친
  대대적인 수정이 필요한 변경을 생각해낼 것이다.
  * 변경의 고통을 완전히 제거 하는 것이 아닌, 변경을 최소화 하는 것.
  * OCP 는 시스템 아키텍쳐의 핵심이다.
