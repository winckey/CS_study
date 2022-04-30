Reflection이란?
구체적인 클래스 타입을 알지 못해도 그 클래스의 메소드, 타입, 변수들에 접근할 수 있도록 해주는 자바 API

런타임에 지금 실행되고 있는 클래스를 가져와서 실행해야하는 경우

동적으로 객체를 생성하고 메서드를 호출하는 방법

자바의 리플렉션은 클래스, 인터페이스, 메소드들을 찾을 수 있고, 객체를 생성하거나 변수를 변경하거나 메소드를 호출할 수 있다.


어떤 경우에 사용되나?

코드를 작성할 시점에는 어떤 타입의 클래스를 사용할지 모르지만, 런타임 시점에 지금 실행되고 있는 클래스를 가져와서 실행해야 하는 경우

프레임워크나 IDE에서 이런 동적인 바인딩을 이용한 기능을 제공한다. intelliJ의 자동완성 기능, 스프링의 어노테이션이 리플렉션을 이용한 기능이다.
-> intelliJ 자동완성 기능은 알거같은데 어노테이션은 어떻게 동작하는지 잘 모르겠다... 어노테이션을 학습해봐야 알 수 있을듯하다.


Java Reflection 사용 실습
Person이라는 클래스를 생성하고, 리플렉션을 사용해보자.

class Person {
    int age;

    Person() {
        this.age = 27;
    }

    Person(int age) {
        this.age = age;
    }

    int getAge() {
        return this.age;
    }
}
생성자 찾기
getDeclaredConstructor()를 이용해 클래스로부터 생성자를 가져올수 있다.

Class clazz = Class.forName("Person");
Constructor constructor = clazz.getDeclaredConstructor();
getDeclaredConstructor()는 인자가 없는 생성자를 가져온다.


Method 찾기

Class clazz = Person.class;
Method[] methodList = clazz.getDeclaredMethods();    
System.out.println(methods[0].invoke(clazz.newInstance())) // 27이 출력됨
invoke() 메소드를 사용하면 Method 객체를 실행할 수 있다. 첫번째 인자는 호출하려는 객체, 두번째 인자는 전달할 파라미터 값을 준다.


Field 변경

Class clazz = Person.class;
Field[] field = clazz.getDeclaredFields();
System.out.println(field[0]);   // 출력 : int reflection_test.Person.age
필드 가져오기

Class clazz = Person.class;
Field[] field = clazz.getDeclaredFields();

Person person = new Person();
field[0].set(person, 17);
System.out.println(field[0].get(person));  // 17이 출력됨 
set() 메소드를 사용해서 객체의 변수를 변경할 수 있다.


이외에도 리플렉션을 사용하는 여러가지 방법이 있지만, 블로그를 보고 간단하게 나만의 실습을 해보았다. 더 자세한 내용은 필요할때 블로그 참고해서 공부해보자. 이번엔 리플렉션이 무엇인지 이해하는게 목적.