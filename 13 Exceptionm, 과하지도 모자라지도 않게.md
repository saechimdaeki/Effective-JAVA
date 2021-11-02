# 13. Exception, 과하지도 모자라지도 않게

## Item 69. 예외는 진짜 예외 상황에만 사용하라

- 이런 상태의 코드는 절대 안된다...

```java
try{
  int i=0; 
  while(true)
    range[i++].climb();
}catch(ArrayIndexOutBoundsException e){}
```

## Item 70. 복구할 수 있는 상황에서는 검사 예외를 ,프로그래밍 오류에는 런타임 예외를 사용하라

#### `Exception의 상속 구조`

![image](https://user-images.githubusercontent.com/40031858/139866750-99d246c4-64ae-415e-9a0b-cdc69673f11e.png)

#### `Checked/Unchecked Exception`

|                      | Checked Exception       | Unchecked Exception             |
| -------------------- | ----------------------- | ------------------------------- |
| 처리 여부            | 명시적인 예외 처리 필요 | 명시적인 처리를 강제하진 않는다 |
| Transaction Rollback | Yes                     | No                              |
| Ex                   | FileNotFoundException   | NullPointerException            |

#### `즉 Checked Exception은`

호출하는 쪽에서 복구하리라 여겨질 때 사용한다

```java
public static void main(String[] args){
  MainApplication m =new MainApplication();
  List<String> hello = new ArrayList<>();
  try{
    hello.add("hello");
  }catch(NeedRetryException){
    //retry
  }
}
public List<String> add (String name) throws NeedRetryException{
  throw new NeedRetryException();
}

class NeedRetryException extends Exception{}
```

#### `Runtime Exception`

```java
@Retryable
@Transactional
public Book saveBook(BookForm bookForm){
  if(bookCategoryRepository.findById(bookForm.getBookCategoryId()) == null){
    //bookCategory추가
    bookCategoryRepository.save(BookCategory.from(bookForm));
  }
  Book book = Book.from(bookForm);
  return bookRepository.save(book);
}
```

![image](https://user-images.githubusercontent.com/40031858/139867885-2c007991-6f75-4c83-9cae-a820d68348be.png)

## 정리하자면 item: 복구할 수 있는 상황에서는 검사 예외를 ,프로그래밍 오류에는 런타임 예외를 사용하라

- 호출하는 쪽에서 복구하리라 여겨지는 상황이면 Checked Exception을
- 그 외라면 RuntimeException을 확장한 Unchecked Exception을 이용해 예외를 던지자

## Item 71. 필요 없는 검사 예외 사용은 피하라

- 의미 없는 예외 처리는 하지 말자
- 검사 예외를 던지는 대신 상태 검사 메소드를 활용하라

![image](https://user-images.githubusercontent.com/40031858/139868306-e30fad87-05d1-4dea-a74c-0e2bf84eb285.png)

## Item 72. 표준 예외를 사용하라

| 예외                            | 주요쓰임                                                     |
| ------------------------------- | ------------------------------------------------------------ |
| IllegalArgumentException        | 허용하지 않는 값이 인수로 건네졌을때(null은 따로 NullPointerException으로 처리) |
| IllegalStateException           | 객체가 메소드를 수행하기에 적절하지 않은 상태일 때           |
| NullPointerException            | null을 허용하지 않는 메소드에 null을 건넸을때                |
| IndexOutOfBoundsException       | 인덱스가 범위를 넘어섰을때                                   |
| ConcurrentModificationException | 허용하지 않는 동시 수정이 발견됐을 때                        |
| UnsupportedOperationException   | 호출한 메소드를 지원하지 않을 때                             |

## Item 73. 추상화 수준에 맞는 예외를 던지라

#### `적절한 예외란?`

```java
public class PushPop{
  int size;
  int[] data;
  public PushPop(){
    size = 0;
    data = new int[50]; //for test
  }
  public void push(int input){
    data[size++] = input;
  }
  public int pop(){
    return data[--size];
  }
}
```

## 정리하자면 item: 추상화 수준에 맞는 예외를 던지라

- 쉽게 이야기하면 적절한 예외를 던져야한다

```java
public String subString(String input, int length){
  try{
    return input.substring(length);
  }catch(Exception e){
    if(e instanceof IndexOutOfBoundsException){
      log.error(e.getMessage(),e);
      //checked Exception or Null
    }else if (e instanceof NullPointerException){
      log.error(e.getMessage(),e);
      //checked Exception or Null
    }else{
      return null;
    }
  }
}
```

## Item 74. 메소드가 던지는 모든 예외를 문서화해라

- Checked Exception은 항상 따로따로 선언하고 javadoc의 throws 태그를 사용해서 문서화하자

![image](https://user-images.githubusercontent.com/40031858/139869721-bc854e61-49d3-4ea0-ade0-c5044e8552a2.png)

## Item 75. 예외의 상세 메시지에 실패 관련 정보를 담으라

![image](https://user-images.githubusercontent.com/40031858/139869847-9c7a1c30-1346-4e7a-8dbd-cd18699791e1.png)

## Item 76. 가능한 한 실패 원자적으로 만들라 

- 호출된 메소드가 실패하더라도 해당 객체는 메소드 호출 전 상태를 유지해야한다

```java
public class PushPop{
  int size;
  int[] data;
  public PushPop(){
    size = 0;
    data = new int[50]; //for test
  }
  public void push(int input){
    data[size++] = input;
  }
  public int pop(){
    return data[--size];
  }
}
```



## Item 77. 예외를 무시하지 말라

- Catch블럭에서는 반드시 필요한 일을 할 것
- 만약 예외를 무시하기로 했다면 catch블록 안에 명확한 이유를 주석으로 남기고 예외 변수의 이름도 ignored로 바꾸라

![image](https://user-images.githubusercontent.com/40031858/139871527-fe61120f-cf70-4b0e-a3d4-c261455eec12.png)

