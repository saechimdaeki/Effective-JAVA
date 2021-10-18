# 습관적으로 사용할 수 있는 Object의 공통 메소드

## Item10. equals의 일반 규약을 지켜 재정의하라

#### `Equals`

![image](https://user-images.githubusercontent.com/40031858/137695708-6d90d502-3858-4e41-a1e4-d70b96576262.png)

#### `Equals의 자동 생성`

```java
@Override
public boolean equals(Object o){
  if(this==o) return true;
  if(o==null || getClass() != o.getClass()) return false;
  LaptopDto laptopDto = (LaptopDto) o;
  return Objects.equals(modelName, laptopDto.modelname) && 
    Objects.equals(company,laptopDto.company);
}

@override
public int hashCode(){
  return Objects.hash(modelName, company);
}
```

#### `Equals가 만족해야 하는 것`

1. [reflexivity] e.equals(x) => true
2. [symmetry] x.quals(y) => true `then`y.equals(x) => true
3. [transivity] x.equals(y)=> true, y.eqals(z) => true `then` x.equals(z) => true
4. [consistency] x.equals(y) 를 여러번 호출해도 항상 겨로가는 같게
5. [not null] if x is not null , `then` x.equals(null) => false

#### `Equals를 override하지 않는 것이 최선일때`

```java
public class ClassScore{
  private List<Score> scoreList;
}

public class Score{
  private String subjectName;
  private int score;
}
```

####  `Equals의 전형적인 검사 패턴`

1. ==를 통해 input이 자기 자신의 참조인지
2. Instanceof를 통해 input 타입이 명확한지
3. 2를 통해 검사한 객체를 올바른 타입으로 형변환
4. 핵심 필드들이 모두 일치하는지
5. [not null] if x is not null , `then` x.equlas(null) => false

## 정리하자면 item: equals의 일반 규약을 지켜 재정의하라

- TPO에 맞춰 equals를 재정의 할 지 잘 결정하여 규약에 맞춰 override 할지 정하라

### Override시 주의사항

1. 만족해야 하는 조건을 만족시켰는가
2. Equals를 재정의 할 때 hashcode도 재정의 하였는가
3. Equals의 input이 Object인가 (Overriding 하였는가)
4. 핵심 필드들이 모두 일치하는지
5. [not null] if x is not null, `then` x.equals(null) => false

## Item11. equals를 재정의하려거든 hashcode도 함께 재정의하라

| `비교방법` | `설명`                                                       |
| ---------- | ------------------------------------------------------------ |
| ==         | value comapre<br />즉 primitive type일 때는 value compare <br />Reference type일 때는 주소가 같은지 비교 |
| equals()   | 메소드의 의도 : 같은 객체인지<br />Default: == 과 동일<br />Override하여 사용 |
| hashcode() | 논리적으로 같은 객체라면 <br />같은 hashcode를 반환해야 한다 |

#### `잘못된 예`

Example: 안내방송을 위한 TTS 예약 Map

```java
Speaker speaker1 = new Speaker("수업 시작 시간입니다.");
Map<Speaker , LocalTime> localTimeMap = new HashMap<>();
localTimeMap.put(speaker1 , LocalTime.of(9,0));
//수업 시작 시간을 10분 당기기로 하였다.
Speaker speaker2 = new Speaker("수업 시작 시간입니다.");
localTimeMap.put(speaker2, LocalTime.of(8,50));
```

실제로는 안내 방송이 8시 50분, 9시에 두번 울리게 된다

#### `간단한 방식의 hash`

가장 Simple하게 적용해야 한다면

```java
@Override
public int hashCode(){
  int result = message.hashCode();
  return result;
}
```

속도를 고려해야 한다 (Objects의 경우 속도가 아쉽다)

```java
@Override
public int hashCode(){
  return Objects.hash(modelName, company);
}
```

속도가 많이 느려지만 lazy init, or caching을 고려하자. 또한 핵심 필드를 누락하지 말것.

#### `Objects.hash() (내부에서 Arrays.hash 출력)`

```java
public static int hashCode(Object a[]){
  if(a==null) return 0;
  int result = 1;
  for(Object element : a)
    result = 31* result + (element == null ? 0 : elment.hashCode());
  return result;
}
```

#### Lombok을 사용하는 방법도 있다.

```java
@EqualsAndHashCode
public class EqualsAndHashCodeExample {
  private transient int transientVar = 10;
  private String name;
  private double score;
  private String[] tags;
  @EqualsAndHashCode.Exclude private int id;
}
```

전체 코드와 변환되는 것은 Google에 Lombok EqualsAndHashCode를 검색해 Projectlombok(공식 사이트) 의 링크를 확인할 것

특히나 @Data를 사용하고 있다면, Data는 다음과 같은 것들을 포함한다.

@Getter @Setter @RequiredArgsConstructor @ToString @EqualsAndHashCode

#### `Lombok이 실제로는 이런일을 한다`

![image](https://user-images.githubusercontent.com/40031858/137741079-63f35ace-f6dd-4c30-902e-42645f418034.png)

## 정리하자면 item: equals를 재정의하려거든 hashcode도 함께 재정의하라

### equals & hashcode 요약

1.  equals는 필요할 때 적재 적소에 활용하자
2. equals를 override한다면 hashcode의 override는 option이 아닌 필수이다.
3. Lombok을 사용한다면 @Data , @EqualsAndHashCode는 반드시 동작 원리를 이해하도록 한다

## Item 12. toString을 항상 override하라

#### `toString의 default value`

className@16진수 hashcode

#### `toString의 일반 규약`

간결하고 사람이 읽기 쉬운 형태의 유익한 정보

#### `Lombok의 @ToString`

```java
@AllArgsConstructor
@ToString
public class Laptop{
  private String modelName;
  private String company;
}

System.out.println(new Laptop("그램 16인치 ", "삼성"));

//실행 결과 Laptop(modelName=그램 16인치, company=삼성)
```

####  불필요한 변수가 있을경우

```java
@AllArgsConstructor
@ToString(exclue={"modelName"})
public class Laptop{
  private String modelName;
  private String company;
}


//불필요한 변수가있을경우(2) - better
@ToString
public class Laptop{
  @ToString.Exclude private String modelName;
  private String company;
}
```



## 정리하자면 item: toString을 항상 override하라

#### toString() summary

1. 로그를 찍을 일이 있을것 같으면 귀찮아하지 말고 toString을 overriding 하자
2. 전부 다 toString으로 찍지 말고, 필요한 것 위주로 작성하라
3. Lombok은 toString을 만들기 귀찮은 개발자들이 성실하게 toString을 구현하도록 유인할 수 있다
4. Lombok 사용시 주요 옵션은 공식 홈페이지에서 확인할 것

## Item 13. clone 재정의는 주의해서 사용하라

#### `배열 copy`

```java
int[] a={1,2,3,4};
int[] b=a; //Shallow copy;
b= a.clone(); //deep copy
```

#### `Copy시 주의사항`

```java
Laptop[] a={new Laptop("그램 16인치","삼성")};
Laptop[] b= a.clone();
b[0].setComapny("LG");
```

잘 복사한 것 같지만 Object의 reference value를 참조했기 때문에 a[0] ==b[0] 즉 둘이 같은 객체를 가리키고 있음

#### Why not use clone

객체의 복사본을 생성해 반환한다. 복사의 정확한 뜻은 클래스에 따라 다를 수 있고, 일반적인 의도는 다음과 같다

```java
x.clone() != x //참
x.clone.getClass() ==x.getClass() //참
x.clone().equals(x) //일반적으로 참  
```

![image](https://user-images.githubusercontent.com/40031858/137747009-9e88d249-bc6d-4dd2-8a3f-cdafc7b619c3.png)

#### `쉬운 길이 있는데 돌아가지 말자`

conversion Constructor

```java
public Yum(Yum yum){...};
```

conversion Factory

```java
public static Yum newInstance(Yum yum){...};
```

## 정리하자면 item: clone 재정의는 주의해서 사용해라

### Clone 은

- Primitive type의 배열이 아니면 쓰지 말자
- Copy Constructor or Copy Factory method를 활용하라
- Cloneable을 확장하지 마라

## Item 14. Comparable을 구현할지 고려하라

#### `compareTo의 규약 (equals와 비슷)`

객체와 주어진 객체의 순서를 비교한다

이 객체가 주어진 객체보다 작으면 음의 정수, 같으면 0.  크면 양의 정수를 반환.

비교할 수 없을 땐  `ClassCastException`

```java
sgn(x.compareTo(y)) == -sgn(y.compareTo(x))
x.compareTo(y) > 0 && y.comapreTo(z) > 0 then x.compareTo(z) >0
x.comapreTo(y) == 0 then sgn(x.compareTo(z)) == sgn(y.compareTo(z))
//권고는 아니지만 Recommend
x.compareTo(y) == 0 then x.equals.(y) == true
```

복잡해 보이지만 하나씩 뜯어보면 상식적인 이야기이다.



#### Java 7 이후의 compare

과거 (자바 7 미만)

```java
1<2;//정수 비교
Double.compare(1.2, 3.4); //double 비교
Float.compare(1.2f , 3.4f); //실수 비교
```

자바 7 이후

```java
Integer one = 1;
one.compareTo(2);
```

#### `정렬 예제`

```java
public class Person{
  private int age;
  private String name;
  private double height;
}
```

조건: 1순위 나이순 2순위 키순 3순위 이름순 

1. compareTo의 구현

   ```java
   public int compareTo(Person p){
     int result = Integer.compare(age,p.age);
     if(result ==0){
       result = Double.compare(height,p.height);
     }
     if(result==0){
       result = name.compareTo(p.name);
     }
     return result;
   }
   ```

2. compareTo의 또다른 구현 (Comparator 생성 method)

   ```java
   private static final Comparator<Person> COMPARATOR = 
     Comparator.comparingInt(Person::getAge)
     .thenComparingDouble(Person::getHeight)
     .thenComparing(person -> person.getName());
   
   public int compareTo(Person p){
     return COMPARATOR.compare(this,p);
   }
   ```

## 정리하자면 item: Comparable 을 구현할지 고려하라

- 필요하다면 적절하게 Comparable를 구현하여 compareTo의 이점을 누릴 수 있다.
- 하지만 정렬의 기준이 고정적이 아니라면, 다른 방식(ex. Method, service를 통한 조건 별 ordering) 을 고려해 볼 수 있다.
