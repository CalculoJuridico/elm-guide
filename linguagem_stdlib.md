# A Linguagem e a biblioteca padrão

## Tipos básicos
A [seção correspondente do guia oficial](https://guide.elm-lang.org/types/) explica quais são os tipos básicos. A maioria dos tipos básicos não surpreende muito, como Int, Float, Char, String. Os tipos Int e Float em conjunto são chamados de number. É importante também prestar atenção na notação para Char que usa aspas simples 'a' da notação para String que usa aspas duplas "abc".

Normalmente não é necessário declarar os tipos expressamente pois o compilador já os determina automaticamente:

```elm
> 1 + 1
2 : number
> "abc"
"abc" : String
> 'a'
'a' : Char
```


## Listas
A forma mais comum de trabalhar com listas (encadeada) é com esta notação:

```elm
> [1, 2, 3]
[1,2,3] : List number
```

Mas uma outra notação importante é a seguinte:

```elm
> 1 :: 2 :: 3 :: []
[1,2,3] : List number
```

Ela utiliza a função/operador (::), conhecida como cons, que é a abreviação de list constructor:

```elm
> (::)
<function> : a -> List a -> List a
```

Esse operador faz sentido se você pensar na implementação de uma lista encadeada. No caso de uma lista com elementos 1, 2 e 3, nós temos três nós, com esses valores, e cada um está apontando para o elemento seguinte. E o último elemento (no caso o 3) aponta para o fim da lista, que pode ser representado pela lista vazia.

Ao contrário de todos os operadores aritméticos (+, -, * etc) o cons é associativo da direita para a esquerda, isto é:

```elm
> 1::2::3::[] == 1::(2::(3::[]))
True : Bool
```

Esse operador é muito utilizado para criar uma lista a partir de outra:

```elm
> x = [1, 2, 3]
[1,2,3] : List number
```

```elm
> y = 4 :: x
[4,1,2,3] : List number
```

Esse operador é de certa forma o contrário do List.head e List.tail:

```elm
> List.head [1, 2, 3]
Just 1 : Maybe number


> List.tail [1, 2, 3]
Just [2,3] : Maybe (List number)


> 1 :: [2, 3]
[1,2,3] : List number
```

Por hora ignore o Just. Ele faz parte do tipo Maybe e será discutido depois.

## Arrays
Não são muito usados em Elm porque para adicionar ou remover um elemento é necessário recriar o array do zero. Isso é diferente de JS, por exemplo, onde os arrays são mutáveis

## Maybe
https://thoughtbot.com/blog/problem-solving-with-maybe
https://thoughtbot.com/blog/maybe-mechanics

## [Tipagem nominal](https://101wiki.softlang.org/Concept:Structural_typing)

A seção de [custom types](https://guide.elm-lang.org/types/custom_types.html) do guia oficial explica o uso da declaração type. Ele serve para [tipagem nominal](https://en.wikipedia.org/wiki/Nominal_type_system), isto é, um tipo que precisa sempre ser chamado explicitamente pelo nome:

```elm
> type Bool = True | False
> b = True
True : Bool
```

Isso parece bastante um [enum do Typescript](https://www.typescriptlang.org/docs/handbook/enums.html), mas há uma diferença bem importante: cada uma das variações do tipo (customizado) pode carregar dados. Por exemplo, para modelarmos em Elm as 53 cartas do baralho (4 naipes x 13 "números" + 1 coringa) podemos fazer assim:

```elm
type Suit = Club | Diamond | Spade | Heart
type Value = N1 | N2 | N3 | N4 | N5 | N6 | N7 | N8 | N9 | N10 | J | Q | K
type Card = RegularCard Suit Value | Joker
```

Nesse exemplo podemos criar duas cartas c1 e c2 assim:

```elm
> c1 = RegularCard Heart J
RegularCard Heart J : Card
> c2 = Joker
Joker : Card
```

Repare que ambas são do tipo Card, apesar de uma carta ter mais informações que a outra.

Para trabalhar com esses tipos compostos você pode usar o [pattern matching](https://guide.elm-lang.org/types/pattern_matching.html):

```elm
type User
  = Regular String Int
  | Visitor String

toName : User -> String
toName user =
  case user of
    Regular name age ->
      name

    Visitor name ->
      name
```

Repare que nesse último exemplo o campo age não interessa, então ele pode ser omitido:

```elm
toName : User -> String
toName user =
  case user of
    Regular name _ ->
      name

    Visitor name ->
      name
```

https://thoughtbot.com/blog/shaping-values-with-types
https://thoughtbot.com/blog/booleans-and-enums
https://thoughtbot.com/blog/modeling-with-union-types

## Tipagem estrutural

Compare isso com o type alias, que serve para [tipagem estrutural](https://en.wikipedia.org/wiki/Structural_type_system) através de records. Dessa forma não é necessário chamá-lo explicitamente pelo nome para utilizá-lo. É similar às [interfaces do Typescript](https://www.typescriptlang.org/docs/handbook/interfaces.html).

```elm
type alias User = { name : String, age : Int }
```

Você pode criar um registro sem precisar chamar o tipo explicitamente:

```elm
> u1 = { name = "Foo", age = 13 }
{ age = 13, name = "Foo" }  : { age : number, name : String }
```

Ou pode também utilizar as funções de conveniência geradas quando se declara o type alias:

```elm
> u2 = User "Bar" 10
{ age = 10, name = "Bar" } : User
```

O resultado é equivalente. Além disso, entre as funções de conveniência temos algumas que servem para acessar os campos do record. Elas adotam uma convenção bem similar a de JS, que é .nomeDoAtributo. Funciona assim:

```elm
> .age u2
10 : Int
> .name u2
"Bar" : String
```

O Elm também permite escrever essas funções que começam com ponto depois do record:

```elm
> u2.age
10 : Int
> u2.name
"Bar" : String
```

Assim fica bem mais conveniente, né? Mas repare que isso é só uma notação. O .nomeDoAtributo é uma função normal:

```elm
> .name
<function> : { b | name : a } -> a
> .age
<function> : { b | age : a } -> a
```

Aqui podemos ver de volta a tipagem estrutural. Essas funções não precisam ser chamadas com o tipo explícito definido anteriormente (no caso User). Basta que sejam chamadas com um record que por um acaso tenha o campo homônimo. É isso que a notação { b | name : a } significa.

## Variáveis de tipo ou type variables

E o que são essas letras `a` e `b` na assinatura da função? Elas são variáveis de tipo (type variables). São letras arbitrárias que designam um tipo genérico, e são equivalentes aos [Generics do Typescript](https://www.typescriptlang.org/docs/handbook/generics.html). No caso em questão, podemos ler as assinaturas das funções da seguinte forma:

```elm
> .name
<function> : { b | name : a } -> a
```

A função .name pode ser chamada num record de tipo arbitrário b, com um campo name de tipo arbitrário a, e a resposta dessa função tem que ser do **mesmo tipo** arbitrário a.


Lembre-se que funções são muito importantes numa linguagem funcional, e portanto dizer que o tipo de uma expressão é "função" é muito vago. Você precisa dizer quais são os tipos de entrada e de saída.

Repare também que a declaração do tipo das funções e expressões é **opcional**. O compilador geralmente é capaz de identificar sozinho os tipos mesmo se você omitir essa linha. A assinatura da função está lá só para ajudar o desenvolvedor a ler o código. Então se você não souber o tipo de uma função você pode pedir uma ajuda para o compilador, seja usando o elm repl (pode [importar](https://guide.elm-lang.org/webapps/modules.html) uma função de um arquivo) ou com o próprio elm reactor, bastando escrever um código errado e vendo no navegador a ajuda que o compilador te oferece.

```elm
-- Esse tipo está propositalmente errado
unknownType : Int
unknownType = toName
```

Ao abrir a aplicação no navegador o compilador vai sugerir o tipo correto

[Friendly Compiler](images/friendly_compiler.png)



## Funções

Todas as funções em Elm só recebem um argumento, mas há várias formas de compor funções para receber vários deles. O livro Elm in Action explica bem como trabalhar com tuplas, curryng (cujo nome é originário do matemático [Haskell Curry](https://en.wikipedia.org/wiki/Haskell_Curry)) e aplicação parcial de funções.

[Curry table](images/curry_table.png)

O uso de aplicação parcial de funções é muito comum em Elm e outras linguagens funcionais. Por isso é comum que os parâmetros fixos sejam posicionados antes dos valores de runtime, por exemplo na função [List.filter](https://package.elm-lang.org/packages/elm/core/latest/List#filter):

[filter](images/filter.png)

Repare que nesse exemplo a função isEven é o primeiro argumento, pois é normal que esse tipo de função seja fixada em tempo de compilação. Já o argumento da lista pode ser um valor que só vai aparecer bem depois no momento de runtime, então é conveniente que ele apareça por último. Esse padrão é bem recorrente em Elm.

Vamos olhar agora para a assinatura da função. É importante notar a associatividade da direita para a esquerda. Isso significa que as duas notações a seguir são equivalentes:

```elm
f : a -> b -> c -> d
f : a -> (b -> (c -> d))
```

## Tipos mais complexos

A tipagem do Elm é extremamente poderosa, e permite resolver vários problemas só com isso, em tempo de compilação. É graças a isso que os erros de runtime se tornam muito raros.

**TODO**
Falar sobre tipos opacos (ex.: type UserId = UserId Int)

