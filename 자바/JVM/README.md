Autoboxing vs Unboxing
오토박싱(Autoboxing)은 Java 컴파일러가 원시 타입(Primitive types)과 해당 객체 래퍼 클래스 간에 수행하는 자동 변환을 말한다. 예를 들어 int를 Integer로, double을 Double로 변환하는 식이다. 변환이 다른 방향으로 진행되는 경우 이를 언박싱(unboxing)이라고 한다.

다음 간단한 오토박싱 예제를 살펴보자.

Character ch = 'a';
‘a’의 타입은 char인데 Character에 대입했다. 이렇게 해도 호환이 되며 이것이 오토박싱이다.

다음 예제코드를 살펴보자.

List<Integer> li = new ArrayList<>();
for (int i = 1; i < 50; i += 2)
    li.add(i);
int 값을 Integer 객체가 아닌 기본 유형으로 li에 추가하더라도 코드가 컴파일된다. li는 int 값 List가 아니라 Integer 객체 List이기 때문에 Java 컴파일러가 컴파일 타임 오류를 발생시키지 않는지 의문이 생길 수 있다. 그러나 컴파일러는 i에서 Integer 객체를 만들고 li에 객체를 추가하기 때문에 오류가 생기지는 않는다.

따라서 컴파일러는 런타임에 위 코드를 다음 코드로 변환한다.

List<Integer> li = new ArrayList<>();
for (int i = 1; i < 50; i += 2)
    li.add(Integer.valueOf(i));
위의 예제처럼 원시타입의 값을 적당한 래퍼 클래스의 타입으로 변경하는 것을 오토박싱이라고 한다.

또 다른 예제코드를 살펴보자.

public static int sumEven(List<Integer> li) {
    int sum = 0;
    for (Integer i: li)
        if (i % 2 == 0)
            sum += i;
        return sum;
}
‘%’ 연산자는 및 ‘+=’ 연산자는 일반적으로 객체에는 적용이 안되기 때문에 컴파일러가 어떻게 에러없이 이러한 작업을 수행하는지 궁금할 것이다. 컴파일러는 런타임에 Integer를 int로 변환하기 위해 다음과 같이 intValue 메서드를 호출한다.

public static int sumEven(List<Integer> li) {
    int sum = 0;
    for (Integer i : li)
        if (i.intValue() % 2 == 0)
            sum += i.intValue();
        return sum;
}
래퍼 클래스 타입의 객체를 원시 타입 값으로 변경하는 것을 언박싱이라고 한다.

오토박싱 및 언박싱을 통해 개발자는 보다 깔끔한 코드를 작성할 수 있으므로 읽기 쉽다. 다음은 오토박싱 및 언박싱을 위해 Java 컴파일러에서 사용하는 원시 타입 및 해당 래퍼 클래스를 나열한다.

boolean <-> Boolean
byte <-> Byte
char <-> Character
float <-> Float
int <-> Integer
long <-> Long
short <-> Short
double <-> Double
JVM 내에 캐시 된 문자열 및 박싱된 원시타입 객체들
JVM내에서는 하나의 가상 머신에서 동일한 문자열을 처리하는 코드가 여러 개 있다면, 기존의 문자열을 재사용 한다.

fun main() {
    val str1 = "Charles"
    val str2 = "Charles"
    val str3 = StringBuilder("Charles").toString()
    
    println(str1 == str2) // true, 동등성 비교
    println(str1 === str2) // true, 레퍼런스(메모리 주소) 비교
    println(str2 === str3) // false, 새로운 객체 생성 (새로운 메모리 할당)
    println(str2 === str3.intern()) // true, JVM 문자열 pool에 있는 객체를 가져옴
}
Open in Playground →
Target: JVM
Running on v.1.7.10
Integer와 Long처럼 박스화한 기본 자료형도 기본적으로 -128~127 범위를 캐시한다.

fun main() {
    val i1: Int? = 1
    val i2: Int? = 1
    val i3: Int? = 1000
    val i4: Int? = 1000
  
    println(i1==i2) // true, 값 비교
    println(i1===i2) // true, 레퍼런스 비교
    println(i3==i4) // true, 값 비교
    println(i3===i4) // 캐시되지 않는 값이므로 false를 반환, 레퍼런스 비교
}
Open in Playground →
Target: JVM
Running on v.1.7.10
코루틴 스코프 내에서는 캐시되지 않는다.
위의 예제 코드를 간단히 runBlocking 스코프로 감싸보자.

import kotlinx.coroutines.*
​
fun main() = runBlocking {
    val str1 = "Charles"
    val str2 = "Charles"
    val str3 = StringBuilder("Charles").toString()
    
    println(str1 == str2) // true
    println(str1 === str2) // true
    println(str2 === str3) // false
    println(str2 === str3.intern()) // true
}
Open in Playground →
Target: JVM
Running on v.1.7.10
String 문자열의 경우 코루틴 스코프 내에서도 기존 예제코드와 동일한 결과를 나타낸다. 하지만 다음 박싱된 원시 타입의 캐싱 예제 코드를 살펴보자.

import kotlinx.coroutines.*
​
fun main() = runBlocking {
    val i1: Int? = 1
    val i2: Int? = 1
    val i3: Int? = 1000
    val i4: Int? = 1000
  
    println(i1==i2) // true
    println(i1===i2) // false, 캐싱되지 않음
    println(i3==i4) // true
    println(i3===i4) // false
}
Open in Playground →
Target: JVM
Running on v.1.7.10
(i1 === i2) 의 비교가 false를 리턴하는 것을 확인할 수 있다. 일반적인 함수에서 수행시 캐시되는 것을 확인할 수 있었는데, 코루틴 스코프에서는 왜 결과가 달라졌을까?

해당 코드들을 디컴파일 해보았다.


빨간색 상자로 마킹해둔 부분이 눈여겨 볼 부분이다. 왼쪽은 컴파일 된 이후에 JVM내 캐시된 값을 참조하고 있는데, 오른쪽은 Boxing.boxInt(…) 를 참조하고 있다. 이렇게 되면 내부적으로 객체를 새로 생성하게 되고 새로운 메모리를 할당 받으므로 레퍼런스가 갈리게 된다.

코루틴 스코프에서 컴파일러의 동작이 왜 다른지는 모르겠다. 하지만 박싱된 기본 자료형이 캐시 되지 않는 경우도 있다 정도만 알고 넘어가도록 하자.