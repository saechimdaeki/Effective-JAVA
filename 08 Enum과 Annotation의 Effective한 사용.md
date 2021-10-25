# 8. Enum과 Annotation의 Effective한 사용

## Item 34. Int 상수 대신 enum을 사용하라

#### `구별을 위해서는 enum을 사용하자`

책의 말처럼 정수를 열거해 타입을 구분하는건 아무런 이득이 없다

```java
public static final int APPLE = 0;
public static final int ORANGE = 1;
public static final int BANANA = 2;
public static final int GRAPE = 3;
```

과일상점에서 사용하는 사과와 음식으로써의 사과를 구분하는 데에는 다음과 같은 것밖에는 방법이 없을 것이다.

```java
public static final int FRUIT_APPLE = 10;
public static final int FOOD_APPLE = 100;
```

#### `간단한 형태의 enum`

```java
@AllArgsConstructor
@Getter
public enum Fruit {
  APPLE(1000,20,"RED"),
  PEACH(2000,9,"YELLO");
  
  private final int price;
  private final int box;
  private final String color;
  public int boxPrice(){
    return price * box;
  }
}
```

언제 enum을 쓸까? 

1. 타입으로 분류될 수 있는 두개이상의 값이 있다
2. 100% 확장 될 것 같은 (Util과 다른느낌 높은 확장 가능성)

#### `fromString method (자주 쓰이는 pattern)`

```java
private static final Map<String, Fruit> stringToEnum = Stream.of(valueS())
  .collect(Collectors.toMap(Objects::toString, e-> e));
publci static Optional<Fruit> fromString(String symbol){
  return Optional.ofNullable(stringToEnum.get(symbol));
}
```

타 서버에서 불확실성을 가지고 enum이 넘어오거나 (100% 확신할 수 있으면 바로 enum으로 받기도 함)

DB등의 값을 처리할 때 등등 유용할 수 있다.

## 정리하자면 item: int 상수 대신 enum을 사용하라

- Enum화 시킬 수 있는 상황이면 가급적이면 하는 것이 좋습니다
- 또한 외부 type의 Symbol이 불안정하다면, fromString을 통한 방안도 고려해 볼만 하다.



## Item 35. ordinal method대신 instant field를 사용하라

#### `Enum의 Ordinal vs instant Field`

- ordinal() 사용

  ```java
  public enum Ensemble{
    SOLO, DUEL, TRIO, QUARTET;
    public int numberOfMusicians(){
      return ordinal()+1;
    }
  }
  ```

- Instant field 사용

  ```java
  @AllArgsConstructor
  public enum Ensemble{
    SOLO(1), DUEL(2),
    TRIO(3), QUARTET(4),
    TRIPLE_QUARTET(12);
    
    private final int numberOfMusicians;
    public int numberOfMusicians(){ return ordinal() + 1; }
  }
  ```

## 정리하자면 item: ordinal method 대신 instant field를 사용하라

- Enum에선 ordinal() 이라는 enum의 번호를 반환해 주는 메소드가 있다 (실제로 쓸일은 잘 없음)
- 하지만 경우에 따라 넘버링을 bit로 하는 경우도 있고 (1,2,4) 코드를 잘못 건드리는 순간 겉잡을 수 없을수도 있다
- 그러니 인스턴트 필드에 값을 저장해서 쓰자
- Ordinal의 용도는 EnumSet, EnumMap 과 같이 열거 타입 기반의 자료구조를 위함이다.

## Item 36. bit field대신 Enumset을 사용하라

#### `Bit field으로 구현한 style code를 가져오는 Util class`

```java
public class TextStyleUtil{
  public static final int STYLE_BOLD = 1<<0; //1
  public static final int STYLE_ITALIC = 1<<1; //2
  public static final int STYLE_UNDERLINe = 1<<2; //4
  public static final int STYLE_STRIKETHROUGH = 1<<3; //8
  
  public static int getStyleCode(List<String> textStyle){
    ... //조금은 억지지만
  }
}
```



#### `Enumset으로 구현한 style code 가져오기`

```java
@Getter
@AllArgsConstructor
public enum TextStyle{
  BOLD(1), ITALIC(2), UNDERLINE(4), STRIKETHROUGH(8);
  
  private final int code;
  public static int getStyleCode(Set<TextStyle> styles){
    return styles.stream().mapToInt(TextStyle::getCode).sum();
  }
  ...
    int styleCode= TextStyle.getStyleCode(Enumset.of(TextStyle.BOLD,TextStyle.ITALIC));
}
```

## 정리하자면 item: bit field 대신 Enumset을 사용하라

- 대부분의 상황에서 Bitfield를 사용할 이유가 없다
- Enumset을 사용하는 것을 권장한다.



## Item 37. ordinal indexing 대신 EnumMap을 사용하라

#### `예제에 사용할 코드`

```java
@AllArgsConstructor
public class Plant{
  enum LifeCycle {ANNUAL, PERENNIAL , BIENNIAL}
  final String name;
  final LifeCycle lifeCycle;
  
  @Override
  public String toString(){
    return name;
  }
}
```

#### `ordinal()을 사용한 좋지 못한 코드`

요구사항: 식물들을 배열 하나로 관리, 생애 주기로 묶는다

```java
Set<Plant>[] plantsByLifeCycle = (Set<Plant<[]) new Set[Plant.LifeCycle.values().length];

for(int i=0; i<plantsByLifeCycle.length; i++)
  plantsByLifeCycle[i]=new HashSet<>();

for(Plant p: garden)
  plantsByLifeCycle[p.lifeCycle.ordinal()].add(p);
```

