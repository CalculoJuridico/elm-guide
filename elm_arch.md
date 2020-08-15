# A Arquitetura Elm MVU

## Visão Geral

A [arquitetura Elm](https://guide.elm-lang.org/architecture/) segue o paradigma Model-View-Update. Este diagrama representa esse ciclo contínuo:

![elm architecture](images/elm_arch.png)


Para ver como fica o código vamos tomar como exemplo um formulário com dois campos representado. O modelo consiste em um type alias e um valor constante init.

```elm
-- MODEL

type alias Model =
  { name : String
  , password : String
  }

init : Model
init =
    { name = ""
    , password = ""
    }
```


Já a função update tem uma assinatura mais complexa. Ela recebe como primeiro argumento a Message (Msg) da interação com o app, como segundo argumento o estado atual do modelo e retorna o estado final do modelo.


```elm
-- UPDATE

type Msg
  = Name String
  | Password String

update : Msg -> Model -> Model
update msg model =
  case msg of
    Name name ->
      { model | name = name }

    Password password ->
      { model | password = password }
```

```elm
-- VIEW

view : Model -> Html Msg
view model =
  div []
    [ viewInput "text" "Name" model.name Name
    , viewInput "password" "Password" model.password Password
    ]

viewInput : String -> String -> String -> (String -> msg) -> Html msg
viewInput t p v toMsg =
  input [ type_ t, placeholder p, value v, onInput toMsg ] []
```

Mas o que é exatamente esse Html Msg?

## O tipo Html Msg
É apenas um tipo customizado, porém causa algumas dúvidas quando estamos aprendendo elm, vamos explorar um pouco como esse tipo funciona para tentar esclarecer esse assunto.

Vamos começar com algo simples e depois algo mais próximo do real.

### [Inicial](https://ellie-app.com/9HXCRnKWKpWa1)

Nesse exemplo tanto `Model` quanto `Msg` são aliases para `Int` e assim podemos ver que a `Msg` em `Html Msg` é de fato um tipo customizado cuja variante `Html` possui dados associados.

Algo como:
```elm
type Html a =
  Html a
```

Dessa forma vemos que a `Msg` retornada por uma view e utilizada nas outras partes da aplicação pode assumir qualquer valor, desde que mantenha a consistência.

As vantagens de utilizar um tipo customizado estão na possibilidades de armazenar tanto uma ação quanto os dados relacionados a ela e ainda poder contar com as garantias fornecidas pelo compilador do Elm ao fazer um `pattern match`.

Veja o que acontence ao receber uma mensagem após o usuário clicar em salvar em um formulário "ClickedSave" se utilizarmos uma String como `Msg`:

```elm

type alias NaiveMsg = String

-- 

case naiveMsg of

  "ClickedSave" ->
    -- lógica para salvar

    {--  como essa não é a única String possível e para utilizarmos um case of
     --  precisamos cobrir todas os casos teríamos que adicionar algo para lidar
     --  com todas as demais Strings
     --}

  _ ->

   {-- aqui lidamos com todas as demais Strings porém destruímos todas as garantias
    -- que o compilador nos dá. Caso tívessemos escrito errado ou esquecido de lidar com uma mensagem
    -- cairíamos aqui.
    --}
```

Agora veja o que acontece com um tipo customizado:

```elm
type Msg = ClickedSave

case msg of

  ClickedSave ->
    -- lógica para salvar

    {-- e acabou pois o compilador sabe que essa é a única possibilidade
     -- mas caso existam outras possibilidades você será obrigado a "lidar" com todas elas
     -- caso contrário receberá um erro de compilação.
     --}
```

Nesse exemplo tirado do tutorial oficial do elm você pode notar como utilizar um tipo customizado como `Msg` é útil ao olhar o método `update`.
[Final](https://elm-lang.org/examples/buttons)

Para concluir, se você pensar em `Html a`, ou seja `Html` com um tipo qualquer para a mensagem, vai entender melhor o que está acontecendo.

## Variações da arquitetura Elm

A arquitetura Elm está disponível em algumas variações:

### Sandbox

TODO

```elm
main =
  Browser.sandbox { init = init, update = update, view = view }
```


### Element

TODO

```elm
main =
  Browser.element
    { init = init
    , view = view
    , update = update
    , subscriptions = subscriptions
    }
```

### Application

TODO

```elm
main : Program () Model Msg
main =
  Browser.application
    { init = init
    , view = view
    , update = update
    , subscriptions = subscriptions
    , onUrlChange = UrlChanged
    , onUrlRequest = LinkClicked
    }
```


