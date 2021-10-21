# Effective-JAVA

### 1.객체의 생성
#### [Item1. Constructor 대신 Static Factory Method를 고려하라.](https://github.com/saechimdaeki/Effective-JAVA/blob/main/01%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%EC%83%9D%EC%84%B1.md#item1-constructor-%EB%8C%80%EC%8B%A0-static-factory-method%EB%A5%BC-%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC)
#### [Item2. 많은 parameter가 있는 Constructor는 Builder를 고려하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/01%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%EC%83%9D%EC%84%B1.md#item2-%EB%A7%8E%EC%9D%80-parameter%EA%B0%80-%EC%9E%88%EB%8A%94-constructor%EB%8A%94-builder%EB%A5%BC-%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC)
#### [Item3. private constructor나 enum Type으로 Singleton임을 보장하라.](https://github.com/saechimdaeki/Effective-JAVA/blob/main/01%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%EC%83%9D%EC%84%B1.md#item3-private-constructor%EB%82%98-enum-type%EC%9C%BC%EB%A1%9C-singleton%EC%9E%84%EC%9D%84-%EB%B3%B4%EC%9E%A5%ED%95%98%EB%9D%BC)
#### [Item4. Instance화를 막으려면 private constructor를 사용하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/01%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%EC%83%9D%EC%84%B1.md#item4-instance%ED%99%94%EB%A5%BC-%EB%A7%89%EC%9C%BC%EB%A0%A4%EB%A9%B4-private-constructor%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)
#### [Item5. Resource를 직접 명시하지 말고 Dependency Injection을 사용하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/01%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%EC%83%9D%EC%84%B1.md#item5-resource%EB%A5%BC-%EC%A7%81%EC%A0%91-%EB%AA%85%EC%8B%9C%ED%95%98%EC%A7%80-%EB%A7%90%EA%B3%A0-dependency-injection%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)


### 2.객체의 파괴
#### [Item6. 불필요한 객체 생성 금지](https://github.com/saechimdaeki/Effective-JAVA/blob/main/02%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%ED%8C%8C%EA%B4%B4.md#item6-%EB%B6%88%ED%95%84%EC%9A%94%ED%95%9C-%EA%B0%9D%EC%B2%B4-%EC%83%9D%EC%84%B1-%EA%B8%88%EC%A7%80)
#### [Item7 . 다 쓴 객체 참조를 해제해라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/02%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%ED%8C%8C%EA%B4%B4.md#item-7--%EB%8B%A4-%EC%93%B4-%EA%B0%9D%EC%B2%B4-%EC%B0%B8%EC%A1%B0%EB%A5%BC-%ED%95%B4%EC%A0%9C%ED%95%B4%EB%9D%BC)
#### [Item 8. finalizer, cleaner를 피하라.](https://github.com/saechimdaeki/Effective-JAVA/blob/main/02%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%ED%8C%8C%EA%B4%B4.md#item-8-finalizer-cleaner%EB%A5%BC-%ED%94%BC%ED%95%98%EB%9D%BC)
#### [Item9. Try-finally 대신 try-with-resources](https://github.com/saechimdaeki/Effective-JAVA/blob/main/02%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%ED%8C%8C%EA%B4%B4.md#item9-try-finally-%EB%8C%80%EC%8B%A0-try-with-resources)

### 3.습관적으로 사용할 수 있는 Object의 공통 메서드
#### [Item10. equals의 일반 규약을 지켜 재정의하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/03%20%EC%8A%B5%EA%B4%80%EC%A0%81%EC%9C%BC%EB%A1%9C%20%EC%82%AC%EC%9A%A9%ED%95%A0%20%EC%88%98%20%EC%9E%88%EB%8A%94%20object%EC%9D%98%20%EA%B3%B5%ED%86%B5%20%EB%A9%94%EC%86%8C%EB%93%9C.md#item10-equals%EC%9D%98-%EC%9D%BC%EB%B0%98-%EA%B7%9C%EC%95%BD%EC%9D%84-%EC%A7%80%EC%BC%9C-%EC%9E%AC%EC%A0%95%EC%9D%98%ED%95%98%EB%9D%BC)
#### [Item 12. toString을 항상 override하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/03%20%EC%8A%B5%EA%B4%80%EC%A0%81%EC%9C%BC%EB%A1%9C%20%EC%82%AC%EC%9A%A9%ED%95%A0%20%EC%88%98%20%EC%9E%88%EB%8A%94%20object%EC%9D%98%20%EA%B3%B5%ED%86%B5%20%EB%A9%94%EC%86%8C%EB%93%9C.md#item-12-tostring%EC%9D%84-%ED%95%AD%EC%83%81-override%ED%95%98%EB%9D%BC)
#### [Item 13. clone 재정의는 주의해서 사용하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/03%20%EC%8A%B5%EA%B4%80%EC%A0%81%EC%9C%BC%EB%A1%9C%20%EC%82%AC%EC%9A%A9%ED%95%A0%20%EC%88%98%20%EC%9E%88%EB%8A%94%20object%EC%9D%98%20%EA%B3%B5%ED%86%B5%20%EB%A9%94%EC%86%8C%EB%93%9C.md#item-13-clone-%EC%9E%AC%EC%A0%95%EC%9D%98%EB%8A%94-%EC%A3%BC%EC%9D%98%ED%95%B4%EC%84%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)

