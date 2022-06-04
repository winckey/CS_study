String 생성 방법
new 연산자
String str = new String("new 연산자로 String 생성")
JVM의 Heap 영역에 존재하게 된다(Object임. String은 Reference Type)
literal 방식
String literalStr = "literal 방법으로 String 생성"
JVM의 String Constant Pool에 존재하게 된다
JAVA 6 까지 String Constant Pool은 Heap 영역의 Perm에 있었다.
JAVA 7부터 Heap 영역으로 변경되었다 (Out Of Memory 이슈때문)
Perm은 Runtime중 size를 바꿀 수 없다
Heap 영역으로 변경된 것은, GC의 대상이 될 수 있다
JDK 8에서 Perm 영역을 삭제하고, Metaspace 영역이 추가(Native 영역)
OS가 자동으로 크기를 조절
OutOfMemoryError를 더 보기 힘들어졌다