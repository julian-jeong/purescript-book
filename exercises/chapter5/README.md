# Ch.5 Pattern Matching

## Chapter Goals

### new concepts : algebraic data types, pattern matching
### row polymorphis

- Pattern matching : FP에서 일반적으로 사용하는 기술. 복잡한 조건을 간단하게 작성할 수 있도록 도와줌

- Algebraic data types (대수자료형) : 패턴 매칭과 밀접하게 관련있음

## Project Setup(?)
- _qualified name_ 을 이용해서 간단하게 사용할 수 있도록 한다.  `Number.max` 와 같이 사용하면 imports overlapping을 피할 수 있음

- 정규화된 imports를 위해 `import Data.Number as N` 와 같이 사용할 수 있다. 

## Simple Pattern Matching
Euclidean Algorithm 예시

장점 : 각 case에 따라(만족하는) return 되는 값을 명시적으로 작성할 수 있다. 
```
gcd :: Int -> Int -> Int
gcd n 0 = n
gcd 0 m = m
gcd n m = if n > m
            then gcd (n - m) m
            else gcd n (m - n)
```

```
const gcd = (n, m) => {
1. if (m === 0) return n;
2. if (n === 0) return m;
3. if (n > m) return gcd (n - m) m;
4. return gcd n (m - n)
}
```

## Simple Patterns
- Variable patterns : 인자에 이름을 바인딩함
- `Number`, `String`, `Char` and `Boolean` literals
- *Wildcard* : patterns indicated with an underscore (`_`)

## Guards
- boolean 값
- character (`|`) 으로 구분함
```
gcdV2 :: Int -> Int -> Int
gcdV2 n 0 = n
gcdV2 0 n = n
gcdV2 n m | n > m     = gcdV2 (n - m) m
          | otherwise = gcdV2 n (m - n)
```
- extra condition : case에 부합했을 때 case(패턴)에 추가할 수 있음
- final line에 사용하는 `otherwise`은 키워드처럼 보일 수 있지만 실제로는 true 의 별칭임

## Array Patterns
- _Array literal patterns_ provide a way to match arrays of a fixed length 
고정된 길이의 array를 _Array literal patterns_를 통해 매칭할 수 있음

- `isEmpty` 예시 
```
isEmpty :: forall a. Array a -> Boolean
isEmpty [] = true // 첫번째 케이스에서 [] 패턴을 이용해 isEmpty를 수행할 수 있음
isEmpty _ = false
```

- array 리터럴을 사용하면 고정 길이의 배열을 매칭할 수 있지만 PureScript는 길이가 지정되지 않은 배열의 패턴 매칭은 제공하지 않는다
이유는 성능이 저하될 수 있기 때문

- 이러한 상황에는 `Data.List`을 추천함. 다른 자료 구조가 더 개선된 성능을 제공함
- 임의 접근이 필요할 때 array를쓰고 순차접근을 위해서는 list를 쓴다.

## Record Patterns and Row Polymorphism
- 레코드 패턴은 레코드를 일치시키는데 사용됨
- 레코드 패턴은 레코드 리터럴처럼 보이지만 `:` 오른쪽에 있는 값 대신 각 필드의 바인더를 지정한다는 점이 다름

```
showPerson :: { first :: String, last :: String } -> String
showPerson { first: x, last: y } = y <> ", " <> x
```
- first는 x에 last는 y에 바인딩
- 레코드 패턴은 PureScript의 타입 시스템의 _row polymorphism_의 좋은 예를 제공

- ...뒤에 예시 이해안됨..

## Record Puns
- _record pun_: 필드 이름만 지정하고 value의 이름은 지정할 필요 없음
```
showPersonV2 :: { first :: String, last :: String } -> String
showPersonV2 { first, last } = last <> ", " <> first
```

## Nested Patterns
- 패턴은 임의로 중첩될 수 있음
```
sortPair :: Array Int -> Array Int
sortPair arr@[x, y]
  | x <= y = arr
  | otherwise = [y, x]
sortPair arr = arr
```

## Named Patterns
- Nested Patterns을 사용할 때 패턴에 이름을 지정할 수 있음. 
- `@` symbol을 사용함

