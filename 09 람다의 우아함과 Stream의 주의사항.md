# 9. 람다의 우아함과 Stream의 주의사항

## Item 42. 익명 클래스 보다는 람다를 사용하라

#### `자바8 그리고 람다`

- 자바 8이 되며 작은 함수 객체를 구현하기에 적합한 람다가 도입되었다
- 함수형 인터페이스를 지향하고자 하였다
- 타입을 명시해야 코드를 더 명확할 때를 제외하고는 람다의 모든 매개변수 타임은 생략한다
- 즉 컴파일러가 타입을 알 수 없다고 에러를 낼 때를 제외하고는 항상 생략한다.

#### `익명 함수를 람다로 변환하는 모습`

- 익명함수

  ```java
  List<String> words = List.of("사과","배");
  Collections.sort(words, new Comparator<>(){
    public int compare(String o1, String o2){
      return Integer.compare(o1.length(),o2.length());
    }
  })
  ```

- 람다

  ```java
  Collections.sort(words, Comparator.comparingInt(s->s.length()));
  Collections.sort(words, Comparator.comparingInt(String::length));
  words.sort(Comparator.comparingInt(String::length));
  ```

  

## 정리하자면 item: 익명 클래스 보다는 람다를 사용하라

- 람다에 대해 간단히 알아보았다
- 람다를 사용할 수 있는 자바 버전이면, 타입 추론 또한 가능하기 때문에 매개변수 타입은 가능하면 생략한다
- 익명 함수는 이제 (함수형 인터페이스가 아닌) 타입의 인스턴스를 만들 때만 사용하라

## Item 43. 람다보다는 메소드 참조를 사용하라

#### `메소드 참조와 람다`

| 메소드 참조 유형   | 예                     | 같은 기능을 하는 람다                                  |
| ------------------ | ---------------------- | ------------------------------------------------------ |
| 정적               | Integer::parseInt      | str->Integer.parseInt(str)                             |
| 한정적(인스턴스)   | Instant.now()::isAfter | Instant then = Instant.now();<br />t-> then.isAfter(t) |
| 비한정적(인스턴스) | String::toLowerCase    | str -> str.toLowerCase()                               |
| 클래스 생성자      | TreeMap<K,V>::new      | () -> new TreeMap<K,V>()                               |
| 배열 생성자        | Int[]::new             | len -> new int[len]                                    |

- 핵심 정리

  메소드 참조는 람다의 간단명료한 대안이 될 수 있따. 메소드 참조 쪽이 짧고 명확하다면 메소드 참조를 쓰고 그렇지 않을때만 람다를 사용

- 항상 메소드 참조가 정답은 아니다

  ```java
  //ide 권장
  service.excute(GoshThisClassNameIsHumongous::action);
  //람다, 실제로는 훨씬 간결하다
  service.execute(()->action);
  ```

  

## 정리하자면 item: 람다보다는 메소드 참조를 사용하라

- 람다도 우아하지만, 메소드 참조는 대부분의 경우에서 이보다 더 우아하고, 명확하다
- 처음 사용할 경우 어색할 수도 있고, 람다가 좀 더 명확해 보일 수 있긴 하지만, 메소드 참조를 자주 보지 않았기 때문에 그럴 수 있다
- 적응하고 사용하다 보면 깔끔해지는 코드를 보고 자신도 모르게 사용하는 모습을 볼 수 있다.

## Item 44. 표준 함수형 인터페이스를 사용하라

#### `@FunctionalInterface`

- 람다형으로 선언되었음을 문서로 알린다
- 해당 인터페이스가 추상 메소드를 하나만 가지고 있어야 컴파일 가능
- 다른 의도로 사용되는 것을 막는다.

#### `함수형 인터페이스를 만들어야 할 지 고민해 봐야하는 신호`

- 자주 쓰이며, 이름 자체가 용도를 명확히 설명해준다
- 반드시 따라야 하는 규약이 있다
- 유용한 default method를 제공할 수 있다.

#### `대표적인 기본 함수형 인터페이스`

| 인터페이스          | 함수 시그니처       | 예                  |
| ------------------- | ------------------- | ------------------- |
| `UnaryOperator<T>`  | T apply(T t)        | String::toLowerCase |
| `BinaryOperator<T>` | T apply(T t1, T t2) | BigInteger::add     |
| `Predicate<T>`      | boolean test(T t)   | Collection::isEmpty |
| `Function<T,R>`     | R apply(T t)        | Arrays::asList      |
| `Supplier<T>`       | T get()             | Instant::now        |
| `Consumer<T>`       | void accept(T t)    | System.out::println |

