# Ch.9 Asynchronous Effects👻

- `Effect` monad와 유사하지만 비동기 side-effects 를 내는 `Aff` monad를 알아봅시다
- 비동기를 병렬적으로 다루는 방법도 다룰 것입니다.

<br />

## Asynchronous JavaScript

- 자바 스크립트에서는 `async` / `await` 를 이용해 비동기를 편리하게 작업합니다.

  (자세한 배경이 궁금한가요? 👉🏻[this article on asynchronous JavaScript](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Introducing))

- 단점
  - 콜백은 "Callback Hell", "Pyramid of Doom" 과 같은 과도한 중첩으로 이어져요
  - 동기 코드는 다른 함수의 실행을 차단합니다

<br />

## Asynchronous PureScript

- 자바스크립트의 `async` / `await` 와 비슷한 `Aff` monad를 퓨어스트립트에서 사용합니다
- `Aff`는 monad이기 때문에 do notation 사용할 수 있어요

<details>
<summary>예제🥹</summary>

```hs
{{#include ../exercises/chapter9/test/Copy.purs:copyFile}}
```

It is also possible to re-write the above snippet using callbacks or synchronous functions (for example with `Node.FS.Async` and `Node.FS.Sync` respectively), but those share the same downsides as discussed earlier with JavaScript, and so that coding style is not recommended.

The syntax for working with `Aff` is very similar to working with `Effect`. They are both monads, and can therefore be written with do notation.

For example, if we look at the signature of `readTextFile`, we see that it returns the file contents as a `String` wrapped in `Aff`:

```hs
readTextFile :: Encoding -> FilePath -> Aff String
```

We can "unwrap" the returned string with a bind arrow (`<-`) in do notation:

```hs
my_data <- readTextFile UTF8 file1
```

Then pass it as the string argument to `writeTextFile`:

```hs
writeTextFile :: Encoding -> FilePath -> String -> Aff Unit
```

The only other notable feature unique to `Aff` in the above example is `attempt`, which captures errors or exceptions encountered while running `Aff` code and stores them in an `Either`:

```hs
attempt :: forall a. Aff a -> Aff (Either Error a)
```

You should hopefully be able to draw on your knowledge of concepts from previous chapters and combine this with the new `Aff` patterns learned in the above `copyFile` example to tackle the following exercises:

</details>

<br />

## Additional Aff Resources

<details>
<summary>Aff Resources 링크</summary>

- [official Aff guide](https://pursuit.purescript.org/packages/purescript-aff/)
- [Drew's Aff Post](https://blog.drewolson.org/asynchronous-purescript)
- [Additional Aff Explanation and Examples](https://github.com/JordanMartinez/purescript-jordans-reference/tree/latestRelease/21-Hello-World/02-Effect-and-Aff/src/03-Aff)

</details>

<br />

## A HTTP Client

- `affjax` library : `Aff`를 이용한 AJAX HTTP requests을 편하게 해준다.
- 환경에 따라 [purescript-affjax-web](https://github.com/purescript-contrib/purescript-affjax-web) 또는 [purescript-affjax-node](https://github.com/purescript-contrib/purescript-affjax-node)을 사용해야합니다.

<br />

## Parallel Computations

- `Aff`을 사용하면 비동기 계산을 병렬으로 사용할 수 있다.
- `parallel` 패키지에 type class `Parallel`는 `Aff` 와 같은 모나드의 병렬 계산을 지원함.

서술과 실행의 분리<- 밑줄쫙 별표두개
