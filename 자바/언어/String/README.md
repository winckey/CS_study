
### String

- String은 문자들을 유니코드로 보관 한다. 
    - HTML / 마크업 문자들을 저장 할수 없다 
    - 유니코드 범위를 넘을 경우 에러반환
    
- String은 불변성을 가짐
   - 저장되는 value가 final로 선언됨
   ```java
    private final byte[] value;
   ```
   - 객체를 변경할수 없고 연산할때마다 새로운 객체 생성
   
- 때문에 변하지 않는 문자열을 자주 읽어들이는 경우 String을 사용해 주시면 좋은 성능 보여준다. 

- 문자열 추가,수정,삭제 등의 연산이 빈번하게 발생하는 알고리즘에 String 클래스를 사용하면 힙 메모리(Heap)에 많은 임시 가비지(Garbage)가 생성되어 GC 또는 full GC가 자주 발생할수 있음.

![](https://velog.velcdn.com/images/winckey0/post/52ed1bdc-f0f7-4a9d-891b-887d92029ce5/image.png)


###  StringBuffer / StringBuilder
String의 단점을 보안하기 위한 클래스

- String 과는 반대로 StringBuffer/StringBuilder 는 가변성 가지기 때문에 .append() .delete() 등의 API를 이용하여 동일 객체내에서 문자열을 변경하는 것이 가능

StringBuffer     :  문자열 연산이 많고 멀티쓰레드 환경일 경우
StringBuilder   :  문자열 연산이 많고 단일쓰레드이거나 동기화를 고려하지 않아도 되는 경우  


### StringBuffer + Synchronized 

```java
@Override
public synchronized int length() {
	return count;
}
    
@Override
public synchronized void setCharAt(int index, char ch) {
    toStringCache = null;
    super.setCharAt(index, ch);
}
```
synchronized 키워드를 통해 멀티스레드 환경에서 제어가능