## Ch4. Recursion, Maps And Folds
재귀 함수를 사용하여 알고리즘을 구성하는 방법을 살펴본다.

---

### Introduction
- 재귀는 변경 가능한 상태를 줄이는 데 도움이 된다.
- 재귀는 함수형 프로그래밍에 기본 기술이다.
- divide and conquer(분할 정복) 전략과 매우 가까운 방법이다.
- 문제를 해결하기 위해 input을 작은 단위로 분해하고 해결한 결과를 조합하는 방법

<br />

### Recursion on Arrays
- 배열의 길이를 계산하는 함수: 입력값이 비어있지 않은지 여부에 따라 분기 처리
- fromMaybe 함수 (default value, Maybe)를 인자로 받는다.
- Nothing을 return하면 `default` value, 다른 케이스를 반환하면 `Just` value

  ~~Nothing을 반환하면 default value 다른 케이스가 나오면 계산한 값을 반환한다...?~~

<br />

### Maps
- it changes the contents of the array, but preserves its shape (i.e. its length).

  **배열의 content는 변하지만 shape은 유지한다.**

- the map function is an example of a more general pattern of shape-preserving functions which transform a class of type constructors called functors.

  - map은 유형 생성자의 클래스를 변환하는 모양 보전 함수보다 일반적인 패턴의 예

  - functor가 무엇인가,,,!

<br />

### Infix Operators
- backticks을 이용해 함수를 중위어로 만들 수 있다. 일반적으로 두 개의 인수가 있는 함수에 적합하다.
- map function when used with arrays, called `<$>`

```
> (\n -> n + 1) `map` [1, 2, 3, 4, 5]
[2, 3, 4, 5, 6]
```

<br />

### Filtering Arrays
- 조건과 일치하는 요소를 유지하고 새로운 배열을 반환하는 `filter`를 제공한다. 
```
> import Data.Array

> filter (\n -> n `mod` 2 == 0) (1 .. 10)
[2,4,6,8,10]
```

<br />

### Flattening Arrays
- 배열의 배열을 단일 배열으로 병합하는 `concat`을 제공한다.
```
> import Data.Array

> :type concat
forall a. Array (Array a) -> Array a

> concat [[1, 2, 3], [4, 5], [6]]
[1, 2, 3, 4, 5, 6]
```
-  `concat`+ `map` = `concatMap` 
- `map`, `filter`,`concatMap` 배열에 대한 전체 기능 범위의 기초를 형성한다. => 배열과 관련된 모든 기능의 기초를 이룬다..!

<br />

### Array Comprehensions
- 배열 이해하기

<br />

### Do Notation
- `concatMap`, `map`는 do notation이라는 특수 구문의 기초를 형성한다.

  notice: `map`, `concatMap`이 array comprehensions을 작성하게 해줌. `map`, `bind`가 monad comprehensions를 작성하게 해줄것
- do introduces a block of code which uses do notatio
  - 오른쪽에 `<-` 을 이용해 array에 바인딩. 왼쪽에는 이름
  - 배열에 바인딩하지 않는 표현식이 있다. 마지막 줄에 do의 결과를 `pure [i, j]` 이 형태로 표현한다.
  - `let` keyword를 이용해서 표현식에 이름을 지정할 수 있다.

<br />

~~Guards~~

~~Folds~~

### Tail Recursion
- input이 너무 클 때 stack overflow가 발생할 수 있다.


~~Accumulators~~

Prefer Folds to Explicit Recursion

A Virtual Filesystem

Listing All Files

Conclusion