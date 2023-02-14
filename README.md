# item21. 인터페이스는 구현하는 쪽을 생각해 설계하라


## Java 8 이전 

>기존 구현체를 깨뜨리지 않고는 인터페이스에 메세드를 추가할 방법이 없었다.<br> 
기존 인터페이스에 메서드를 추가했을 때 컴파일 오류가 발생합니다 <br>
→ 우연히 기존 구현체에  추가된 메서드가 이미 존재할 가능성이 굉장히 낮기 때문에 컴파일 오류가 발생한다.

## Java 8 이후
>기존 인터페이스에 메서드를 추가할수 있도록 
`디폴트 메서드`를 소개 하고있습니다.<br>
하지만 모든 기존 구현체들과 매끄럽게 연동되리라는 보장은 없다. 불변식을 해치지 않는 디폴트 메서드를 작성하기 어렵습니다<br>
→ 자바 7까지의 세상에서는 모든 클래스가 **"현재의 인터페이스에 새로운 메서드가 추가될 일은 영원히 없다"** 고 가정하고 작성되었기 때문이다.


 ### 자바 8의 Collection 인터페이스에 추가된 removeIf 메서드 
```java
public interface Collection<E> extends Iterable<E> {

	// ...

	default boolean removeIf(Predicate<? super E> filter) {
		Objects.requireNonNull(filter);
		boolean removed = false;
		final Iterator<E> each = iterator();
		while (each.hasNext()) {
			if (filter.test(each.next())) {
				each.remove();
				removed = true;
			}
		}
		return removed;
	}

	// ...

}
```

```java
//사용예시 코드 
public class Exam {

    public static void main(String[] args) {
        List<String>  colors = new ArrayList<>();
        colors.add("White");
        colors.add("Yellow");
        colors.add("Red");
        colors.add("Blue");
        colors.add("Black");

        System.out.println(colors);

        // removeIf 사용 조건에 일치하는 값인 문자열 길이가 5보다 큰 값을 삭제
        colors.removeIf(name -> name.length() > 5);

        // 삭제후 요소를 출력
        System.out.println("---------------------------");
        System.out.println(colors);
    }

}
```

### SynchroziedCollection
>모든 메서드에서 주어진 락 객체로 동기화한 후 내부 컬렉션 객체에 기능을 위임하는 래퍼 클래스이다.

→ Thread-safe한 객체를 만들 수 있다.

![image](https://user-images.githubusercontent.com/42836576/106704468-8a04d280-662f-11eb-979d-3905cb495335.png)
>책 에서 언급하는 시점 removeIf 메서드를 재정의 하고 있지 않다 <br>
> 
→ 3.2.2 버전에서는 removeIf를 재정의하지 않고 있다.

![image](https://user-images.githubusercontent.com/42836576/106704478-8c672c80-662f-11eb-8434-d37db32b0134.png)

4.4 버전에서는 removeIf를 재정의하고 있다.
🔎  [버전 확인 링크](https://commons.apache.org/proper/commons-collections/javadocs/api-4.4/index.html)


### 3.2.2버전에서 재정의 되지않은 `removeIf` 호출할 경우  

→ **ConcurrentModificationException**이 발생하거나 예기치 못한 결과가 발생할 수 있다.

 
# 📋 **정리**
### `인터페이스를 설계할때는 여전히 세심함 주의를 기울여야 합니다`<br>
자바 8 이전 인터페이스에 메서드를 추가할 수 없었습니다<br>
하지만 자바 8 에서 부터 추가할수 있도록 디폴트 메서드 라는 도구가 생겼디고 언급하고 있습니다<br>
### `디폴트 메서드`<br>
a. 기존 인터페이스에는 꼭 필요한 경우가 아니면 디폴트 메서드는 추가하지 않는 것이 좋다.<br>
b. 인터페이스로 부터 메서드 제거 또는 기존 메서드의 수정 용도가 아니다<br>
c. item 20 새로운 인터페이스를 경우에 디폴트 메서드를 활용하면 인터페이스를 구현하는데 잇어서 활용할 수 있는 유용한 수단이다 <br>

### `인터페이스 설계`<br> 
a. 릴리스 전 반드시 테스트를 거쳐야 한다<br>
b. 최소한 세가지 이상의로 인터페이스를 구현해보아야 한다<br>
c. 인터페이스 릴리스 이후 수정이 가능한 경우도 있지만 추후에 결함 수정 가능성에 기대서는 안된다.
