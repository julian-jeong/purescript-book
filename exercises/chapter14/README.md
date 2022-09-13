# Domain-Specific Languages

이번 장에는 여러 표준 기술을 사용하여 domain-specific languages(DSL)의 구현을 탐구한다.

**DSL?**

- 특정 도메인 개발에 적합한 언어.
- syntax와 function은 코드의 가독성을 극대화하도록 선택함.
- 이미 이전 챕터에서 도메인별 언어의 예를 보았음
  - chapter 11 The `Game` monad : _text adventure game development_ 도메인 언어를 구성함.
  - chapter 13 The `quickcheck` package : _generative testing_ 도메인 언어를 구성함.

## A HTML Data Type

HTML 라이브러리 가장 기본 버전은 `Data.DOM.Simple`모듈에 정의되어 있음.

```haskell
newtype Element = Element
  { name         :: String
  , attribs      :: Array Attribute
  , content      :: Maybe (Array Content)
  }

data Content
  = TextContent String
  | ElementContent Element

newtype Attribute = Attribute
  { key          :: String
  , value        :: String
  }
```

- Element type : HTML elements를 나타냄. 각 요소는 name, attribs, content로 구성됨. content 속성은 `Maybe` type을 이용해 열려 있거나 닫혀 있음을 나타냄.

- 아래 함수는 HTML element를 문자열로 렌더링함. 라이브러리의 핵심 기능은 이것임.

```haskell
render :: Element -> String
```

하지만..! 세상에 완벽한 것은 없다... 이 라이브러리에도 문제가 있는데...!(두둥)

- HTML documents를 만드는 것이 어렵고..! 모든 새로운 element에 최소 하나의 record와 data constructor가 필요함.
- 유효하지 않은 documents를 보여줄 수 있음
  - 개발자가 이름을 잘못 입력할 수 있즤..?
  - 개발자가 속성을 잘못된 element type와 연결할 수 있지...?
  - '' 개발자는 open element가 정확할 때 closed element를 사용할 수 있음(?)

## Smart Constructors

이 미덥지 않은 사용자에게 데이터 표현을 노출하는 대신 module exports list를 사용해 `Element`, `Content` and `Attribute`의 데이터 생성자를 숨기고 믿을 수 있는!(a.k.a 올바른 생성자) *smart constructors*를 내보내 element를 사용할 수 있도록 한다.

예시

1. HTML element를 생성하기 위해 편의 기능을 제공함.

```haskell
element :: String -> Array Attribute -> Maybe (Array Content) -> Element
element name attribs content = Element
  { name:      name
  , attribs:   attribs
  , content:   content
  }
```

2. `element` 함수를 적용해 사용할 HTML element smart constructors를 만듬.

```haskell
a :: Array Attribute -> Array Content -> Element
a attribs content = element "a" attribs (Just content)

p :: Array Attribute -> Array Content -> Element
p attribs content = element "p" attribs (Just content)

img :: Array Attribute -> Element
img attribs = element "img" attribs Nothing
```

3. 올바른 데이터 생성자 함수를 내보낸다. 그 함수를 이용해 element를 업데이트 한다.

```haskell
module Data.DOM.Smart
  ( Element
  , Attribute(..)
  , Content(..)

  , a
  , p
  , img

  , render
  ) where
```

Note, 기본 데이터 표현은 변경되지 않았기 때문에 `render` function을 변경할 필요 없었음. 이것은 `smart constructors` 이점 중 하나임. 내부 데이터 표현을 외부 API사용자가 인식하는 표현과 분리할 수 있음.(?)

## Phantom Types

```text
> log $ render $ img
    [ src    := "cat.jpg"
    , width  := "foo"
    , height := "bar"
    ]

<img src="cat.jpg" width="foo" height="bar" />
unit
```

이 코드를 봐라. `width`, `height` 값을 문자열을 넣었다..(px또는 백분율 단위 숫자값이 들어가야 함)
이 문제를 해결하기 위해 _phantom type_ argument을 도입할 수 있다.

(AttributeKey 예시라 패스)

## The Free Monad

- API 최종 수정에서 *free monad*를 이용해 `Content` type을 모나드로 변환하고 do notation을 활성화합니다.

이 친구를

```haskell
p [ _class := "main" ]
  [ elem $ img
      [ src    := "cat.jpg"
      , width  := 100
      , height := 200
      ]
  , text "A cat"
  ]
```

이렇게

```haskell
p [ _class := "main" ] $ do
  elem $ img
    [ src    := "cat.jpg"
    , width  := 100
    , height := 200
    ]
  text "A cat"
```

- do notation이 free monad의 유일한 이점은 아님. free monad를 사용하면 monadic 동작의 표현(_representation_)을 해석(_interpretations_)과 분리할 수 있으며, 여러 해석(_multiple interpretations_)을 지원할 수 있음.

- The `Free` monad는 `Control.Monad.Free` 모듈에 `free` 라이브러리에 정의되어 있음.

```text
> import Control.Monad.Free

> :kind Free
(Type -> Type) -> Type -> Type
```

- `Free`의 종류는 type constructor를 argument로 사용하고 다른 type constructor를 반환함.
  = `Free` monad는 모든 `Functor`를 `Monad`로 바꾸는데 사용할 수 있군여!

(예시)

## Interpreting the Monad

## Extending the Language

## Conclusion

이 장에서 표준 기술을 사용해 점진적으로 개선하여 HTML documents 만들기 위한 도메인별 언어를 개발했음.(글쿤)

- *smart constructors*를 이용해 데이터 표현의 세부 사항을 숨기고 사용자가 *correct-by-construction(구성에 따라 수정)*한 문서를 생성할 수 있도록 허용했음.
- 언어의 구문을 개선하기 위해 _user-defined infix binary operator_(사용자 정의 중위 이항 연산자)를 사용했음
- *phantom types*을 사용해 테이터 유형의 추가 정보를 인코딩해서 사용자가 잘못된 값을 입력하는 것을 방지했음
- *free monad*를 사용해 콘텐츠 컬렉션의 배열 표현을(?) do notation을 지원하는 monadic 표현으로 변경. 이 표현을 확장해 새로운 monad 동작을 지원하고 standard monad transformers를 사용해 monad 계산을 해석함.

이 기술은 사용자가 실수하는 것을 방지하거나 도메인별 언어의 구문을 개선함.

## 느낀점

- 최근에 rescript 에서 `HtmlInputElement.setChecked`(input 속성인 checked 값을 변경할 수 있는 함수) 라는 것을 발견했다.
  rescript Html Input Element에서 setChecked가 따로 구현되어 있어서 신기했는데 이 Smart Constructors 부분을 읽으니 setter / getter 를 만들어서 속성에 접근하는 것이 이해가 된다..? OOP...?
- free.. monad...!(?)
