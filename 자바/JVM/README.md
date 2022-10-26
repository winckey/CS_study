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


### 가비지 컬렉션(Garbage Collection)이란?

- 가비지 컬렉션은 자바의 메모리 관리 기법이다. 
- 힙 메모리에서 동적으로 할당되어 사용 중인 객체와 사용하지 않는 객체를 식별하고 사용하지 않는 개체를 삭제하는 작업을 담당하고 있다. 
- C와 같은 프로그래밍 언어는 메모리 할당 및 할당 해제를 수동으로 하지만 Java에서는 이 가비지 컬렉션이 자동으로 처리된다.

#### JVM 세이프포인트

- 애플리케이션 스레드마다 세이프포인트(안전점, safe point)라는 특별한 지점을 둡니다. 
- 세이프포인트는 스레드의 내부 자료 구조가 보이는 지점이며, 이때 어떤 작업을 하기 위해 스레드는 잠시 중단될 수 있습니다.

#### 세이브 포인트 규칙

1. JVM은 강제로 스레드를 세이프포인트 상태로 바꿀 수 없습니다.
2. JVM은 스레드가 세이프포인트 상태에서 벗어나지 못하게 할 수 있습니다.

따라서 세이프포인트 요청을 받았을 때 그 지점에서 스레드가 제어권을 반납하게 만드는 코드가 VM 인터프리터 구현체 어디에 잇어야합니다. 세이프포인트 상태로 바뀌는 경우는 다음과 같습니다.

1. JVM이 전역을 세이프포인트 시간 플래그를 세팅합니다.
2. 각 애플리케이션 스레드는 폴링_(스레드 할당)_ 을 하면서 이 플래그가 세팅됐는지 확인합니다.
3. 애플리케이션 스레드는 일단 멈췄다가 다시 깨어날 때까지 대기합니다.
4. 세이프포인트 시간 플래그를 세팅하면 모든 애플리케이션 스레드는 반드시 멈춰야합니다. 


다음의 경우, 스레드는 자동으로 세이프포인트 상태가 됩니다.

- 모니터에서 차단되는 경우
- JNI 코드를 실행하는 경우

다음의 경우, 스레드가 꼭 세이프포인트 상태가 되는 것은 아닙니다.

- 바이트코드를 실행하는 도중(인터프리티드 모드) OS가 인터럽트를 거는 경우


#### Garbage collection 이 있는 언어를 원자력 발전소, 자동차 동력 제어, 인공위성, 국가 전력망 제어시스템 같은 곳에 쓸 수 있을까요? 후보자님의 생각을 말씀해 주세요. 

절대 안됨 Garbage collection은 개발자가 직접 메모리 할당과 해제를 관여하지 않는 대신 stw가 언제 적용될지 알수 없다. 은행이나 전산업무 어플리케이션은 잠깐 기다리면 되지만 전기는 잠깐 기다린다고 공급안하면 중단됨 자동차와같은 운행또한 잠깐 멈추고 대기상태가 되었을때 무슨일이 벌어질지 모른다. 

https://www.kernelpanic.kr/32?category=930930

#### gc가 없는 언어는 어떻게 메모리를 관리하는가?

### c 언어  

1. 스택 : 호출 함수가 종료되면 자동 해제 해당 로컬변수들(검색할 필요가 없음 어플리케이션 중단 x)
크기 제한이 있어 오버플로우 가능
![](https://velog.velcdn.com/images/winckey0/post/5f420da8-4d8b-48f3-beea-f74431dc2a43/image.png)


2. 힙 (Heap)
힙은 '더미'로 직역된다. 스택이 함수 호출에 따라서 메모리가 차곡차곡 쌓인 모양으로 관리되는 반면, 힙은 이름 그대로 메모리가 힙 영역에 아무렇게나 할당되어 있다. 따라서 힙은 스택과 비교하면 크게는 다음 세가지 특징을 가지고 있다.

- 힙에 저장된 데이터는 함수 호출이 종료되어도 해제되지 않는다. 개발자가 명시적으로 해제하거나, 프로그램이 종료되어야 해제된다.
- 프로그램은 메모리 주소에 따라서 힙 데이터에 접근한다.
- 힙 공간은 크기의 제약이 없다. 메모리가 충분하다면, 힙 영역은 필요한 만큼 확장될 수 있다.

보통 힙에 메모리 할당은 malloc, calloc, realloc 함수를 사용하고, 해제는 free 함수를 사용한다.
![](https://velog.velcdn.com/images/winckey0/post/6b7a52dd-3b36-4465-b99d-636318654fb6/image.png)


### c언어의 메모리 관리 

메모리 관리 규칙 
1. (규칙1) 가능하다면 메모리 할당과 해제는 한 코드 블록 안에서 한 번만
2. (규칙2) 메모리 할당과 해제가 한 블록 이내에서 이뤄질 수 없다면 레퍼런스 카운트를 활용

#### 레퍼런스 카운트
c 언어의 경우 rc 레퍼런스 카운트를 활용한다. (하지만 C언어에서는 개발자가 레퍼런스 카운트를 직접 관리해야 한다.)

레퍼런스 카운트는 동적으로 할당된 메모리를 참조하는 객체의 수를 의미한다. 동적으로 할당된 메모리를 특정 객체가 참조하게 되면 레퍼런스 카운트 값을 증가시키고 해당 메모리를 참조하는 객체가 종료되면 레퍼런스 카운트를 감소시킨다. 레퍼런스 카운트 값이 0이 되면 해당 메모리를 해제한다.

레퍼런스 카운트를 활용한 메모리 관리는 원리가 어렵지 않고 구현하기 간단하며 프로세스에 연산 / 메모리 부하를 많이 주지 않는다. 따라서 초기버전의 가비지 컬렉터나 Rust의 Rc/Arc 객체등에 의해서 사용되고 있다.
![](https://velog.velcdn.com/images/winckey0/post/35bfabfe-259e-4761-bd6c-e57efbc76ee0/image.png)
