# Chapter 3 - 람다 표현식    
익명 클래스로 다양한 동작을 구현할 수 있지만 만족할 만큼 코드가 깔끔하진 않다. 깔끔하지 않은 코드는 동작 파라미터를 실전에 적용한느 것을 막는 요소다. 3장에서는 더 깔끔한 코드로 동작을 구현하고 전달하는 자바8의 새로운 기능인 람다 표현식을 설명한다. 람다 표현식은 익명 클래스처럼 이름이 없는 함수이면서 메서드를 인수로 전달할 수 있으므로 일단은 람다 표현식이 익명 클래스와 비슷하다고 생각하자.

## 3.1 람다란 무엇인가?   
람다 표현식은 메서드로 전달할 수 있는 익명함수를 단순화한 것이라고 할 수 있다.

람다의 특징
 - 익명 : 보통의 메서드와 달리 이름이 없으므로 익명이라 표현한다.
 - 함수 : 람다는 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 부른다.
 - 전달 : 람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있다.
 - 간결성 : 익명 클래스 처럼 많은 자질구레한 코드를 구현할 필요가 없다.

```
//기존 코드
Comparator<Apple> byWeight = new Comparator<Apple>(){
  public int compare(Apple a1,Apple a2){
    return a1.getWeight().compareTo(a2.getWeight());
  }
};

//람다 코드
Comparator<Apple> byWeight = (Apple a1,Apple a2) -> a1.getWeight().compareTo(a2.getWeight());

파라미터 리스트 : Comparator의 compare 메서드 파라미터(사과 2개)
화살표 : 화살표는 람다의 리스트와 바디를 구분한다.
람다 바디 : 두 사과의 무게를 비교한다. 람다의 반환값에 해당하는 표현식이다. 
```

## 퀴즈 ( 람다 문법 ) -> 4,5번이 잘못됨 
1. () -> {}
2. () -> "Raoul"
3. () -> {return "Mario";}
4. (Integer i) -> return "Alan" + i;   // {} 빠짐
5. (String s) -> {"Iron Man";} // return 빠짐

## 3.2.1 함수형 인터페이스
우리가 인터페이스를 만들면, 그 인터페이스로 필터 메서드를 파라미터화할 수 있었음을 기억해보자. 함수형 인터페이스는 오직 하나의 추상 메서드만 지정한다.
예를 들면 Comparable의 compare메서드 라던가 등등 에서 람다를 사용 가능하다.

## 3.2.2 함수 디스크립터
함수형 인터페이스의 추상 메서드 시그니처는 람다 표현식의 시그니처를 가리킨다. 람다 표현식의 시그니처를 서술하는 메서드를 함수 디스크립터라고 부른다.
예를 들어 Runnable 인터페이스의 유일한 추상 메서드 run은 인수와 반환값이 없으므로(void반환) Runnable 인터페이스는 인수와 반환값이 없는 시그니처로 생각할 수 있다. ex) ()->void
다양한 종류의 함수 디스크립터를 기술하는 인터페이스를 알아보자 

## 3.4 함수형 인터페이스 사용
다양한 람다 표현식을 사용하려면 공통의 함수 디스크립터를 기술하는 함수형 인터페이스 집합이 필요하다. 이미 자바 API는 Comparable,Runnable,Callable 등의 다양한 함수형 인터페이스를 포함하고 있다.
자바 8 라이브러리 설계자들은 java.util.function 패키지로 여러가지 새로운 함수형 인터페이스를 제공한다. 이 절에서는 Predicate,Consumer,Function 인터페이스를 설명한다. 

## 3.4.1 Predicate
이 인터페이스는 test라는 추상 메서드를 정의하며 test는 제네릭 형식 T의 객체를 인수로 받아 불리언(boolean)을 반환한다. 따로 정의할 필요없이 바로 사용할 수 있다는 점이 장점이다.
T형식의 객체를 사용하는 불리언 표현식이 필요한 상황에 사용하면된다.

```
Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
```

## 3.4.2 Consumer
이 인터페이스는 제네릭 형식 T객체를 받아서 void를 반환하는 accept라는 추상 메서드를 정의다. T형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 사용하면 된다.
예를 들어 Integer 리스트를 인수로 받아서 각 항목에 어떤 동작을 수행한느 forEach메서드를 정의할 때 활용할 수 있다.

```
// Consumer의 accept를 구현하는 람다.
forEach(
        Arrays.asList(1,2,3,4,5),
        (Integer i) -> System.out.println(i)
);
```

## 3.4.3 Function
이 인터페이스는 제네릭 형식 T객체를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 apply를 정의한다.

```
// String 리스트를 인수로 받아서 -> 각 String의 길이를 포함하는 Integer 리스트로 변환하는 map메서드를 정의하는 예제
// [7,2,6]
List<Integer> 1 = map(
        Arrays.asList("lambdas","in","action"),
        (String s) -> s.length()
);
```

