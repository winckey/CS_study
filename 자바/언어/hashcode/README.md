- == : 주소값만으로만 비교
- equals : 논리적동등비교 ( 재정의 가능 )
- hashcode : 객체의 메모리 번지를 이용하여 생성 
   - 다른 객체라도 해쉬값이 같을 수 있다
   -  java8인가 9버전부터 LinkedList 아이템의 갯수가 8개 이상으로 넘어가면 TreeMap 자료구조로 저장된다
   
   
   
💡hash컬랙션비교 과정
1. 해쉬값 비교
2. 해쉬값이 동일하다면 equals 메소드 이용

- set이 중복이 없는 이유 -> 해쉬테이블을 이용한 빠른검색을 이용한 중복제거
- HashTable에 put 메서드로 객체를 추가하는 경우
  - 값이 같은 객체가 이미 있다면(equals()가 true) 기존 객체를 덮어쓴다.
  - 값이 같은 객체가 없다면(equals()가 false) 해당 entry를 LinkedList에 추가한다.


  💡불변객체 : 불변객체는 재할당은 가능하지만, 한번 할당하면 내부 데이터를 변경할 수 없는 객체

- 불변객체는 참조타입일경우 내부 변수도 불변이여야 한다. 객체 자체는 변경이 일어날수 없지만 접근은 가능하기 때문에 내부 변수또한 통제하여야 한다.

```java
public class Animal {
    
    private final Age age;

    public Animal(final Age age) {
        this.age = age;
    }
    
    public Age getAge() {
    	return age;
    }
}

class Age {
    
    private int value;

    public Age(final int value) {
        this.value = value;
    }

    public void setValue(final int value) {
        this.value = value;
    }
    
    public int getValue() {
    	return value;
    }
}
```
Animal 클래스는 final을 사용하고, Setter를 구현하지 않았지만 불변객체가 될 수 없습니다. 왜냐하면 Animal 클래스의 필드인 Age의 값을 아래처럼 변경할 수 있기 때문입니다.
```java
public static void main(String[] args) {
    Age age = new Age(1);
    Animal animal = new Animal(age);

    System.out.println(animal.getAge().getValue());
    // Output: 1

    animal.getAge().setValue(10);
    System.out.println(animal.getAge().getValue());
    // Output: 10
}
```
