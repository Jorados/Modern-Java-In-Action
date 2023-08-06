# Chapter 4 - 스트림 소개
> 이 장의 핵심 내용
> 1. 스트림이란 무엇인가?
> 2. 컬렉션과 스트림 (차이)
> 3. 내부 반복과 외부 반복
> 4. 중간 연산과 최종 연산


##  4.1 스트림이란 무엇인가?
스트림은 자바 8 API에 새로 추가된 기능이다. 스트림을 이용하면 선언형 ( 즉, 데이터를 처리하는 임시 구현 코드 대신 질의로 표현할 수 있다)으로 컬렉션 데이터를 처리할 수 있다.
또한 스트림을 이용하면 멀티스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬로 처리할 수 있다. 스트림의 병렬 처리는 7장에서 설명한다.

```
// 예제 : 저칼로리의 요리명을 반호나하고, 칼로리를 기준으로 요리를 정렬하는 자바 7 코드다. 이 코드를 자바 8의 스트림을 이용해서 다시 구현할 것이다.

// 자바 7
public static List<String> getLowCaloricDishesNamesInJava7(List<Dish> dishes) {
    List<Dish> lowCaloricDishes = new ArrayList<>();
    for (Dish d : dishes) {
      if (d.getCalories() < 400) {
        lowCaloricDishes.add(d);
      }
    }
    List<String> lowCaloricDishesName = new ArrayList<>();
    Collections.sort(lowCaloricDishes, new Comparator<Dish>() {
      @Override
      public int compare(Dish d1, Dish d2) {
        return Integer.compare(d1.getCalories(), d2.getCalories());
      }
    });
    for (Dish d : lowCaloricDishes) {
      lowCaloricDishesName.add(d.getName());
    }
    return lowCaloricDishesName;
  }

// 자바 8
public static List<String> getLowCaloricDishesNamesInJava8(List<Dish> dishes) {
    return dishes.stream()
        .filter(d -> d.getCalories() < 400)
        .sorted(comparing(Dish::getCalories))
        .map(Dish::getName)
        .collect(toList());
}

// stream을 parallelStream()으로 바꾸면 이 코드를 멀티코어 아키텍처에서 병렬로 실행할 수 있다.
public static List<String> getLowCaloricDishesNamesInJava8(List<Dish> dishes) {
    return dishes.parallelStream()
        .filter(d -> d.getCalories() < 400)
        .sorted(comparing(Dish::getCalories))
        .map(Dish::getName)
        .collect(toList());
}
```
parallelStream()를 호출했을 때 정확히 어떤 일이 일어날까? 얼마나 많은 스레드가 사용되는지? 성능은? 모두 7장에서 알아보는 걸로 하자.

우선 스트림은 filter,sorted,map,collect 같은 여러 빌딩 블록 연산을 연결해서 복잡한 데이터 처리 파이프라인을 만들 수 있다.
filter(또는 sorted,map,collect) 같은 연산은 고수준 빌딩 블록으로 이루어져 있으므로 특정 스레딩 모델에 제한되지 않고 자유롭게 어떤 상황에서든 사용할 수 있다.
결론적으로 우리는 데이터 처리 과정을 병렬화하면서 스레드와 락을 걱정할 필요가 없다. 이 모든것이 스트림 API 덕분이다.

## 4.2 스트림 시작하기   
스트림이란? 정확하게 '데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소'로 정의할 수 있다. 
 - filter : 람다를 인수로 받아 스트림에서 특정 요소를 제외시킨다.
 - map : 람다를 이용해서 한 요소를 다른 요소로 변환하거나 정보를 추출한다. 예제 에서는 메서드 참조를 이용했다. Dish::getName ( 람다 : d -> d.getName()) 을 전달해서 각 요리명을 추출.
 - limit : 정해진 개수 이상의 요소가 스트림에 저장되지 못하게 스트림 크기를 축소한다.
 - collect : 스트림을 다른 형식으로 변환한다.

