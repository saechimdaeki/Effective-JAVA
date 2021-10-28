# 10. Effective Method와 주의사항

## Item 49. 매개변수가 유효한지 검사하라

#### `매개변수가 유효한지 검사하는 기준`

- 상황: 장바구니에 아이템을 추가한다

```java
@RestController
public class ParameterCheckController{
  @Data
  class AddItemForm{
    private long itemId;
    private int count;
  }
  
  @PostMapping(value = "/{userId}/add")
  public ResponseEntity getBasketInfo(@PathVariable long userId,
                                     @RequestBody AddItemForm addItemForm){
    ///---///
  }
}
```

#### `매개변수 검사하는 위치는 반드시 메소드 시작 부분을 권장한다`

```java
/**
* 적립될 포인트를 계산한다
* @param userId 사용자 id
* @param cardId 사용자의 카드 id
* @param orderItemInfoVoList 주문할 아이템의 정보와 수량
* @return
*/
public static double calculateUserPoint(
				long userId,
				long cardId,
				List<OrderItemInfoVo> orderItemInfoVoList){
  	// 유저 등급 조회
  	// 카드 정보 조회
  	// 아이템 품절 여부 조회
}
```

#### `어디서 호출 되는지가 중요하다. 검사 규칙도 TPO가 중요하다`

- 공개된 API일 수록 parameter 검사 기준은 엄격하게 이루어져야 한다. (예측되지 못한 값이 들어올 확률이 그만큼 높음)

- 내부에서 소화될 코드라면 공개된 API만큼은 아닐지라도, 서비스에 영향이 크게 있거나, 여러 이슈들로 인해 예측되지 않은 값이

  들어올 가능성이 있다면 매개변수를 검사하는 것이 좋다

- 이러한 규칙은 모든 메소드에 동일하다

- 과하게 검사를 하는 것이 나쁘지는 않지만, 잘 구현된 검사에는 의도가 담겨있다.

## 정리하자면 item: 매개변수가 유효한지 검사하라

- 제약들을 문서화 하면 좋다( 사실 이것을 지키는 것은 힘들기는 하다)
- 검사하는 기준은 코드에도 TPO를 적용하라
- 검사를 해야 할지 말아야 할지 혼동되는 상황에서는 과하게 검사한느 것도 나쁘지 않다
- 매개변수 검사는 코드의 시작 부분에서 명시적으로 하는 것이 좋다
- Test code를 통해 문서화의 보조 수단으로 삼는것도 좋은 방법이다

## Item 50. 적시에 방어적 복사본을 만들라

####  `필요할 때 방어적 복사본을 만들자`

- 하단의 코드는 원본이 훼손된다

```java
//과거로 가지 않고 가장 가까운 month에 해당되는 Date 객체 response
public Date getNearbyMonth(Date time, int month){
  if(time.getMonth()>month){
    time.setYear(time.getYear()+1);
  }// 이 경우 원본이 틀어진다
  time.setMonth(month);
  return time;
}
```

## 정리하자면 item: 적시에 방어적 복사본을 만들라

- Date는 어쩔 수 없는 상황이 아니라면 이제 쓰지 말자
- 유사하게 원본이 훼손될 수 있는 상황을 방어적 복사하여 방지하라.

## Item 51. 메소드 시그니처를 신중히 설계하라

#### `메소드 이름은 신중히`

- 표준 명명 규칙을 따르자

- 너무 긴이름은 피하자 

  ex) findByIdAndItemNameAndItemTargetSubIdAndCouponGroupIdOrderByCreatedDateDesc

#### `편의 method(ex. Util Class 소속의 method와 같은) 를 너무 많이 만들지 말자`

- 정말 자주 쓰일 때에만 편의 method를 제공해야 한다. (앞서 나가지 말자)
- 확신이 생기지 않는다면 private로 쪼개 놓은 후 차후에 다른 class에서의 니즈가 생길 경우 리팩토링 해도 늦지않다



#### `Parameter 목록은 짧게`

- 4개 이하 권장. 특히 같은 타입 여러 개는 작성자도 혼란스럽게 한다.
- 해결방법
  1. 만든 메소드가 너무 장황하였을 확률이 있다. 쪼개자
  2. Vo(Value Object) 를 사용한다

```java
class UserVo{
  private long id;
  private int age;
  private int footSize;
  private float weight;
  private float height;
}

//...
public static void expandParam(long id, int age, int footSize, int weight, int height){}
public static void voParam(UserVo userVo){}
```

#### `Parameter의 타입은 Interface가 더 났다 (변수도 동일)`

```java
public static void example(){
  //대부분의 상황
  List<String> aList = new ArrayList<>();
  //명확히 ArrayList 로 선언된 것을 알아야 할 경우
  // (LinkedList가 불리한 상황 등)
  ArrayList<String> bList=new ArrayList<>();
}
```

#### `Boolean은 정말 참 거짓일 때만, 나머지는 원소 두 개 짜리 enum`

```java
boolean isFivePin;
...
enum SmartPhoneChargeType{
  FIVE_PIN, LIGHTING,
  C_TYPE; // 나중 추가
}
```

## 정리하자면 item: 메소드 시그니처를 신중히 설계하라

- 메소드 이름은 신중히
- 편의 메소드는 신중히
- Parameter 목록은 짧게
- Parameter type은 Class 보단 Interface
- 2개 값일 때 boolean보단 enum

## Item 52. 다중 정의는 신중히 사용하라

#### `Overroding의 위험함을 피하는 방법`

