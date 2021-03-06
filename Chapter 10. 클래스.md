# Chapter 10. 클래스

## 클래스 체계

클래스를 정의하는 표준 자바 관례에 따르면

- 변수 목록: static, public 상수, private 변수, private 인스턴스 변수, (public 변수)
- 공개 함수
- 비공개 함수: 자신을 호출하는 공개 함수 직후에 넣음

→ 추상화 단계가 순차적으로 내려감, 프로그램은 신문 기사처럼 읽힘

### 캡슐화

- 변수와 유틸리티 함수는 가능한 공개하지 않는 편이 낫지만 반드시 숨겨야 한다는 법칙은 없다.
- 때로는 변수나 유틸리티 함수를 protected로 선언해 테스트 코드에 접근을 허용하기도 한다.

## 클래스는 작아야 한다!

- 얼마나 작아야 하는가?
    - 클래스가 맡은 **책임**으로 크기를 측정한다.
    - 메서드 수가 작아도 책임이 많으면 안 된다. ex. SuperDashboard 클래스
- 클래스 이름은 해당 클래스 책임을 기술해야 한다.
    - 클래스 이름이 모호하거나 간결한 이름이 떠오르지 않는다면 책임이 너무 많아서이다.
        
        ex. Processor, Manager, Super와 같은 모호한 단어
        
    - 클래스 설명은 if, and, or, but을 사용하지 않고 25단어 내외로 가능해야 한다.

### 단일 책임 원칙

**SRP**(Single Responsibility Principle)

- 클래스나 모듈을 **변경할 이유(책임)가 하나뿐**이어야 한다.
- 객체 지향 설계에서 중요한 개념이다.
- 변경할 이유를 파악하려 애쓰다 보면 코드를 추상화하기도 쉬워진다.
    
    ex. SuperDashboard 클래스 변경 이유
    
    1. 소프트웨어 버전 정보 추적: 소프트웨어를 출시할 때마다 버전 정보가 달라짐
    2. 자바 스윙 컴포넌트 관리: 스윙 코드를 변경할 때마다 버전 번호가 달라짐
    
    → 버전 정보를 다루는 Version 클래스 만들기
    
- 큰 클래스 몇 개가 아니라 **작은 클래스 여럿으로 이뤄진 시스템이 더 바람직**하다.

### 응집도

클래스는 **인스턴스 변수 수가 작아야 한다.**

- 메서드가 변수를 많이 사용할수록 메스드와 클래스는 응집도가 높다.
- 응집도가 높다는 말은 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다는 의미이다.

‘함수를 작게, 매개변수 목록을 짧게'라는 전략을 따르다 보면 때때로 몇몇 메서드만이 사용하는 인스턴스 변수가 아주 많아진다.

- **새로운 클래스로 쪼개야 한다**는 신호
- 응집도가 높아질수록 변수와 메서드를 적절히 분리해 새로운 클래스 두세 개로 쪼개준다.

### 응집도를 유지하면 작은 클래스 여럿이 나온다

ex. 변수가 많은 큰 함수 일부를 작은 함수 하나로 빼내고 싶은데, 빼내려는 코드가 큰 함수에 정의된 변수 넷을 사용한다.

- 변수 네 개를 새 함수에 인수로 념겨야 옳은가? No!
- 네 변수를 클래스 인스턴스 변수로 승격하면 새 함수는 인수가 필요 없다. 함수를 쪼개기 쉬워진다.

하지만!! 클래스가 응집력을 잃게된다.

- 몇몇 함수가 몇몇 변수만 사용한다면 독자적인 클래스로 분리하면 된다.
- 클래스가 응집력을 잃는다면 쪼개라!!!

ex. PrintPrimes 프로그램 리펙터링

- 작은 함수와 클래스로 나눈 후 함수, 클래스. 변수에 의미 있는 이름을 부여
- 프로그램이 길어진 이유
    - 좀 더 길고 서술적인 변수 이름 사용
    - 코드에 주석을 추가하는 수단으로 함수 선언과 클래스 선언을 활용?
    - 가독성을 높이고자 공백 추가 및 형식 맞추기
