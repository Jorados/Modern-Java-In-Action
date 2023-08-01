# Chapter 2 - 동작 파라미터화 코드 전달하기   

## 2.1 변화하는 요구사항에 대응하기          
우리가 어떤 상황에서 일을 하든 소비자 요구사항은 바뀐다. 그렇기에, 동작 파라미터화를이용하면 자주 바뀌는 요구사항에 효과적으로 대응할 수 있다. 동작 파라미터화란 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의마하며, 이 코드 블록은 나중에 프로그램에서 호출된다. 즉 , 코드 블록의 실행은 미뤄진다.     

```
//원래는 각각 다른 요구사항마다 아래처럼 조건마다 하나씩 코드를 구현해야 한다.
  public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
      if (apple.getColor() == Color.GREEN) {
        result.add(apple);
      }
    }
    return result;
  }

  public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
      if (apple.getColor() == color) {
        result.add(apple);
      }
    }
    return result;
  }

  public static List<Apple> filterApplesByWeight(List<Apple> inventory, int weight) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
      if (apple.getWeight() > weight) {
        result.add(apple);
      }
    }
    return result;
  }
```

## 2.2 동작 파라미터화     
위의 코드를 보면 변화하는 요구사항에 좀 더 유연하게 대응하는 방법이 절실하다. 그렇기 때문에 참 또는 짓을 반환하는 함수 프레디케이트 인터페이스를 정의하자.     
```
// 이런식으로 인터페이스 test()메서드를 구현하고, 객체를 집어넣으면
// ApplePredicate 인터페이스에 여러버전의 요구사항에 맞는 메서드를 구현할 수 있다.
// 이것을 전략 패턴이라고 한다. ApplePredicate는 사과 전략을 캡슐화한다.
 interface ApplePredicate {
    boolean test(Apple a);
  }

  static class AppleWeightPredicate implements ApplePredicate {
    @Override
    public boolean test(Apple apple) {
      return apple.getWeight() > 150;
    }
  }

  static class AppleColorPredicate implements ApplePredicate {
    @Override
    public boolean test(Apple apple) {
      return apple.getColor() == Color.GREEN;
    }
  }

  static class AppleRedAndHeavyPredicate implements ApplePredicate {
    @Override
    public boolean test(Apple apple) {
      return apple.getColor() == Color.RED && apple.getWeight() > 150;
    }
  }
```

```
// 위 인터페이스를 이용해 유연하게 구현이 가능하다
  public static List<Apple> filter(List<Apple> inventory, ApplePredicate p) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
      if (p.test(apple)) {
        result.add(apple);
      }
    }
    return result;
  }
무거운 사과도 나오고, 녹색 사과도 나온다. 파라미터에 ApplePredicate p 만 넣어주면 된다. 한개의 파라미터,다양한 동작이 되는 것이다.
```
## 2.3 익명 클래스     
다음으로는 익명 클래스이다. 익명 클래스는 자바의 지역 클래스와 비슷한 개념이다. 말 그대로 이름이 없느 ㄴ클래스이다. 익명 클래스를 사용하면 클래스 선언과 인스턴스화를 동시에 할 수 있다. 즉 , 즉석에서 필요한 구현을 만들어서 사용할 수 있다.       

```
// 익명 클래스를 이용해 ApplePredicate를 구현하는 객체를 만드는 방법으로 필터링예제를 구현한 코드이다.
  List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple){
      return RED.equals(apple.getColor());
    }
  });
```

## 2.3.3 람다 표현식 사용    
```
//람다를 이용해 위 예제코드를 간단하게 재구현 가능하다.
 List<Apple> redApples = filterApples(inventory, (Apple apple) -> RED.equals(apple.getColor()));
```


앞서 설명했던 클래스,익명클래스,람다 방식을 이용해서 좀 더 유연하게 요구사항을 수용이 가능한 동작 파라미터화를 구현했다.    
제일 처음에 요구사항별로 하나하나 씩 구현했던 값 파라미터화 방식은 뻣뻣하고 간결하지 못하다.   
 
## 실전 예제     

무게가 적은 순서로 사과를 정렬해보자      
```
// 요구사항이 생기면 그거에 맞는 Comparator를 만들어 sort메서드에 전달할 수 있다.
inventory.sort(new Comparator<Apple>(){
  public int compare(Apple a1,Apple a2){
    return a1.getWeight().compareTo(a2.getWeight());
  }
});

// 람다 이용
inventory.sort((Apple a1,Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

## 마치며     
- 동작 파라미터화에서는 메서드 내부적으로 다양한 동작을 수행할 수 있도록 코드를 메서드 인수로 전달한다.-   
- 동작 파라미터화를 이용하면 변화하는 요구사항에 더 잘 대응할 수 있는 코드를 구현할 수 있으며 나중에 엔지니어링 비용을 줄일 수 있다.    
- 코드 전달 기법을 이용하면 동작을 메서드의 인수로 전달할 수 있다. 자바 8 에서는 인터페이스를 상속받아 여러 클래스를 구현해야 하는 수고를 없앨 수 있는 방법을 제공한다.    