## 정리하자면 item: 표준 함수형 인터페이스를 사용하라

- API를 설계할때 람다를 고려하라
- 43개의 인터페이스를 외우지 말고, 6개의 동작 방식만 이해하고 있다면, 나머지는 유추해 내 사용할 수 있다.

## Item 45. 스트림은 주의해서 사용하라

#### `스트림을 사용해야 하지 말 or 못할 때`

- return (메소드 삐져나가기), break&continue (반복문 종료 or 특정 한번 건너뛰기) 같은 행위가 필요할 때

- 순수 함수를 머리속에 생각하자 EX) 스트림 내부에서 밖의 지역 변수를 수정해야 할 때 - 금지

  ```java
  int a =1;
  List.of("사과","배").stream().filter(str -> {
    if(str.equals("배")){
      a=2;
    }
    return true;
  })
  ```

#### `스트림을 사용해야 할 때`

- 원소의 시퀀스의 일괄 변환 ex. `List<ItemInfo> -> List<String>`
- 시퀀스를 필터링 할 때 ex. `.filter()`
- 시퀀스를 하나의 연산을 통해 결합할 때 (더하기, 연결하기, 최솟값 등) ex. .mapToInt(~).sum()``
- 시퀀스를 컬렉션에 모은다. `.collect(Collectors.toList())`
- 시퀀스에서 특정 조건을 만족하는 원소를 찾는다 ex. `findFirst()`

#### `반복 방식으로 구현한 것`

```java
public static List<Integer> minFive(List<Integer> integerList){
  List<Integer> returnList = new ArrayList<>();
  List<Integer> copiedList = new ArrayList<>(integerList);
  Collections.sort(copiedList);
  int cnt = 0;
  for(int i=0; i<copiedList.size(); i++){
    if(i==5){
      break;
    }
    returnList.add(copiedList.get(i));
  }
  return returnList;
}
```

#### `스트림 방식으로 구현한 것`

```java
public static List<Integer> minFiveExtend(List<Integer> integerList){
  return integerList.stream().sorted().limit(5).collect(Collectors.toList());
}
```

## 정리하자면 item: 스트림은 주의해서 사용하라

- 스트림을 사용할 때, 항상 내부에서 모든 일이 완결성 있게 끝나야 한다는 것을 명심하자
- 항상 스트림이 정답은 아니고, 반복 방식이 좀더 좋은 상황도 있다
- 하지만 스트림을 먼저 시도해 보는 것이 좋다

## Item 46. 스트림에서는 부작용 없는 함수를 사용하라

#### `Stream은 순수 함수여야 한다`

Stream은 순수하여야 한다. 순수 함수란 오직 입력만이 결과에 영향을 준다 (그 자체로 순수성이 유지되어야 한다)

다른 가변 상태를 참조하지 않으며, 함수는 다른 상태를 변경시키지 않는다. 

Stream코드를 사용했을 뿐, 함수형 코드도 아니고, 단순 반복문에 불과하다

```java
public static List<Integer> integerSort(list<Integer> integerList){
  List<Integer> returnList = new ArrayList<>();
  integerList.stream().sorted().forEach(num -> {returnList.add(num);});
  return returnList;
}
```

올바른 코드의 예

```java
public static List<Integer> integerSort(list<Integer> integerList){
	List<Integer> returnList;
  returnList=integerList.stream().sorted().collect(Collectors.toList());
  return returnList;
}
```

forEach 연산은 결과 보고 (ex. print) 할 때만 쓰는 것을 권장한다

#### `toMap의 예제`

```java
@Data
@AllArgsConstructor
class User{
  private Long id;
  private String name;
  private int age;
  private float height;
}
```

```java
/**
* id를 key로, 이름을 value로 가진 map
* @param userList userList
* @return height(5cm 단위), cnt 로 된 map
*/
public static Map<Long,String> getHeightGroup(List<User> userList){
  return userList.stream().collect(Collectors.toMap(User::getId,User::getName));
}
```

#### `groupingBy의 예제`