## 정리하자면 item: ordinal indexing 대신 EnumMap을 사용하라

- 책의 예제를 꼼꼼히보자
- Stream에 익숙하지 않다면 이번 기회에 익숙해질 기회
- ordinal 직접 사용은 피하자

## Item 38. 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

#### `Interface를 통해 enum 확장하기`

```java
public interface Operation{
  double apply(double x, double y);
}
```

```java
@AllArgsConstructor
public enum BasicOperation implements Operation{
  PLUS("+"){
    @Override
    public double apply(double x, double y){
      return x+y;
    }
  },
  MINUS("-"){
    @Override
    public double apply(double x, double y){
      return x-y;
    }
  };
  private final String symbol;
}
```

#### `Interface를 통해 enum 확장하기(use)`

```java
public static void test(Collection<? extends Operation> opSet, double x, double y){
  for(Operation op: opSet){
    System.out.printf("%f %s %f = %f\n",x,op,y,op.apply(x,y));
  }
}
```

## 정리하자면 item: 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

- enum 을 확장할 수는 없지만, interface를 이용해 같은 효과를 낼 수 있다
- Api가 interface 기반으로 작성되었으면, 확장한 enum의 instance로 대체해 사용할 수 있다(어떤건 +, 어떤건 -)
- 잘 쓰이지는 않긴 하지만 꼼꼼히 챕터 정독 추천( 평소엔 볼일이 없으므로)

## Item 39. 명명 패턴보다 Annotation을 사용하라

#### `명명 패턴`

jpa의 custom method의 경우 또한 postfix가 Impl로 고정되어 있다.

![image](https://user-images.githubusercontent.com/40031858/138684907-6b2b2fa9-f449-4c3c-94ef-cd672ce0d6a6.png)

#### `명명 패턴의 문제`

- 오타에 취약함
- 올바른 프로그램 요소에서만 사용되라는 법이 없음 (원하지 않는 scan으로 인한 error)
- 프로그램 요소를 매개변수로 전달할 방법이 없다.

#### `Annotation은 이를 모두 해결해준다`

Test Code를 위한 예제

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Test{
 
}
```

#### `annotation의 속성`

RetentionPolicy enum

```java
public enum RententionPolicy{
  SOURCE, //컴파일러에 의해 무시
  CLASS, // 런타임 시 무시 (컴파일 시에만 체크)
  RUNTIME // 런타임 시에도 확인
}
```

ElementType enum

```java
public enum ElementType{
  TYPE,
  FIELD,
  METHOD,
  PARAMETER,
  CONSTRUCTOR,
  LOCAL_VARIABLE,
  ANNOTATION_TYPE,
  PACKAGE,
  TYPE_PARAMETER,
  TYPE_USE,
  MODULE
}
```

#### `Marker annotation example`

```java
public class Sample{
  @Test public static void m1(){}
  public static void m2(){}
  @Test public static void m3(){
    throw new RuntimeException("Fail");
  }
  public static void m4(){}
  @Test public static vodi m5(){}
  @Test public static void m7(){
    throw new RuntimeException("Fail");
  }
}
```

#### `Marker annotation을 이용한 test code`

```java
public class RunTest{
  public static void main(String[] args) throws Exception{
    int tests=0;
    int passed = 0;
    Class<?> testClass = Class.forName(args[0]);
    for(Method m : testClass.getDeclaredMethods()){
      if(m.isAnnotationPresent(Test.class)){
        tests++;
        try{
          m.invoke(null);
          passed++;
        }catch (InvocationTargetException wrappedExc){
          Throwable exc = wrappedExc.getCause();
          System.out.println(m+" 실패 "+exc);
        }catch(Exception exc){
          System.out.println(m+" 잘못 사용한 @Test "+exc);
        }
      }
    }
		//결과 출력
  }
}
```

## 정리하자면 item: 명명 패턴보다 Annotation을 사용하라

- 명명 패턴과 마찬가지로 일반 프로그래머가 타입을 직접 정의할일은 거의 없다 (있다면 확장성을 고려한 marker annotation 정도)
- 하지만 자바 프로그래머라면 annotation type의 동작에 대해 이해하고 있어야 하고 이를 잘활용하면 코드 품질이 여러모로 좋아질 수 있다
- 전체 코드를 읽지만 말고 한번 실습하기 권장

## Item 40. @Override 애노테이션을 일관되게 사용하라

#### `@Override annotation의 효능`

- 실제로 Override되지 않은 method에 해당 annotation 사용 시 컴파일 에러!
- Override되었지만, annotation을 달지 않았을때: 정상 실행
- 프로그래머에게 명확하게 해당 method가 override 되었다는 것을 안내해 준다

## 정리하자면 item: @Override 애노테이션을 일관되게 사용하라

- @Override annotation을 사용하지 않아도 되는 경우
- 추상 메소드를 재정의할 때 (이경우에도 달아도 무방하다)

## Item 41. 정의 하려는 것이 타입이라면 마커 인터페이스를 사용하라

#### `Marker interface vs Marker annotation`

Serializable은 아무 내용도 담고 있지 않으며, 단지 직렬화가 가능한 타입인지를 알리는 역할

```java
public interface Serializable{ 
}
```

비슷한 방식으로 선언한 marker annotation

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface SaechimMarker{}
```

## 정리하자면 item: 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

- 마커 인터페이스와 애노테이션은 각각 쓰임이 다르다