## Case Expressions
- 패턴은 top-level 함수 선언에서만 사용할 수 있는 것 아님.
- `case` 표현식 이용해서 계산 중간에 패턴 매칭을 할 수 있음.
- `case` 표현식은 익명 함수와 유사함
```
lzs :: Array Int -> Array Int
lzs [] = []
lzs xs = case sum xs of
           0 -> xs
           _ -> lzs (fromMaybe [] $ tail xs)
```

## Pattern Match Failures and Partial Functions
- 패턴 매칭이 실패한 경우 런타임에서 뻗음
```
partialFunction :: Boolean -> Boolean
partialFunction = unsafePartial \true -> true
```
- total function: 모든 입력 조합에 대한 값을 반환하는 함수
- partial function: total function과 반대

- 가능한 경우 total function으로 정의하는 것이 좋음
- 불가능한 경우 `Nothing`을 사용해 `Maybe a`로 실패를 나타대는 값을 반환하는 것이 좋음
- PureScript 컴파일러는 불완전한 패턴 매칭일 경우 에러를 발생시킴
- 위 에러는 `unsafePartial`함수로 숨길 수 있음. 단 `partial function`이 안전하다는 확신이 있는 경우에만 사용
- PureScript는 type system을 통해 partial functions를 추척한다
- partial function가 안전하다는 사실을 type checker에게 명시적으로 알려야함
- 컴파일러는 케이스가 중복되는 경우에도 경고 날림

## Algebraic Data Types (ADTs)
- ADTs(대수 자료 유형)은 기본적으로 패턴 매칭과 관련이 있음.
- 선, 직사각형, 원, 텍스트 등 간단한 모양을 정의할 때 OOP에서는 interface 혹은 abstract class `shape` 와 각 유형에 대한 구체적인 하위 클래스를 정의해야함
- 추상 클래스 `shape`을 사용하려면 subclass를 정의하고 실행하고자 하는 작업을`shape` 인터페이스에서 정의해야함. 이 방법은 모듈성을 훼손하지 않고 새로운 작업을 추가하는 것이 어려움

- (대충 멋진 등장)*Algebraic data types*은 type-safe한 방식으로 이러한 어려움을 해결할 수 있음
- 모듈 방식으로  `shape`에 대한 새 작업을 정의하고 type-safety
성을 유지할 수 있음
```
data Shape
  = Circle Point Number
  | Rectangle Point Number Number
  | Line Point Point
  | Text Point String
```
- `An algebraic data type(대수적 테이터 유형)`은 `data`키워드를 사용함
- 그 뒤에 새로운 유형의 이름과 유형의 인수가 들어감
- type 생성자는 pipe characters (`|`)로 구분됨
`data Maybe a = Nothing | Just a`
- !!`forall` 구문은 함수에 필요하지만 `data`의 타입 aliases를 정의할 때는 사용하면 안됨

-  rescript에서 type shape = Circle | Rectangle |...으로 선언하고 패턴 매칭에서 사용하는 방식과 비슷한 것 같습니다...!

## Using ADTs
- ADTs 생성자를 함수처럼 사용하고 적절한 인수를 넘겨주는 방식으로 사용한다.

## Newtypes

## 소감
- 패턴 매칭 그 찬란하고 아름다운 세계로...


low...어쩌구는
OOP에서 서브 어쩌구를하면 데이터구조가 유지가 안되는데 row어쩌구를 쓰면 인풋 데이터 구조를 유지한다.

리스크립트는 모든게 익스프레션이다 표헌식이다

unbox와 리스크립트의 newtype과 비슷

@ -> as _ 등등

대수 그린랩스 블로그글 + 효은님 블로그글...

newtype 현실 세계의 int를 컴퓨팅 언어로 바꿔서 사용할 수 있게하는것

현실에서는 int가 다양한 사용을한다. 100은 온도일수도있고 경도일수도있다.

등등 그것을 newtype 경도의 int로 만들수있다

로그인할때 아이디도 비밀번로도 스티링인데 너무 모호하지않으냐

Password(string), Name(string) 

논리학

일리미네이션 - 분해

타입 시스템과 연관되어있는 논리학





