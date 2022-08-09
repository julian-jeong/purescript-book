# Ch.6 Type Classes
type classes : PureScript's type system을 기반으로 한 강력한 추상화 형식

**🚨매우 다름 주의🚨**
- 객체 지향에서 사용하는 class와 다름
- interface와 비슷

<br />

## Show Me!😎
---
- `show`함수 : 값을 문자열로 표시
  - type class `Show`선언
  - `Show`는 `a`변수에 의해 매개변수화(parameterized)
    ```haskell
    class Show a where
      show :: a -> String
    ```

  <br />

- type class instance에는 type class에서 정의된 함수 구현이 포함됨
-> instance는 type class의 구현 내용이 포함됨

  - `Boolean`값에 대한 `Show` type class의 instance
  - `Boolean` type는 `Show`type class에 포함됨
    ```haskell
    instance showBoolean :: Show Boolean where
    show true = "true"
    show false = "false"
    ```

  <br />

- Type class instances 다른 곳에서 정의할 수 있음
  >1. type class가 정의된 모듈과 같은 위치
  >2. type class가 속하는 type이 정의된 동일한 모듈(..어디죠 거기가?)
  
  <br />

  그 외 장소에 정의된 instance = orphan instance
    <br />
  PureScript compiler님이 절대로 통과 안시켜줌⛔️

<br />

## Common Type Classes
---
관용적 PureScript 코드에서 잘 쓰이는 추상화 패턴의 기초를 형성하므로 아래 type class는 이해하고 가세용
  
  <br />

  ### Eq
  - type class `Eq`은 `eq`함수를 정의함
  - 두 개의 값이 같은지 테스트함
  - `==`은 `eq`의 alias
  - 두 인자는 같은 타입
    ```haskell
    class Eq a where
      eq :: a -> a -> Boolean
    ```

  ### Ord
  - type class `Ord`은 `compare`함수를 정의함
  - 두 값을 비교함
  - 순서를 지원하는 type에 사용함
  - compare operator은 `compare`을 이용함
    `<`, `>` / `<=`, `>=`(non-strict) 

    ```haskell
    data Ordering = LT | EQ | GT

    class Eq a <= Ord a where
      compare :: a -> a -> Ordering
    ```

    `compare` 함수는 두 값을 비교해 `Ordering`을 return
    - `LT` : 첫 번째가 두 번째보다 작다 less
    - `EQ` : 같다 equal
    - `GT` : 첫 번째가 두 번째보다 크다 greater
  
  ### Field
  - type class `Field`는 numeric operators(`+`, `-`, `*`, `/`)을 식별함
  - 재사용할 수 있도록 해당 연산자를 추상화하기 위해 제공함(?)
  - `Eq`, `Ord` type classes 처럼 `Field` type class는 ✨PureScript compiler님의 특별 지원✨을 받아 `1 + 2 * 3` 와 같은 간단한 표현은 간단한 JavaScrip로 번역됨
  - type class implementation으로 dispatch하는 함수와 반대임

    ```haskell
    class EuclideanRing a <= Field a
    ```
  - type class `Field`는 일반적인 _superclasses_ 들의 조합임


  ### Semigroups and Monoid
  - Semigroups
    - type class `Semigroup`은 `append` operation을 지원함
    - 두 개의 값을 결함함
    - `append` alias : `<>` 

      ```haskell
      class Semigroup a where
        append :: a -> a -> a
      ```
  - Monoid
    - type class `Monoid`는 `mempty`라고 하는 빈 값의 개념
    - type class `Semigroup`를 확장함
    ```haskell
    class Semigroup m <= Monoid m where
      mempty :: m
    ```
  - strings, Array는 monoids의 간단한 예임
    - 빈 값으로 시작해 새 결과를 결합하여 어떻게 누적하는지 설명함
    ```haskell
    > foldl append mempty ["Hello", " ", "World"]
    "Hello World"
    ```

  ### Foldable
  - type class `Monoid`가 fold를 식별
  - type class `Foldable`는 fold의 식별자로 사용할 수 있는 type constructors 를 식별함
  - type class `Foldable`는`foldable-traversable` 패키지에 포함됨 (여기에는 arrays, `Maybe`도 있음 _알찬 구성! 놀라워!_)
  - type signatures _복잡할 예정_
    ```haskell
    class Foldable f where
      foldr :: forall a b. (a -> b -> b) -> b -> f a -> b
      foldl :: forall a b. (b -> a -> b) -> b -> f a -> b
      foldMap :: forall a m. Monoid m => (a -> m) -> f a -> m
    ```
    ...일단 후퇴🫡

  ### Functor, and Type Class Laws
  - `Functor`, `Applicative`, `Monad` : PureScript에서 함수형 프로그래밍 스타일로 side-effects를 처리할 수 있는 type classes의 집합
  - `map` 함수 (alias :  `<$>`)
    - map 함수를 사용하면 함수를 데이터 구조에서 들어올릴 수 있음
    - map함수는 컨테이너의 각 요소에 주어진 함수를 적용하고 원본과 동일한 모양으로 새로운 컨테이너를 빌드한다.
  ```haskell
  class Functor f where
    map :: forall a b. (a -> b) -> f a -> f b
  ```
  - `Functor`의 type class instances는 _functor laws_ 를 따름
    1. _identity law_ 
      - `map identity xs = xs` 
      - identity 함수를 구조 위로 lifting. 
      - 원래 구조를 그대로 반환한다. 입력을 수정하지 않는다..(?)
    2. _composition law_
      - `map g (map f xs) = map (g <<< f) xs`
      - 첫 번째 함수를 구조에 매핑한 다음 두 번쨰 함수 매핑
      - 두 개의 함수를 매핑하는 것과 동일(?)

  ### Deriving Instances
  - instances를 수동으로 작성하는 대신 ✨compiler님이✨ 대부분의 작업을 수행하도록 할 수 있음
  - [Type Class Deriving guide](https://github.com/purescript/documentation/blob/master/guides/Type-Class-Deriving.md)

<br />

## Type Class Constraints
---
type classes를 이용해 함수 타입을 제한할 수 있음.

예시
- `Eq` type class instance
- `=>`를 사용해 `a`가 `Eq`의 instance임을 명시
```haskell
threeAreEqual :: forall a. Eq a => a -> a -> a -> Boolean
threeAreEqual a1 a2 a3 = a1 == a2 && a2 == a3
```
## Instance Dependencies

## Multi Parameter Type Classes

## Functional Dependencies

## Nullary Type Classes

## Superclasses

## A Type Class for Hashes