# 11. Basic한 프로그래밍 원칙 (1)

## Item 57. 지역변수의 범위를 최소화하라

#### `지역변수는 가장 처음 쓰일 때 선언하기. 선언과 동시에 초기화하기`

- 어떤 코드가 적절하다고 생각하는가?

```java
public void test(){
  int size;
  int start;
  int end;
  int plus;
  List<Integer> numList;
  
  size=30;
  start=1;
  end=100000;
  plus=3;
  numList=new ArrayList<>();
}

------
public void test(){
  int full;
  {
    int block1;
    {
      int block2;
    }
  }
}

```

#### `선언과 동시에 초기화하기`

```java
public void test(){
  int size=30, start=1, end= 10000, plus=3;
  FileOutputStream fos=null;
  try{
    fos=new FileOutputStream("test.txt");
    for(int i=start; i<end; i+=plus){
      fos.write((i+", ").getBytes(StandardCharsets.UTF_8));
    }
  }catch(Exception e){
    System.out.println("파일을 찾을 수 없습니다.");
  }
  //여기서 뭔가 fos를 활용할 일이 있다면?
}
```

#### `메소드를 작게 유지하고 한가지 기능에 집중하기`

```java
//고객 시점
public void order(생략){
  // 1. 카드정보 확인
  // 2. 업체 정보 확인
  // 3. 업체 주문 가능 여부 확인
  // 4. 주문 처리
  // 5. 결제 처리
  // 6. 주문 완료 push 발송
}
public boolean isAvailablePayment(){}
public boolean isOrderAble(){}
public DeliveryOrderDto Deliveryorder(){}
public boolean requestPayment(){}
public boolean push(){}
```

## 정리하자면 item: 지역변수의 범위를 최소화하라

- 처음 쓰일 때 선언하기, 즉 선언과 동시에 초기화하기
- 선언과 동시에 초기화하기
- 메소드를 작게 유지하고 한가지 기능에 집중하기

## Item 58. 전통적인 for문보다는 for-each를 사용하라

#### `전통적인 for 문들`

```java
List<String> stringList=new ArrayList<>();
for(Iterator<String> i = stringList.iterator(); i.hasNext();){
  String s=i.next();
  //todo.
}
for(int i=0; i<stringList.size(); i++){
  stringList.get(i);
  //todo
}
String[] stringArray=(String[])stringList.toArray();
for(int i=0; i<stringArray.length; i++){
  //todo
}
```

#### `향상된 For문의 한계`

```java
List<String> stringList=new ArrayList<>();
for(Iterator<String> i = stringList.iterator(); i.hasNext();){
  String s= i.next();
  if(s.equals("test"))
    t.remove();
}
stringList.removeIf(s->s.eqals("test"));
```

- 파괴적인 필터링
  - 순회하면서 선택된 원소를 제거해야 한다면 반복자의 remove를 호출해야함
  - 자바 8 부터는 Collection의 removeIf를 사용한다
- 변형: 원소의 값 일부 혹은 전체를 교체해야 한다면 리스트의 반복자나 배열의 인덱스 사용 필요
- 병렬 반복: 병렬순회일 경우(58-4) 명시적으로 제어

## 정리하자면 item: 전통적인 for문보다는 for-each를 사용하라

- Iterable을 구현한 for-each가 대부분의 경우에서는 더 나은 선택일 수 있다.
- 하지만 for-each의 경우 변화에 약하다
- 원본을 변화해야 한다면 (CRUD중에 CUD) for문 등을 고려하라.

## Item 59. 라이브러리를 익히고 사용하라

- 바퀴가 있는데, 직접 다시 만들어야 하는 합당한 이유는 무엇인가?
- (개인의견) 바퀴를 커스텀하지 말자. 나온 모델은 그대로 사용하자

## Item 60. 정확한 답이 필요하다면 float와 double은 피하라

- 부동소숫점 연산은 정확하지 않다
- 금융 계산에는 BigDecimal을 사용하거나 자릿수를 바꿔 int or long을 사용

![image](https://user-images.githubusercontent.com/40031858/139562970-f9ea61a6-62c4-4e4b-8bfa-ddb30b48d0d2.png)

## Item 61. 박싱 타입 대신 기본 타입을 사용하라

![image](https://user-images.githubusercontent.com/40031858/139562991-23291ad7-9ebf-4b50-8f28-f89b68e6eac1.png)

`deprecated` . it is rarely appropriate to use this constructor. The static factory valueOf(int) is generally a better choice,

as it is likely to yield significantly better space and time performance

## Item 62. 다른타입이 적절하다면 문자열 사용을 피하라

- 타입의 구분이 명확하지 않은 언어로 시작하였다면, 명확히 타입을 사용하라.
- 열거형 데이터는 enum을 사용하라
- {id} || {type } || {description} 과 같은 혼합 타입을 문자열로 처리하지 말것.

## Item 63. 문자열 연결은 느리니 주의하라

- 문자열 n개를 잇는 시간은 n제곱
- 성능을 포기하고 싶지 않다면 StringBuilder사용
- Lombok의 @ToString은 + 사용



