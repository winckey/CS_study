

public interface Turkey {
	public void gobble();
	public void fly();
}

#WildTurkey.java
package AdapterPattern;

public class WildTurkey implements Turkey {

	@Override
	public void gobble() {
		System.out.println("Gobble gobble");
	}

	@Override
	public void fly() {
		System.out.println("I'm flying a short distance");
	}
}

#TurkeyAdapter.java
package AdapterPattern;

public class TurkeyAdapter implements Duck {

	Turkey turkey;

	public TurkeyAdapter(Turkey turkey) {
		this.turkey = turkey;
	}

	@Override
	public void quack() {
		turkey.gobble();
	}

	@Override
	public void fly() {
		turkey.fly();
	}

}

#DuckTest.java
package AdapterPattern;

public class DuckTest {

	public static void main(String[] args) {

		MallardDuck duck = new MallardDuck();
		WildTurkey turkey = new WildTurkey();
		Duck turkeyAdapter = new TurkeyAdapter(turkey);

		System.out.println("The turkey says...");
		turkey.gobble();
		turkey.fly();

		System.out.println("The Duck says...");
		testDuck(duck);

		System.out.println("The TurkeyAdapter says...");
		testDuck(turkeyAdapter);

	}

	public static void testDuck(Duck duck) {

		duck.quack();
		duck.fly();

	}
}

아까 확인한 클래스 다이어그램에서 Target은 오리에 해당하며, Adapter는 칠면조라고 생각하면 된다.