![image](https://github.com/Jorados/Modern-Java-In-Action/assets/100845256/797d6e53-acc0-402a-b59e-9be3c020ec3b)   

## 4.3 스트림과 컬렉션
자바의 기존 컬렉션과 새로운 스트림 모두 요소 형식의 값을 저장하는 자료구조의 인터페이스를 제공한다.   

컬렉션과 스트림의 차이
 - 데이터를 언제 계산하느냐가 컬렉션과 스트림의 가장 큰 차이다. 컬렉션은 현재 자료구조가 포함하는 모든 값을 메모리에 저장하는 자료구조다. 즉, 컬렉션의 모든 요소는 컬렉션에 추가하기 전에 계산되어야 한다.
 - 반면 스트림은 이론적으로 요청할 때만 요소를 계산하는 고정된 자료구조다. 이러한 특성은 프로그래밍에 큰 도움을 준다.
 - 데이터 반복 처리 방법 ( 내부반복, 외부반복 )

## 4.3.1 딱 한번만 탐색할 수 있다.
반복자와 마찬가지로 스트림도 한 번만 탐색할 수 있다. 즉, 탐색된 스트림의 요소는 소비된다. 반복자와 마찬가지로 한 번 탐색한 요소를 다시 탐색하려면 초기 데이터 소스에서 새로운 스트림을 만들어야 한다.
(그러려면 컬렉션처럼 반복 사용할 수 있는 데이터 소스여야 한다.)

## 4.3.2 외부 반복과 내부 반복
컬렉션 인터페이스를 사용하려면 사용자가 직접 요소를 반복해야한다. 예를 들면 for-each 등등 이를 외부반복이라고 한다.   
반면 스트림 라이브러리는 내부 반복을 사용한다.   

![image](https://github.com/Jorados/Modern-Java-In-Action/assets/100845256/1d01cf9e-47d1-430f-b4d1-768900edf2e9)   

## 4.4 스트림 연산
java.util.stream.Stream 인터페이스는 많은 연산을 정의한다. 스트림 인터페이스의 연산을 크게 두 가지로 구분할 수 있다.   
 - filter,map,limit는 서로 연결되어 파이프 라인을 형성한다.
 - collect로 파이프라인을 실행한 다음에 닫는다.
연결할 수 있는 스트림 연산을 중간 연산이라고 하며, 스트림을 닫는 연산을 최종 연산이라고 한다.

![image](https://github.com/Jorados/Modern-Java-In-Action/assets/100845256/03bbfa57-b195-49bf-9ab9-493524800e09)

```
public static void main(String[] args) {
    List<String> names = menu.stream()
        .filter(dish -> {
          System.out.println("filtering " + dish.getName());
          return dish.getCalories() > 300;
        })
        .map(dish -> {
          System.out.println("mapping " + dish.getName());
          return dish.getName();
        })
        .limit(3)
        .collect(toList());
    System.out.println(names);
  }

// 출력결과
filtering pork
mapping pork
filtering beef
mapping beef
filtering chicken
mapping chicken
[pork, beef, chicken]
```

스트림의 게으른 특성 덕분에 몇 가지 최적화 효과를 얻을 수 있었다. 
 - 첫째, 300칼로리가 넘는 요리는 여러 개지만 오직 처음 3개만 선택되었다. 이는 limit연산과 그리고 '쇼트서킷'(5장) 이라 불리는 기법 덕분이다.
 - 둘째, filter와 map은 서로 다른 연산이지만 한 과정으로 병화되었다.( 루프 기법이라고 한다. )


## 4.4.3 스트림 이용하기   
스트림 이용 과정은 다음과 같이 세 가지로 요약할 수 있다.   
 - 질의를 수행할 (컬렉션 같은) 데이터 소스   
 - 스트림 파이프라인을 구성할 중간 연산 연결  
 - 스트림 파이프라인을 실행하고 결과를 만들 최종 연산.  

스트림 파이프라인의 개념은 빌더 패턴과 비슷하다.   

![image](https://github.com/Jorados/Modern-Java-In-Action/assets/100845256/73adf89d-d5e6-4df7-a1c4-d81d91c70ee8)


## 4.6 마치며
 - 스트림은 소스에서 추출된 연속 요소로, 데이터 처리 연산을 지원한다.
 - 스트림은 내부 반복을 지원한다. 내부 반복은 filter,map,sorted 등의 연산으로 반복을 지원한다.
 - 스트림에는 중간연산과 최종연산이 있다.
 - 증간 연산은 filter와 map처럼 스트림을 반환하면서 다른 연산과 연결되는 연산이다.
 - forEach나 count처럼 스트림 파이프라인을 처리해서 스트림이 아닌 결과를 반환하는 연산을 최종연산이라고 한다.
 - 스트림의 요소는 요청할 때 게으르게 계산된다.