#### [Item 14. Comparable을 구현할지 고려하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/03%20%EC%8A%B5%EA%B4%80%EC%A0%81%EC%9C%BC%EB%A1%9C%20%EC%82%AC%EC%9A%A9%ED%95%A0%20%EC%88%98%20%EC%9E%88%EB%8A%94%20object%EC%9D%98%20%EA%B3%B5%ED%86%B5%20%EB%A9%94%EC%86%8C%EB%93%9C.md#item-14-comparable%EC%9D%84-%EA%B5%AC%ED%98%84%ED%95%A0%EC%A7%80-%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC)

### 4. Class 와 상속
#### [Item 15. 클래스와 멤버의 접근 권한을 최소화하라.](https://github.com/saechimdaeki/Effective-JAVA/blob/main/04%20%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4.md#item-15-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80-%EB%A9%A4%EB%B2%84%EC%9D%98-%EC%A0%91%EA%B7%BC-%EA%B6%8C%ED%95%9C%EC%9D%84-%EC%B5%9C%EC%86%8C%ED%99%94%ED%95%98%EB%9D%BC)

#### [Item 16. public class 에서는 get method를 통해 필드에 접근하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/04%20%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4.md#item-16-public-class-%EC%97%90%EC%84%9C%EB%8A%94-get-method%EB%A5%BC-%ED%86%B5%ED%95%B4-%ED%95%84%EB%93%9C%EC%97%90-%EC%A0%91%EA%B7%BC%ED%95%98%EB%9D%BC)

#### [Item 17. 변경 가능성을 최소화 하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/04%20%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4.md#item-17-%EB%B3%80%EA%B2%BD-%EA%B0%80%EB%8A%A5%EC%84%B1%EC%9D%84-%EC%B5%9C%EC%86%8C%ED%99%94-%ED%95%98%EB%9D%BC)

#### [Item 18. 상속보다는 컴포지션을 사용하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/04%20%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4.md#item-18-%EC%83%81%EC%86%8D%EB%B3%B4%EB%8B%A4%EB%8A%94-%EC%BB%B4%ED%8F%AC%EC%A7%80%EC%85%98%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)

#### [Item 19. 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/04%20%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4.md#item-19-%EC%83%81%EC%86%8D%EC%9D%84-%EA%B3%A0%EB%A0%A4%ED%95%B4-%EC%84%A4%EA%B3%84%ED%95%98%EA%B3%A0-%EB%AC%B8%EC%84%9C%ED%99%94%ED%95%98%EB%9D%BC-%EA%B7%B8%EB%9F%AC%EC%A7%80-%EC%95%8A%EC%95%98%EB%8B%A4%EB%A9%B4-%EC%83%81%EC%86%8D%EC%9D%84-%EA%B8%88%EC%A7%80%ED%95%98%EB%9D%BC)

### 5. Interface와 설계 원칙
#### [Item 20 . Abstract class 보다는 interface를 우선하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/05%20Interface%EC%99%80%20%EC%84%A4%EA%B3%84%20%EC%9B%90%EC%B9%99.md#item-20--abstract-class-%EB%B3%B4%EB%8B%A4%EB%8A%94-interface%EB%A5%BC-%EC%9A%B0%EC%84%A0%ED%95%98%EB%9D%BC)
#### [Item 21. 인터페이스는 구현하는 쪽을 생각해 설계하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/05%20Interface%EC%99%80%20%EC%84%A4%EA%B3%84%20%EC%9B%90%EC%B9%99.md#item-22-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%8A%94-%ED%83%80%EC%9E%85%EC%9D%84-%EC%A0%95%EC%9D%98%ED%95%98%EB%8A%94-%EC%9A%A9%EB%8F%84%EB%A1%9C%EB%A7%8C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)
#### [Item 22. 인터페이스는 타입을 정의하는 용도로만 사용하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/05%20Interface%EC%99%80%20%EC%84%A4%EA%B3%84%20%EC%9B%90%EC%B9%99.md#item-22-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%8A%94-%ED%83%80%EC%9E%85%EC%9D%84-%EC%A0%95%EC%9D%98%ED%95%98%EB%8A%94-%EC%9A%A9%EB%8F%84%EB%A1%9C%EB%A7%8C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)
#### [Item23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/05%20Interface%EC%99%80%20%EC%84%A4%EA%B3%84%20%EC%9B%90%EC%B9%99.md#item23-%ED%83%9C%EA%B7%B8-%EB%8B%AC%EB%A6%B0-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%B3%B4%EB%8B%A4%EB%8A%94-%ED%81%B4%EB%9E%98%EC%8A%A4-%EA%B3%84%EC%B8%B5%EA%B5%AC%EC%A1%B0%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%98%EB%9D%BC)
#### [Item 24. 멤버 클래스는 되도록 static으로 만들라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/05%20Interface%EC%99%80%20%EC%84%A4%EA%B3%84%20%EC%9B%90%EC%B9%99.md#item-24-%EB%A9%A4%EB%B2%84-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-%EB%90%98%EB%8F%84%EB%A1%9D-static%EC%9C%BC%EB%A1%9C-%EB%A7%8C%EB%93%A4%EB%9D%BC)
#### [Item 25 . 탑레벨 클래스는 한 파일에 하나만 담으라](https://github.com/saechimdaeki/Effective-JAVA/blob/main/05%20Interface%EC%99%80%20%EC%84%A4%EA%B3%84%20%EC%9B%90%EC%B9%99.md#item-25--%ED%83%91%EB%A0%88%EB%B2%A8-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-%ED%95%9C-%ED%8C%8C%EC%9D%BC%EC%97%90-%ED%95%98%EB%82%98%EB%A7%8C-%EB%8B%B4%EC%9C%BC%EB%9D%BC)
