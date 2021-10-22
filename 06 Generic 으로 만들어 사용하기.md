# 6 Generic 으로 만들어 사용하기

## Item 26 로 타입은 사용하지 말라

#### `용어 정리`

| 한글용어                 | 영문용어               | 예                                        | 아이템        |
| ------------------------ | ---------------------- | ----------------------------------------- | ------------- |
| 매가변수화 타입          | parameterized type     | `List<String>`                            | 아이템 26     |
| 실제 타입 매개변수       | actual type parameter  | String                                    | 아이템 26     |
| 제네릭 타입              | generic type           | `List<E>`                                 | 아이템 26, 29 |
| 정규 타입 매개변수       | formal type parameter  | E                                         | 아이템 26     |
| 비한정적 와일드카드 타입 | unbounded wildcard typ | `List<?>`                                 | 아이템 26     |
| 로 타입                  | raw type               | List                                      | 아이템 26     |
| 한정적 타입 매개변수     | Bounded type parameter | `<E extends Number>`                      | 아이템 29     |
| 재귀적 타입 한정         | Recursive type bound   | `<T extends Comparable<T>>`               | 아이템 30     |
| 한정적 와일드카드 타입   | bounded wildcard type  | `List<? extends Number>`                  | 아이템 31     |
| 제네릭 메소드            | Generic method         | `static <E> List<E>`<br />`asList(E[] a)` | 아이템 30     |
| 타입 토큰                | type token             | String.class                              | 아이템 33     |

#### `만약 type parameter가 없다면? (비 권장 코드)`

```java
List test = new ArrayList<>();
test.add("no1");
test.add(1);
```

#### `unbounded wildcard type`

```java
private int add(List<?> s1){
  s1.add(1); // error!
  return 1;
}
```

#### `명확하게 타입 추론이 가능하게 하자`

그렇지 않다면 런타임 시 예외가 발생할 수 있다

`List<Object>` 는 명확하게 내가 Object라는 타입을 제시한 것임. 오해 X. 하단과 같이 merge해 버린다면, 다른 타입이 들어왔을 때 런타임 시 언젠가

문제가 생긴다.

```java
static List listMerge(List a, List b){
  List c= new ArrayList();
  c.addAll(a);
  c.addAll(b);
  return c;
}
```

#### `예외적인 사용 예`

Class 리터럴에는 로 raw type을 써야한다

```java
List.class
```

런타임시 제네릭 타입 정보가 지워지기 때문에 다음과 같이 체크

```java
if( a instanceof Set){
  Set<?> s = (Set<?>) a;
  for(object o : s){
    if(o instanceof String){
      //true
    }
  }
}
```



#### `Generic Class / Generic Interface`

```java
List<String> test= new ArrayList<>();
```

List:Generic Interface

```java
public interface List<E> extends Collection<E>{}
```

ArrayList: Generic class

```java
public class ArrayList<E> extends AbstractList<E> implements List<E>,
RandomAccess , Cloneable , java.io.Serializable
```

## 정리하자면 item26 : 로 타입은 사용하지 말라

예외적인 케이스를 제외하면 명확히 타입을 명시하도록 하자

처음에 설명한 용어는 꼭 기억하는것이 좋다.

## Item 27. 비검사 경고를 제거하라

#### `Compiler가 보내는 warning을 제거하라`

Warning이 print된다는 것은 말 그대로 경고를 보내는 것이다. 

![image](https://user-images.githubusercontent.com/40031858/138392815-11ed17c0-152d-4a86-81d0-8909c7ec9338.png)

만약 안전하다고 확신할 수 있으면 @SuppressWarnings("unchecker") 를 통해 경고를 숨기자

## 정리하자면

### 내가 경고를 무시한다면

서버의 치명적인 에러를 감지하지 못할 수 있다

잘못 작성한 코드를 지나칠 수 있다

내가 만든 많은 warning을 통해 , 정작 봐야할 중요한 warning을 볼 수 없다. (특히 의존성을 추가하여 하는 작업일 경우 많음)

동료가 나의 warning을 통해 괴로워 할 수 있다.

## Item 28 배열 대신 리스트를 사용하라

#### `배열 보다는 제네릭`

Runtime Error

```java
Object[] objects=new Long[1];
objects[0] = "test";
```

Compile Error

```java
List<Object> objectList = new ArrayList<Long>();
```

## 정리하자면 item: 배열 대신 리스트를 사용하라

### 추가로 알면 좋은 것

코딩 테스트가 불러온 미신.

Array가 List보다 빠르기 때문에 가능한 Array로 문제를 푸는 것이 좋다 -> 따라서 배열을 쓰는것이 속도에 가장 좋다 (X)

차라리 LinkedList와 ArrayList의 차이점을 잘 알고 쓴느 것이 더 중요하다

코딩 테스트의 환경에서는 Array가 미세하게 더 유리할 수는 있겠지만, 우리가만드는 Product level에서는

ArrayList만으로도 충분한 경우가 대부분이다.



## Item 29. 이왕이면 제네릭 타입으로 만들라

#### `Type parameter Naming Conventions`

- E - Element
- K - Key
- N - Nubmer
- T - Type
- V - Value
- S,U,V etc - 2,3,4th type

Type parameter를 사용한 예

```java
@NoRepositoryBean
public interface JpaRepository<T,ID>
```

#### `Generic Type Example`

```java
public class Calculator<E> {
  private StringBuilder expression;
  public Calculator(){
    expression = new StringBuilder();
  }
  public void add(E e){
    expression.append("+"+e.toString());
  }
  public void minus(E e){
    expression.append("-"+e.toString());
  }
  public String expression(){
    if(expression.charAt(0)=='+'){
      return expression.substring(1);
    }else
      return expression.toString();
  }
}
```

덧셈 뺄셈에 대한 식을 쭉 나열해주는 계산기의 표현화면이 있다고 가정하자. 다만 실수만, 혹은 정수만 받고 싶다. 다른 예외사항은 예제이기에 무시.

## 정리하자면 item: 이왕이면 제네릭 타입으로 만들라

Object를 이용한 직접 형변환하는 코드 대신 제네릭 타입으로 만들어 형변환하는 코드를 없애라

혹시나 기존 형변환 코드가 있다면, 리팩토링 강력 추천

