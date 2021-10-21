# 5 Interface와 설계 원칙

## Item 20 . Abstract class 보다는 interface를 우선하라

#### `Interface` 를 들어가며

- Interface를 정말 잘 이해하고 있다고 생각하는가
- Interface를 진심으로 공감하고 있는가
- 현업에서의 상황 가정

![image](https://user-images.githubusercontent.com/40031858/138197842-c28444a7-6118-461a-b36e-62d6ee2dff61.png)



### extends는 하나만 가능!

```java
public class Sub extends Super implements Serializable, Cloneable {}
public interface Sub extends Serializable,Cloneable{}
```



#### `Example. 배달 앱을 상상해보자`

```java
public class Restaurant extends Delivery{}
```

포장 주문의 필요성이 생김, 하지만 한 class밖에는 상속이 불가능한걸? Interface로 만들면 되지!

```java
public class Restaurant implements Deliveriable, Packable{}
```

하지만 나는 실체가 있는 method가 필요했다고! 다들 그래서 Java 8 에서 default method가 두두등장!

```java
public interface Packable{
  default void packOrder(){
    System.out.println("포장 주문이 들어왔습니다.");
  }
}
```



#### `Default method의 Diamond 문제 주의`

##### 각각 order가 default method 일 때

![image](https://user-images.githubusercontent.com/40031858/138198849-f6a1ab49-f84f-4ae6-a41a-48bb18183149.png)

##### 만약 method가 구현되어 있지 않다면

![image](https://user-images.githubusercontent.com/40031858/138198921-f99d8b89-1b4c-448c-8921-6d66e6e709b4.png)

#### `Skeletal implementation (추상 골격 구현)`

1. Interface로 뼈대를 만들고
2. Abstact class 로 필요한 구현을 모두 마친 후
3. Subclass로 마무리하는것

#### 주의 사항

Object method (equals, toString ...)는 default 메소드로 제공하면 안된다.

## 정리하자면 item: 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라

- 머리속에서 항상 Interface를 먼저 떠올리자
- 뼈대를 만들 때에는 default method가 대안이 될 수 있다.

## Item 21. 인터페이스는 구현하는 쪽을 생각해 설계하라

#### `역지사지로 생각하자`

- Test code를 반드시 짜자 (여러가지 발생할 수 있는 case들에 대해서)
- Interface를 implements한 class를 만들어서도 테스트를 해보자
- 생각해보자 과연 당신의 Next Generation은 당신이 의도한 대로 사용하고 확장하여 사용하겠는가?

## Item 22. 인터페이스는 타입을 정의하는 용도로만 사용하라

#### `Constant static final 은 anti pattern`

클래스 내부에서 사용하는 상수는 내부 구현에 해당된다. 오히려 사용자에게 혼란을 줄 수 있다.

차라리 클래스에 static final로 추가하는 것이 더 낫다

```java
public class OrderService{
  public static final double SECOND_TO_MIN = 60.;
}
```

만약 여러 곳에서 사용해야 할 값이라면 util class!

```java
public class TimeConvertUtil{
  public static final double SECOND_TO_MIN =60.;
  public static double secondToMin(double second){
    return second/SECOND_TOM_MIN;
  }
}
```

#### `static import`

```java
import example.chapter4.TimeConvertUtil;
...
  TimeCOnvertUtil.SECOND_TO_MIN
import static example.chapter4.TimeConvertUtil.SECOND_TO_MIN;
SECOND_TO_MIN
```

두가지 방법이 있는데, 전자의 방법을 추천한다. 

비슷한 이름의 상수값이(enum도 똑같다) import될 경우 혼란을 일으킬 수 있고, 심지어 같은 값이 있는 경우도 아주 가끔이지만 있다.

코드를 줄이기 위해 후자를 사용하려는 의도가 많지만, 언급한 것처럼 혼란을 일으킬 수 있으니 이를 조심하자



## 정리하자면 item: 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라

- 인터페이스를 타입을 정리하는 용도로만 쓰고, 상수 공개용으로 사용하지 마라.
- 필요하다면 Class에 담아라
- Properties에 값을 선언해 두고 Injection하는 것도 방법일 수 있다.

## Item23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라

#### `Shape로 태그를 달아 생성자에 따라 변화하는 하이브리드`

```java
public class Figure{
  enum Shape {RECTANGLE, CIRCLE};
  final Shape shape;
  double length;
  double width;
  double radius;
  
  Figure(double radius){
    shape = Shape.CIRCLE;
    this.radius=radius;
  }
  Figure(double length, double width){
    shape= Shape.RECTANGLE;
    this.length=length;
    this.width=width;
  }
  double area(){
    switch(shape){
      case RECTANGLE : return length * width;
      case CIRCLE: return MAth.PI * (radius*radius);
      default: throw new AssertionError(shape);
    }
  }
}
```

- 불필요한 필드 현재 instance type으로는 의미가 불분명

#### `Type로 태그를 달아 생성자에 따라 변화하는 배달 앱`

클래스 계층 구조를 만들기 위한 예제 코드 (tag)

```java
public class User{
  enum Type{RESTAURANT, CUSTOMER, DELIVERY_PERSON}
  final Type type;
  String location;
  double latitude;
  double longitude;
  boolean order(String info){
    // 배달원일 경우 가까운 위치로 주문 설정
    // 레스토랑일 경우 주문 자동 수락
    // 고객일 경우 카드로 결제 후 주문 수락
  }
}
```

#### `Class 계층 구조로 변환`

Tag 버전에 비해 훨씬 더 명료해졌다

```java
abstract class User{
  abstract boolean order(String info);
}
class Customer extends User{
  @Override
  boolean order(String info){
    //카드로 결제 후 주문 수락
    return false;
  }
}
class DeliveryPerson extends User{
  @Override
  boolean order(String info){
    // 배달 수락 버튼 누를 시 승락
    return false;
  }
}
```

## 정리하자면 item: 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라

- 태그 달린 클래스를 써야 하는 상황은 거의 없다
- 혹시나 이미 오래된 레거시 시스템 등의 이유를 통해 태그 달린 클래스가 먼저 떠오른다면 계층 구조로 대체하는 방법을 생각해보자
- 이미 태그가 달려 있다면, 계층 구조로 리팩토링 하는것을 생각해보자



## Item 24. 멤버 클래스는 되도록 static으로 만들라

#### `Nested class - Member class`

Nested되어 있는 member class가 독립적으로 존재할 수 없으며 바깥 instance없이는 생성할 수 없어야 함

```java
@Data
public class User{
  private String name;
  private Address address;
  @Data
  public class Address{
    String zipcode;
  }
  public String getUserName{
    //의미가 있는 메소드가 아님
    //접근 범위 설명 위해 추가함
    return name;
  }
}
User user = new User();
user.new Address();
```

#### `Nested class - static member class`

Nested되어 있는 member class가 독립적으로 존재할 수 있음 (책에서 권장 방식)

```java
public class Custmoer{
  private int age;
  private Address address;
  public String printBarCode(){
    return address.fullAddress+address.zipcode;
  }
  private static class Address{
    private String fullAddress;
    private String zipcode;
  }
}
```

#### `Nested class - Anonymous class `

Nested되어 있는 member class가 독립적으로 존재할 수 있음

```java
public interface MyName{
  public int getAge();
}
... //실행 code
MyName myName = new MyName(){
  private int age;
  @Override
  public int getAge(){
    return age;
  };
};
```

```java
public abstract class MyName{
  public int getAge(){
    return 0;
  }
}

//OR
public class MyNmae{
  public int getAge(){
    return 0;
  }
}
//실행 코드
MyName myName = new MyName(){
  private int age;
  @Override
  public int getAge(){
    return age;
  };
};
```

#### `Nested class - Local class`

Local variable을 선언할 수 있는 곳에 선언하여 사용

EX) method body

```java
public void getName(){
  class Name{
    public int age;
  }
}
```

## 정리하자면 Item: 멤버 클래스는 되도록 static으로 만들라

##### Nested class

- Method 밖에서 사용할것이다 -member class
- 그 중 member class 가 바깥 instance를 참고한다 -> non static 그외 -> static
- 딱 한곳에서 사용 && 사전 struct가 있다 -> Anonymous class 그외 -> local class

## Item 25 . 탑레벨 클래스는 한 파일에 하나만 담으라

#### `Top level class(interface)`

A top level class is a class that is not a nested class -공식 문서

Top level class(interface) 는 한 파일에 하나만 담아라

public class가 파일에 하나만 가능하다고 해서 public이 아닌 top level class를 여러개 한 파일에 넣지 말자

써야 한다면? Nested class(중첩 클래스) 로 사용하라

## 정리하자면 item: 톱 레벨 클래스는 한 파일에 하나만 담으라

컴파일러가 한 클래스에 대한 여러 정의를 만드는 것을 막을 수 있다

즉 컴파일러가 어느 순서로 컴파일을 하던 일관성이 유지된다