- 매개변수가 같은 Overroding은 만들지 않는 것을 추천한다.
- 만약 만들어야 한다면 차라리 메소드 이름을 다르게 짓는 것을 보수적으로 권장한다 (ex. From, By 등등으로)

```java
public List<E> sort(ArrayList<E> arrayList){
  //1번 알고리즘
  return arrayList;
}
public List<E> sort(LinkedList<E> arrayList){
  //2번 알고리즘
  return arrayList;
}
public List<E> sortFromArrayList(ArrayList<E> arrayList){
  //1번 알고리즘
  return arrayList;
}
```

## 정리하자면 item: 다중정의는 신중히 사용하라

- 매개변수의 수가 같은 Overroding은 피하는 것이 좋다
- 생성자와 같은 경우는 형변환을 해서라도 헷갈릴 만한 요소를 제거해야 한다 
- 매개변수와 같은 Overroding을 할 상황이라면 차라리 메소드의 이름을 다르게 지어라

## Item 53. 가변인수는 신중히 사용하라

#### `올바르게 가변인수를 사용하는 법`

- 필수 인자는 가변 인수에 포함시키지 마라

  ![image](https://user-images.githubusercontent.com/40031858/139194617-5d75e83d-d468-4791-961a-3319bddd79ce.png)

![image](https://user-images.githubusercontent.com/40031858/139194816-018ce251-5a25-417f-9236-687171e94aa9.png)

## 정리하자면 Item 53. 가변인수는 신중히 사용하라

- 짧게 다룬 이유는 사실 크게 사용할 일은 별로 없다
- 그래도 필수 요소를 매개변수로 사용하는 것과, 유연성의 패턴의 경우 실제 프레임워크에서 자주 사용하는 패턴이니 한번쯤 보는것이 좋다

## Item 54. null이 아닌 빈 컬렉션이나 배열을 반환하라

#### `생각해 봐야 할 점`

- 에러 처리가 힘들다. (받는 쪽에서 리스트의 Null처리를 별도로 해야 한다)
- 조금 더 유연하게 생각하자면 empty List or array는 null이 아니다. 단지 내부가 비어있을 뿐이다
- 성능이 걱정되는가? Collections.emptyList, Collections.emptyList는 불변 객체로 존재한다

```java
public class Collections {
  //생략
  public static final List EMPTY_LIST=new Collections.EmptyList();
}
```

#### `API의 json response는 array? Or null?`

- 개인적으로 항상 []를 null 대신 권장하는 편이다.
- 바로 전 장에서 말했지만 리스트가 없는 것이 아닌 리스트가 비어있다고 생각하자
- 특정 리스트를 반환해야 하는 Get API의 호출은 정상적으로 이루어 졌으나, 그 response가 빈 것일 뿐이다

```java
@GetMapping
public List<String> getNameList(){
  //이름의 리스트가 비었다!
  return Collections.emptyList();
}
```

## 정리하자면 item: null이 아닌 빈 컬렉션이나 배열을 반환하라

- Collection or 배열의 값이 없는 response는 null 대신 empty List or Array를 추천한다.

## Item 55. 옵셔널 반환은 신중히 하라

#### `Optional의 올바른 사용 - 정의`

![image](https://user-images.githubusercontent.com/40031858/139196089-96d07bd3-aeb0-4851-8dca-54c7b58c84b7.png)

```java
Optional<Laptop> optionalLaptop = laptopRepository.findById(id);
Laptop laptop= optionalLaptop.orElse(null); //좋지 못하다.
```

#### `Optional의 올바른 사용 - 기본 값`

- 값이 없을 경우 기본값을 사용하기 위한 용도

```java
//없을 경우 기본값 0 리턴하기로 한 경우
public int maxPositiveIntegerValue(List<Integer> integerList){
  return getMaxInteger(integerList).orElse(0);
}
private OptionalInt getMaxInteger(List<Integer> integerList){
  return integerList.stream().mapToInt(Integer::intValue)
    				.filter(integer -> integer>0)
    				.max();
}
```

#### `Optional의 올바른 사용 - 정의`

![image](https://user-images.githubusercontent.com/40031858/139196542-d44420cb-1764-4942-96e7-addf94c097f1.png)

#### `Optional의 올바른 사용 - 예외`

JpaRepository의 일부

![image](https://user-images.githubusercontent.com/40031858/139197020-25984ddc-0ed8-4b07-bffc-026c27710e6d.png)

명확히 id를 기반으로 찾고, 이 값이 없을 경우 에러라고 가정할 때 원하는 예외를 던지기에 적합하다

#### `Optional<T> 가 득이 아닌 경우`

- Container type( 컬렉션, 배열 등등)
- Bodxing된 기본 타입( OptionalInt 같은 것들을 사용)
- 하단의 코드처럼, Optional을 사용한다면 null을 반환하지 마라(Optional의 취지와 맞지 않다)

```java
public ItemInfo getItemInfo(){
  return itemInfoRepository.findById(1L).orElse(null);
}
```

## 정리하자면 item: 옵셔널 반환은 신중히 하라

- `Optional<T>` 활용 케이스에 대해 다시 한번 점검해보자
- orElse(null)을 사용할 케이스라면, Optional이 답이 아닐 수도 있다.
- Primitive Type의 전용 Optional class를 다시 들여다보자

## Item 56 공개된 API 요소에는 항상 문서화 주석을 작성하라 (정리만있음)

- 주석을 많이 달아서 나쁜 점은 없다
- 하지만 현실적으로 모든 주석을 다는 것이 힘든 일이기도 하다
- 당장은 필요하다고 생각되는 주석부터 한걸음씩 나아가 보도록 하자