```java
/**
* id를 key로, 이름을 value로 가진 map
* @param userList userList
* @return height(5cm 단위), cnt 로 된 map
*/
public static Map<Long,String> getHeightGroup(List<User> userList){
	return userList.stream().collect(Collectors.groupingBy(
  user -> ((int) user.getHeight()) /5 *5, Collectors.counting()));
}
```

## 정리하자면 item: 스트림에서는 부작용 없는 함수를 사용하라

- forEach는 결과 보고 할때만 이용하자
- toList, toMap, groupingBy, joining (String 의 joining과 유사) 등에 대하여는 반드시 알고 있어야 한다.



## Item 47. 반환 타입으로는 스트림과 컬렉션이 낫다.

#### `과거의 우리는 Iterator를 사용했다`

```java
List<Integer> list= List.of(1,2,3);
Iterator<Integer> iterator = list.iterator();
while(iterator.hasNext()){
  System.out.println("value = "+ iterator.next());
}
```

Stream은 Iterable을 확장하지 않는다. 즉 for-each로 stream을 반복할 수 없다.

#### `Stream과 Iterable 사이의 어댑터`

```java
public static <E> Iterable<E> iterableOf(Stream<E> stream){
  return stream::iterator;
}

public static <E> Stream<E> streamOf(Iterable<E> iterable){
  return StreamSupport.stream(iterable.spliterator(),false);
}
```

Stream pipeline에서만 사용한다 : response `Stream`

반복문에서만 쓰일 걸 안다 : response `Iterable`



## 정리하자면 item: 반환 타입으로는 스트림과 컬렉션이 낫다

- 컬렉션으로 반환할 수 있는 상황이면 컬렉션으로 반환하자
- 컬렉션에 이미 담아 관리하고 있거나, 하나 더 만들어도 될 정도의 원소 개수가 적다면 ArrayList같은 표준 컬렉션에 담아 반환하라
- 불가능할 때 Stream과 Iterable중 자연스러운 것을 반환하자

## Item 48. 스트림 병렬화는 주의해서 적용하라

#### `parallel은 성공적인 병렬화를 하는 마법의 단어가 아니다`

이 코드가 단일 stream에 비해 무조건 빠르다고 생각한다면, 병렬이라는 단어에 속고있을 수 있다

```java
/**
* 특정 값 이상인 것만 가져와 그 제곱들을 모두 더하는 함수
* @param inputList
* @param minValue 최솟값
* @return 제곱의 합
*/
public Integer parallelExpSum(List<Integer> inputList, int minValue){
  return inputList.stream().parallel().filter(input -> input>=minValue)
    			.mapToInt(input -> input*input).sum();
}
```

#### `ForkJoinPool을 Custom하여 사용한 경우`

- System property를 통해 조정하는 방법도 있지만 권장하지 않는다
- Side effect가 클 수 있다 (전체적인 성능 저하 등)

```java
public Integer parallelExpSum(List<Integer> inputList, int minValue) 
				throws ExecutionException, InterruptedException {
	ForkJoinPool forkJoinPool = new ForkJoinPool(5);
  return forkJoinPool.submit(() ->
                     inputList.stream().parallel().filter(input -> input >= minValue)
                     .mapToInt(input -> input*input).sum()).get();
  
}
```

#### `ForkJoinPool`

![image](https://user-images.githubusercontent.com/40031858/139053064-c8cd4953-2e58-4233-9997-238ba1d70eae.png)

 #### `parallel 은 성공적인 병렬화를 하는 마법의 단어가 아니다`

- Limit과 같은 중간 연산을 사용하면, 병렬화의 비용이 결코 싸지 않다. 반드시 측정하라
- 균등하게 처리되지 않으면 느릴 수 있다
- 외부 API를 호출 할 때도 만능이 아니다
- 병렬 처리를 하려는 자료구조에도 고민하자 (ex ArrayList vs LinkedList)

## 정리하자면 item: 스트림 병렬화는 주의해서 적용하라

- 병렬화는 속도를 빠르게 할 수 있는 매직 넘버가 결코 아니다
- 세 번 강조해도 부족하지 않다. 반드시 측정하고, 확신을 가진 상태에서만 사용하자
- 병렬화에 적합한 상황인지 점검하자. 반드시 측정하자
- 병렬화에 적합한 메소드인지 점검하자 (anyMatch 등 조건에 맞음녀 바로 반환하는 것 )
