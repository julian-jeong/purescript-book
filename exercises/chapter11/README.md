# Chapter 11. Monadic Adventures

---

## Alternatives

`control`패키지는 실패할 수 있는 계산 작업의 추상화를 정의합니다. `Alternatives` type class는 그 중 하나입니다.

```haskell
class Functor f <= Alt f where
  alt :: forall a. f a -> f a -> f a

class Alt f <= Plus f where
  empty :: forall a. f a

class (Applicative f, Plus f) <= Alternative f
```

`Alternatives`는 두 가지 새로운 결합을 제공

- `empty` : 실패한 계산에 대한 프로토타입을 제공
- the `alt` function (and its alias, `<|>`) : 에러가 발생한 경우 alternative 계산으로 폴백

`Data.Array`모듈은 `Alternatives` type class에서 유형 생성자를 사용하는데 유용한 두 가지 함수를 제공

- many : `Alternatives` type class를 사용해 0번 이상 반복적으로 계산을 실행함
- some : many와 비슷하지만 성공하려면 최소한 첫 번째 계산이 필요함

  `Data.List`에 해당하는 `some`, `many`도 있음

## Monad Comprehensions 모나드 이해..(저는 아직 멀었습니다)

`Control.MonadPlus`모듈은 `MonadPlus`라는 이름의 `Alternative` type class를 하위 클래스로 정의함.

`MonadPlus`는 모나드이자 `Alternative` 의 인스턴스인 type constructors를 캡쳐한다(?)

`guard`함수는 일반적이며 `MonadPlus`의 인스턴스인 모든 모나드에서 사용할 수 있음

```haskell
guard :: forall m. Alternative m => Boolean -> m Unit
```

## Backtracking

`<|>`를 사용하면 실패시 역추적할 수 있음

## The RWS Monad

모나드 변환기의 특정 조합은 일반적이라 `transformers` package에 같이 제공됨

`RWS` monad :`Reader`, `Writer`, `State` 모나드를 _reader-writer-state_ monad라고 부르고 더 간단히 `RWS` monad라고 부름

## Implementing Game Logic

## Running the Computation

## Handling Command Line Options

## Conclusion