- 재구현이 아니다. 알고리즘 동작 원리가 동일하다.
    1. 원래 프로그램의 정확한 동작을 검증하는 테스트 슈트 작성
    2. 한 번에 하나씩 수 차례에 걸쳐 조금씩 코드 변경
    3. 코드를 변경할 때마다 테스트를 수행해 원래 프로그램과 동일하게 동작하는지 확인

## 변경하기 쉬운 클래스

대다수 시스템은 지속적인 변경이 가해진다.

- 어떤 변경이든 클래스에 손을 대면 잠정적인 위험이 존재하게 되며 테스트도 다시 해야한다.

ex. Sql 클래스 → SRP 위반

1. 새로운 SQL 문을 지원하려면 반드시 Sql 클래스에 손대야 한다.
2. 기존 SQL 문 하나를 수정할 때도 반드시 Sql 클래스에 손대야 한다.

```java
public class Sql {
	public Sql(String table, Column[] columns)
	public String create()
	public String insert(Object[] fields)
	public String select(Column column, String pattern)
	// ...
}
```

ex. Sql 클래스 리펙터링 → 공개 인터페이스를 각각 Sql 클래스에서 파생하는 클래스로 만들기

1. SRP 지원
2. OCP 지원: 클래스 확장에 개방적이고 수정에 폐쇄적이어야 한다는 원칙
    1. 파생 클래스를 생성하는 방식 → 새 기능에 개방적, 다른 클래스를 닫아놓는 방식으로 수정에 폐쇄적
    2. 그저 UpdateSql 클래스를 끼워 넣으면 됨, 기존 클래스를 변경할 필요가 없음

```java
abstract public class Sql {
	public Sql(String table, Column[] columns)
	abstract public String generate();
}

public class CreateSql extends Sql {
	public CreateSql(String table, Column[] columns)
	@Override public String generate()
}

public class InsertSql extends Sql {
	public InsertSql(String table, Column[] columns, Object[] fields)
	@Override public String generate()
	private String valuesList(Object[] fields, final Colunmn[] columns)
}

public class SelectSql extends Sql {
	public SelectSql(String table, Column[] columns)
	@Override public String generate()
}
// ...
```

→ 새 기능을 수정하거나 기존 기능을 변경할 때 건드릴 코드가 최소인 시스템 구조가 바람직하다.

→ 이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장할 뿐 기존 코드를 변경하지는 않는다.

### 변경으로부터 격리

요구사항은 변하기 때문에 코드도 변한다.

- 객체 지향 프로그래밍: 구체적인 concrete 클래스, 추상 abstract 클래스 존재
    - 상세한 구현에 의존하는 클라이언트 클래스는 구현이 바뀌면 위험에 빠진다.
    - **인터페이스와 추상 클래스를 사용해 구현이 미치는 영향을 격리**한다.
- 상세한 구현에 의존하는 코드는 테스트가 어렵다.
    
    ex. Portfolio 클래스
    
    - 외부 TokyoStockExchange API를 사용해 포트폴리오 값을 계산
    
    → 테스트 코드가 시세 변화에 영향을 받음
    
    ex. Portfolio 클래스 리펙터링
    
    - TokyoStockExchange API를 직접 호출하지 않고 StockExchange 인터페이스 생성
    - StockExchange 인터페이스를 구현한 TokyoStockExchange 클래스 구현
    - 생성자에서 StockExchange를 인수로 받음
    
    → 구현 클래스(TokyoStockExchange)가 아닌 StockExchange **인터페이스에 의존**
    
    → 테스트용 클래스는 StockExchange 인터페이스를 구현하며 고정된 주가를 반환
    
- 시스템 결합도를 낮추면 유연성과 재사용성이 높아지고 변경으로부터 격리된다.
- 결합도를 줄이면 **DIP**(Dependency Inversion Principle)를 따르는 클래스가 나온다.
    - DIP: 클래스가 상세한 구현이 아니라 추상화에 의존해야 한다는 클래스 설계 원칙
