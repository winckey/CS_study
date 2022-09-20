
- primitive Data

	- boolean, char, byte, short, int, long, float, double
	- 아주 가벼운 데이터를 말한다.
	- 스택메모리에 머물러있다.
- Object Data

	- 상대적으로 무거운 데이터이다.
	- 실제 데이터는 힙메모리에 공유하고 레퍼런스만 스택메모리에 있다.
- Wrapper Class

	- primitive Data를 ObjectData화 시킨 Class이다.
    
💡 primitive Data 에서 Wrapper Class로 자동으로 변환되는걸 autoboxing이라 한다.
💡 Wrapper Class에서 primitive Data 자동으로 변환되는걸 unboxing이라 한다.


- 오토박싱은 제네릭 컬렉션에 값을 추가하는 경우 유용
- Integer 타입의 ArrayList에 원시 타입인 int 타입의 값을 할당
- Integer 타입의 ArrayList에서 첫 번째 요소를 int 타입의 변수에 할당합니다.
```java
public class Main {
  public static void main(String args[]) {
    ArrayList<Integer> intArrayList = new ArrayList<>();

    // 오토박싱
    intArrayList.add(10);
    System.out.println("intArrayList: " + intArrayList);

    // 언박싱
    int num = intArrayList.get(0);
    System.out.println("num: " + num);
  }
}
```


🚫문제점

- 오토박싱은 코드에서 보이지 않지만 래퍼 클래스 객체에 값을 할당하기 위해 객체를 생성. 편리한 기능이지만 성능이 저하되는 문제가 존재하므로 반복문에서 오토박싱을 사용하는 것은 좋은 소스코드가 아니다.
```java
 private static long sum_with_autoboxing() {
        Long sum = 0L;
        for (long i = 0; i < Integer.MAX_VALUE; ++i) {
            sum += i;
        }
        return sum;
    }
```
	sum += i -> sum += Long.valueOf(i); 로 바꾸게 된다.
	매번 새로운 객체 생성
    


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
