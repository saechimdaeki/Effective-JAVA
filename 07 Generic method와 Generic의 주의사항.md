

# 7. Generic method와 Generic의 주의사항

## Item 30. 이왕이면 제네릭 메소드로 만들라

#### `Type safe한 Generic method`

```java
List<String> stringList = List.of("T1","T2","T3");
//...

static <E> List<E> of(E e1, E e2, E e3){
  return new ImmutableCollections.ListN<>(e1,e2,e3);
}
// ...
private static UnaryOperator<object> IDENTIFY_FN = (t) -> t;

public static <T> UnaryOperator<T> identifyFunction(){
  return IDENRIFY_FN;
  //object는 제네릭으로 캐스팅 되지 않음, 유형이 다르기 때문임 에러발생
}
```

![image](https://user-images.githubusercontent.com/40031858/138540173-245d0ebe-ec98-4a55-a11e-4de5ff1beaff.png)

#### `Type 한정`

Method의 parameter 타입을, Interface의 Type으로 한정한다

```java
interface Comparable<T>{
  int compare(T o);
}
```

## 정리하자면 item: 이왕이면 제네릭 메소드로 만들라

- Generic 타입과 같이 형변환 해야하는 method보다 generic method가 더 안전하고 , 심지어 사용하기도 쉽다
- 형변환 해야 하는 메소드는 generic하게 만들자

## Item 31. 한정적 와일드카드를 사용해 API 유연성을 높여라

#### `Bounded Wildcard 설명을 위한 예제 코드`

```java
public class Stack<E>{
  public static final int DEFAULT_SIZE = 20;
  private int size;
  private E[] elements;
  public Stack(){ elements = (E[]) new Object[DEFAULT_SIZE]; size=0;}
  public E push(E item){
    elements[++size] = item;
    return item;
  }
  public void pushAll(Iterable<E> src){
    for(E e : src)
      push(e);
  }
}
```

pushAll의 경우 문제를 일으킬수있음..

####  `Bounded Wildcard example`

```java
// 컴파일에러: 불공변이기 때문 (invariant) 자기 타입만 허용
Stack<Number> numberStack = new Stack<>();
Iterable<Integer> integers = List.of(1,2);
numberStack.pushAll(integers);
```

```java
//해결 책은 제네릭 (E) 를 extends한 와일드카드를 입력으로 받겠다
public void pushAll(Iterable<? extends E> src){
  for(E e : src)
    push(e);
}
```

#### `Bounded Wildcard example`

제네릭 상위 클래스인 와일드카드를 Collection으로 받겠다!

```java
public void popAll(Collection<? super E> dst){
  ...
}
```

```java
Stack<Number> numberStack = new Stack<>();
Collection<Object> integers = List.of(1,2);
numberStack.popAll(integers);
//Number의 super인 Object
```

여러개여도 같은 방식으로 사용하면 된다

```java
public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2){
  ...
}
```

자바 8 이상의 코드

```java
Set<Integer> integers = Set.of(1,3,5);
Set<Double> doubles = Set.of(2.0,4.);
Set<Number> numbers = union(integers,doubles);
```

자바 7 까지의 코드

```java
Set<Number> numbers = Union.<Number>union(integers,doubles);
```

#### `Target Typing`

우리는 이미 묵시적으로 target typing을 사용하고 있다

```java
Stack<Number> numberStack = new Stack<>();
```

왼쪽의 타입으로, 오른쪽의 타입을 추론할 수 있다. 이것을 target typing이라 한다.

```java
Set<Integer> integers = Set.of(1,3,5);
Set<Double> doubles = Set.of(2.0,4.);
Set<Number> numbers = union(integers,doubles);
```

자바 7 까지는 명시적 타입 인수를 사용해야 한다.

올바르게 타입을 추론할 수 없기 때문. Target typing이 8부터 지원

```java
Set<Number> numbers = Union.<Number>union(integers,doubles);
```

## 정리하자면 item: 한정적 와일드 카드를 사용해 API 유연성을 높여라

Bounded Wildcard를 알고는 있지만 자세히 들여다 본적은 없을 확률이 높다. 아래표를 다시한번 점검하자

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

## Item 32. 제네릭과 가변인수를 함께 쓸 때는 신중하라

#### `Variadic Arguments(가변 인수)`

Method의 argument의 개수를 클라이언트가 조절할 수 있게 한다.

또한 반드시 `한개의` 가변 인수만을 사용해야 하며 `맨 마지막 Argument` 로 사용해야 한다

```java
static void mergeAll(List<String> ... stringLists){}
```

위의 코드를 풀어 쓰자면 다음과 같은 의미이고

( 이 코드는 컴파일 불가, 컴파일 가능하려면 List[] stringLists={one,two,three};)

```java
static void mergeAll(List<String> one, List<String> two, List<String> three){
  List<String>[] stringLists={one,two,three};
}
```

#### `Heap Pollution`

![image](https://user-images.githubusercontent.com/40031858/138540871-e7ff33c0-a90b-4bc4-9fb4-d4282d517004.png)

```java
List[] test={List.of(1), List.of(2)}; //True
List<Integer>[] test2 = {List.of(1),List.of(2)}; // ERROR
```

안전하기 위해선 제네릭 배열에 아무것도 저장하거나 덮어쓰지 말고, 배열의 참조를 밖으로 노출시키지 말아야 한다.

#### `Remove Warning`

- @SuppressWarnings ( 컴파일 경고 숨기기 )
- @SafeVarangs ( 메소드의 타입 안정성을 보장함 )

#### `예제 해설 (실패케이스)`

![image](https://user-images.githubusercontent.com/40031858/138541044-993f1a8a-8fc4-401b-835e-026245908b7e.png)

## 정리하자면 item: 제네릭과 가변인수를 함께 쓸 때는 신중하라

- 제네릭 배열에 아무것도 저장하거나 덮어쓰지 말고, 배열의 참조를 밖으로 노출시키지 말아야 한다
- 제네릭과 가변인수를 함께 사용할 때에는 궁합이 잘 맞지 않으니 조심하자
- 아니면 이중 리스트 같은 것도 해답이 될 수 있다.

## Item 33. 타입 안정 이종컨테이너를 고려하라

#### `타입 안정 이종 컨테이너`

Key가 wildcard type

```java
public class Favorities{
  private Map<Class<?>, Object> favorites = new HashMap<>();
  public <T> void putFavorite(Class<T> type, T instance){
    favorites.put(Objects.requireNonNull(type),instance);
  }
  public <T> T getFavorite(Class<T> type){
    return type.cast(favorites.get(type));
  }
}
```

Get 할 때 요청받은 타입의 Value를 찾아 cast하여 response함 (Map에서 꺼내올 떄는 Object)



#### `동적 형 변환`

```java
public <T> void putFavorite(Class<T> type, T instance){
  favorites.put(Objects.requireNonNull(type), type.cast(instance));
}
```

```java
Favorites favorites = new Favorites();
Game game = favorites.putFavorite(Game.class , new Game());
```

`HashSet<Integer> `에 string을 넣는 것 같은 문제를 막을 수 있다 

#### `슈퍼 타입 토큰 (ParameterizedTypeReference)`

TypeReference, ParameterizedTypeRefernce 등이 있다.

```java
RestTemplate rt = new RestTemplate();
List<String> test= rt.exchange("http://localhost:8080", HttpMethod.GET,
                              null, new ParameterizedTypeRefernce<List<String>>(){                                
                              }).getBody();
```

## 정리하자면 item: 타입 안정 이종컨테이너를 고려하라

- 타입 안정 이종컨테이너에 대해 알아보았다
- 슈퍼타입 토큰에 대한것은 다루기에 긴내용이니 꼭 여러글들을 확인하는 것을 추천한다.

