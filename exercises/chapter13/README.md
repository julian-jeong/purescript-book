# Generative Testing

## Chapter Goals

- 테스트에서 type classes의 우아한 적용을 보게 될 것임. (기대하라구)
- 코드가 어떻게 동작하는지(_how_)를 테스트하는 대신, 코드가 가져야하는 무엇(_what_)을 테스트한다.
- `generative testing` : type classes를 이용해 렌덤 데이터를 생성하는 boilerplate 코드를 숨기고, 테스트 케이스를 무작위로 생성하는 것. Haskell의 QuickCheck 라이브러리에서 사용하는 기술이다.

## Writing Properties

- `Merge` 모듈은 `quickCheck` 라이브러리의 기능을 시연하는데 사용항 `merge` function 을 구현합니다.

```haskell
merge :: Array Int -> Array Int -> Array Int
```

- `merge`는 두 개의 정렬된 int array를 받고 결과도 정렬 시킨다.

```text
> import Merge
> merge [1, 3, 5] [2, 4, 5]

[1, 2, 3, 4, 5, 5]
```

- `merge`function은 `xs` 와 `ys` 가 정렬된 경우 `merge xs ys`는 합쳐진 두개의 정렬된 값을 리턴한다.

- `quickCheck` 를 사용해 무작위 케이스를 만들고 이 속성을 테스트할 수 있음.

```haskell
main = do
  quickCheck \xs ys ->
    eq (merge (sort xs) (sort ys)) (sort $ xs <> ys)
```

## Improving Error Messages

- `<?>` : failed test case와 함께 에러 메세지를 제공하기 위해 사용하는 `quickcheck` operator. 에러 메세지와 속성 정의를 분리해서 사용

```haskell
quickCheck \xs ys ->
  let
    result = merge (sort xs) (sort ys)
    expected = sort $ xs <> ys
  in
    eq result expected <?> "Result:\n" <> show result <> "\nnot equal to expected:\n" <> show expected
```

## Testing Polymorphic Code

- `Merge` 모듈은 `mergePoly`라고하는 `merge` function 일반화를 정의함(?)
- `mergePoly` : 숫자 배열뿐만 아니라 `Ord` type class에 속하는 모든 유형의 배열에 쓸 수 있음.

```haskell
mergePoly :: forall a. Ord a => Array a -> Array a -> Array a
```

- `merge` 대신 `mergePoly`를 쓰도록 테스트를 수정하면 에러가 뜬다.

## Generating Arbitrary Data