## 3.5.2 같은 람다, 다른 함수형 인터페이스
하나의 람다 표현식을 다양한 함수형 인터페이스에 사용할 수 있다.
```
Comparator<Apple> c1 = (Apple a1,Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
ToIntBiFunction<Apple,Apple> c2 = (Apple a1,Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
BiFunction<Apple,Apple,Integer> c3 = (Apple a1,Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

## 3.5.3 형식 추론
우리의 코드를 좀 더 단순화할 수 있ㄴ느 방법이 있다. 자바 컴파일러는 람다 표현식이 사용된 콘텍스트(대상 형식)를 이용해서 람다 표현식과 관련된 함수형 인터페이스를 추론한다. 즉 대상형 식을 이용해서 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그니처도 추론할 수 있다.

```
Comparator<Apple> c = (Apple a1,Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
Comparator<Apple> c = (a1,a2) -> a1.getWeight().compareTo(a2.getWeight());
```

## 3.6 메서드 참조
메서드 참조를 이용하면 기존의 메서드 정의를 재활용해서 람다처럼 전달할 수 있다. 때로는 이렇게 하는 것이 더 가독성이 좋고 자연스럽다.

```
// 람타 표현식
inventory.sort((Apple a1,Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
// 메서드 참조
inventory.sort(comparing(Apple::getWeight));
```

## 메서드 참조 예제
```
(Apple apple) -> apple.getWeight() / Apple::getWeight
()-> Thread.currentThread().dumpStack() / Thread.currentThread()::dumpStack
(str,i) -> str.substring(i) / String::substring
(String s) -> System.out.println(s) (String s) -> this.isValidName(s) / System.out::println this::ValidName
```

## 3.6.2 생성자 참조
ClassName:new 처럼 클래스명과 new키워드를 이용해서 기존 생성자의 참조를 만들 수 있다. 이것은 정적 메서드의 참조를 만드는 방법과 비슷하다. 
```
//Supplier의 ()-> Apple과 같은 시그니처를 갖는 생성자가 있다고 가정하자.
// 메서드 참조
Supplier<Apple> c1 = Apple::new;
Apple a1 = c1.get();
// 람다 표현식
Supplier<Apple> c1 = () -> new Apple();
Apple a1 = c1.get();
```

## 3.7 람다, 메서드 참조 할용하기
처음에 다룬 사과 리스트를 다양한 정렬 기법으로 정렬하는 문제로 돌아가서 깔끔하게 해결하는 방법을 보여주면서 람다를 마무리한다.
사과 리스트 정렬 문제를 해결하면서 지금까지 배운 동작파라미터화, 익명 클래스, 람다 표현식, 메서드 참조 등을 총동원한다. 

```
//최종 목표 코드
inventory.sort(comparing(Apple::getWeight));
```

## 3.7.1 1단계 : 코드 전달 (동작 파라미터화)
sort메서는 다음과 같은 시그니처를 갖는다.
```
void sort(Comparator<? super E> c)
```
이 코드는 Comparator 객체를 인수로 받아 두 사과를 비교한다. 객체 안에 동작을 포함시키는 방식으로 다양한 전략을 전달할 수 있다. 이제 'sort의 동작은 파라미터화되었다'라고 말할 수 있다.
즉, sort에 전달된 정렬 전략에 따라 sort의 동작이 달라질 것이다.
```
public  class AppleComparator implements Comparator<Apple> {
  @Override
  public int compare(Apple a1, Apple a2) {
    return a1.getWeight() - a2.getWeight();
  }
}
inventory.sort(new AppleComparator());
```

## 3.7.2 2단계 : 익명 클래스 사용
위처럼 compare를 구현하는 것보다 익명 클래스를 이용하는 것이 좋다.
```
inventory.sort(new Comparator<Apple>() {
  @Override
  public int compare(Apple a1, Apple a2) {
    return a1.getWeight() - a2.getWeight();
  }
});
```

## 3.7.3 3단계 : 람다 표현식 이용
```
// 람다 표현식 사옹
1.inventory.sort((Apple a1,Apple a2) -> a1.getWeigth().compareTo(a2.getWeight()));

// 자바 컴파일러는 람다가 사용된 컨텍스트를 활용해 람다의 파라미터 형식을 추론한다. 그렇기 때문에 코드는 줄여진다.
2.inventory.sort((a1,a2) -> a1.getWeigth().compareTo(a2.getWeight()));

// 이 코드의 가독성을 더 향상시키는 법, Comparator는 Comparable 키를 추출해서 Comparator객체로 만드는 Function함수를 인수로 받는 정적 메서드 comparing을 포함한다. 다음처럼 comparing 메서드를 사용할 수 있다.
3.Comparator<Apple> c = Comparator.comparing((Apple a) -> a.getWeight());

// 이제 다음처럼 간소화할 수 있다.
4.inventory.sort(comparing(apple -> apple.getWeight()));
```

## 3.7.4 4단계 : 메서드 참조 사용
```
// 'Apple을 weight별로 inventory를 sort하라'
inventory.sort(comparing(Apple::getWeight());

// 역정렬
inventory.sort(comparing(Apple::getWeight().reversed());

// 같은 경우 처리
inventory.sort(comparing(Apple::getWeight)
         .reversed()
         .thenComparing(Apple::getCountry());

// Predicate 조합 , Function 조합 등등 
```

