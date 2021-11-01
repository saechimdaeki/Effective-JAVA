# 12 Basic한 프로그래밍 원칙(2)

## Item 64. 객체는 인터페이스를 사용해 참조하라

#### `Interface better than Class`

아이템 51 메소드의 시그니처를 신중하게 설계하라. 중 parameter type으로 class대신 interface를 사용하라 의 주제로 다룬 내용의 확장

| Interface                  | Class                                                        |
| -------------------------- | ------------------------------------------------------------ |
| 유연(Flexible)하다         | 형태가 고정적이다                                            |
| 어떤 구현체일지 알 수 없다 | 클래스가 명확하기 때문에 내부가 어떻게 이루어져<br />있는지 명확히 알 수 있다. |

#### `Interface better than Class`

```java
public class EmailUtil{
  private static final Pattern EMAIL = Pattern.compile(
  				"[a-zA-Z0-9.-]\\\\.[a-zA-Z]{2,6}$");
  
  static boolean isEmailValid(String s){
    return EMAIL.matcher(s).matches();
  }
  //들어온 문자열 중 이메일만 필터링해서 response한다
  public static List<String> filterEmail(List<String> stringList){
		return stringList.stream().filter(EmailUtil::isEmailValid)
      			.collect(Collectors.toList());
  }
  public static ArrayList<String> filterEmail(ArrayList<String> stringArrayList){
    return stringArrayList.stream().filter(EmailUtil::isEmailValid)
      		.collect(Collectors.toCollection(ArrayList::new));
  }
}
```

#### `적합한 Interface가 없는 경우`

- String, BigInteger와 같은 값을 나타내는 Class
- Class기반으로 작성된 framework가 제공하는 객체들 (ex. OutputStream)
- Interface에 없는 특별한 메소드를 제공하는 클래스들 (ex. PriorityQueue는 Queue에는 없는 Comparator 메서드를 제공)

## 정리하자면 item: 객체는 인터페이스를 사용해 참조하라

- 가능하다면 객체는 인터페이스를 통해 참조하도록 하자
- 적합한 인터페이스가 없다면 클래스의 계층구조 중 필요한 기능을 만족하는 가장 덜 구체적인 클래스를 타입으로 사용하는 것을 추천한다.

## Item 65. 리플렉션보다는 인터페이스를 사용하라

#### `Reflection이란?`

- 실행중인 Java 프로그램이 자체적으로 검사하거나, 수정할 수 있는 기능.
- 예를 들어 Class가 모든 member의 이름을 가져와 출력할 수 있다.
- 즉 Class의 Structure를 개발자가 확인할 수 있고 값을 가져오거나 메소드를 호출할 수 있음 (Spring,Hibernate,Jackson 등에서 사용)

```java
public static void main(String[] args){
  List<String> a = new ArrayList<>();
  dumpMethod(a);
}
public static void dumpMethod(Object arg){
  Class c = arg.getClass();
  Method m[] = c.getDeclaredMethods();
  for(Method method : m){
    System.out.println(method.toString());
  }
}
```

![image](https://user-images.githubusercontent.com/40031858/139673256-75ccffa9-4eea-4e67-8ef5-85bc1b20c853.png)

#### `Reflection의 단점`

- 컴파일타임 타입 검사가 주는 이점을 누릴 수 없다.
- 코드가 지저분해지고, 장황해진다
- 성능이 떨어진다

#### `Reflection을 사용할 때`

- 컴파일타임에 이용할 수 없는 클래스를 사용해야 하는 경우
- 인스턴스 생성에만 쓰고, 인스턴스는 인터페이스나, 상위클래스로 참조해 사용하자

## 정리하자면 item: 리플렉션보다는 인터페이스를 사용하라

- 리플렉션은 특수 시스템을 개발할 때 강력한 기능이지만, 단점이 많다
- 컴파일 시점에 알 수 없는 클래스를 사용하는 프로그램을 작성하면 리플렉션
- 단 이때도 객체 생성시에만 사용하자

## Item 66. 네이티브 메소드는 신중히 사용하라

- JNI(Java Native Interface)를 통해 Native Method를 호출하는 것
- C, C++ 같은 Native programing language로 작성된 것

1. 레지스트리 같은 플랫폼 특화 기능을 사용해야 할 경우
2. 네이티브 코드로 작성된 기존 라이브러리를 사용할 경우
3. ~~성능 개선을 목적으로 성능에 결정적 영향을 주는 부분을 분리할 경우~~ (현재 JVM에는 거의 해당하지 않음)

## Item 67. 최적화는 신중히 하라

#### `빠른 프로그램 보다는 좋은 프로그램을 작성하라`

- 구현의 문제는 최적화로 해결할 수 있지만, 최적화를 위해 아키텍쳐를 손상시킨다면 돌이킬 수 없다
- 선 Architecture, 후 최적화

#### `성능을 제한하는 설계를 피하라`

- 컴포넌트간/ 외부 시스템과 통신하는 경우 변경이 어렵거나, 불가능에 가깝다

## 정리하자면 item: 최적화는 신중히 하라

- 빠른 프로그램 < 좋은 프로그램

## Item 68. 일반적으로 통용되는 명명 규칙을 따르라

#### `Package 명명 규칙`

- 외부에 사용된다면 조직의 인터넷 도메인 역순 ex) com.google

- 각 요소는 8글자 이하의 짧은 단어 사용 ex) GlobalPositioningSystem -> gps등

- 필요할 경우 계층을 나누어서 ex) deliveryDriver 대신 delivery.driver

  (구글의 경우 합성어를 사용해야 할 상황이라면 _(underline)을 사용하나, 이건 구글의 guide, 잘 사용하지는 않음)

#### `Class & Interface 명명 규칙`

- 하나 이상의 단어 (passenger, deliveryDriver)
- 널리 통용되는 줄임말을 제외하면 줄여 쓰지 않는다
- 약자의 경우 첫글자 대문자 or 전체를 대문자 (보통은 첫문자 대글자)

#### `Method & Field의 명명규칙`

- 첫글자를 소문자로
- 상수 필드는 전체 대문자 (static final)
- 지역변수는 약어를 조금 더 적극적으로 사용해도 무방함

#### `문법 규칙(1)`

- 단순명사 Or 명사구
- 객체를 생성할 수 없는 클래스의 이름은 복수로 (PatternUtils)
- Interface는 able or ible로 끝나는 형용사
- 동작을 수행하는 메소드는 동사나 (목적어를 포함한 동사구)
- ex) append, add
- Boolean의 경우는 is가 보통이나 간혹 has도 있다.

#### `문법 규칙(2)`

- 반환 타입이 Boolean, void가 아니거나, 해당 인스턴스의 속성을 반환한다면 get~을 보통 사용한다
- 객체의 타입을 바꾼다면 to~(toArray, toList, toString)
- 정적 팩토리 메소드는 (from,to, valueOf )

## 정리하자면 item: 일반적으로 통용되는 명명규칙을 따르라

- 일반적인 명명규칙에 적응한다면 대부분의 경우에서 이름을 짓는것이 한결 수월해질 것이라고 생각합니다
- 대부분의 개발자가 지키고 있는 규약같은 것이니 적응이 되면 의사소통하기 편함
- 또한 우리가 영어가 모국어가 아니기 때문에 적응에 시간이 걸릴 수 있다.







