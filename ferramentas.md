# Ferramentas

## Elm Repl
Apesar de Elm ser uma linguagem compilada ela tem o console o [Elm Repl](https://elm-lang.org/news/repl), similar ao que existe em muitas linguagens interpretadas. Ele aparece no [guia oficial](https://guide.elm-lang.org/core_language.html) é muito útil para testar expressões simples:

![elm repl](images/repl.png)

O repl também pode ajudar a entender algumas partes mais complexas do Elm como [decoders](https://medium.com/@jxxcarlson/elm-types-and-json-decoders-cracking-the-code-e2be61f8afcd) pois imprime os tipos de cada expressão o que pode ser um bom guia nestes casos.

## Ellie
Para testar uma aplicação completa e compartilhá-la com os colegas você pode usar o [Ellie](https://ellie-app.com/).

![Ellie](images/ellie.png)

## Elm Reactor
E para desenvolvê-la na sua própria máquina é recomendado utilizar o Elm Reactor. É só iniciar um projeto e depois  `elm reactor` para iniciar o servidor.

![Reactor](images/reactor.png)

Depois você pode abrir o seu editor de código favorito para começar a mexer com Elm. Aqui no CJ a maioria do pessoal que mexe com Elm usa o Sublime Text, seguindo a sugestão do guia oficial. Mas há outros editores com suporte a Elm, como o VS Code, que podem ser interessantes também.

![Sublime](images/sublime1.png)

Como o Elm só aceita identação com espaços e não com tabs, pode ser útil ajustar o seu editor para preencher com espaços quando você apertar a tecla tab.


## Configurando o Sublime
Aqui no CJ a maioria do pessoal que mexe com Elm usa o [Sublime Text](https://www.sublimetext.com/), seguindo a [sugestão do guia oficial](https://guide.elm-lang.org/install/editor.html).

Para configurar o Sublime Text para ter uma melhor experiência no desenvolvimento com Elm precisamos de alguns plugins que podem ser instalados seguindo os seguintes passos:

1. Certifique-se de que o [Package Control](https://packagecontrol.io/) esteja instalado seguindo [esses passos](https://packagecontrol.io/installation), pois você vai precisar dele para instalar os próximos pacotes;
2. Instale o [Elm Syntax Highlighting](https://packagecontrol.io/packages/Elm%20Syntax%20Highlighting) para habilitar o reconhecimento da sintaxe do Elm;
3. Instale o [Elm Format on Save](https://packagecontrol.io/packages/Elm%20Format%20on%20Save) para formatar o código ao salvar utilizando o padrão da comunidade;
4. Instale o [LSP](https://packagecontrol.io/packages/LSP) e siga [esses](https://lsp.readthedocs.io/en/latest/#elm) passos para integrar o Sublime com um servidor da linguagem Elm. Com isso podemos ver no próprio editor os erros e avisos que o compilador do Elm nos fornece.

![Sublime LSP](images/subl-lsp.png)

## Elm-Format

A comunidade Elm utiliza muito o [elm-format](https://github.com/avh4/elm-format) para manter uma formatação padronizada do código. Se você configurar a integração com o seu editor, basta escrever o código de qualquer jeito, apertar o atalho para salvar que daí o código já fica formatado apropriadamente.

O lado positivo de ter um estilo de formatação único em toda a comunidade é que (quase) não se gasta tempo discutindo o estilo. Juntando isso com o compilador que pega mais de 90% dos possíveis problemas da aplicação, o code review de Elm é muito fácil (quando existir).


## Create-elm-app e Webpack

O Elm Reactor funciona bem para trabalhar com Elm, mas ele não tem integração com bibliotecas do ecossistema Node.js, como SASS e CSS Modules. Por isso em produção nós utilizamos o [create-elm-app](https://github.com/halfzebra/create-elm-app) para configurar o Webpack com [algumas customizações](https://github.com/CalculoJuridico/calculojuridico-web-checkout/blob/master/elmapp.config.js).

Importante: Para integrar o Webpack com o Elm utilizamos [flags de inicialização da aplicação](https://github.com/CalculoJuridico/calculojuridico-web-checkout/blob/bb1e70f9ce0b3b5c83608bafd9ca46c215b0003f/src/index.js#L17). Isso nos permite usar, por exemplo, [CSS Modules](https://github.com/CalculoJuridico/calculojuridico-web-checkout/blob/bb1e70f9ce0b3b5c83608bafd9ca46c215b0003f/src/scripts/assets/styles.js) e [obter as dimensões das imagens rasterizadas](https://github.com/CalculoJuridico/calculojuridico-web-checkout/blob/bb1e70f9ce0b3b5c83608bafd9ca46c215b0003f/src/scripts/assets/images.js) via Webpack (no caso de vetores SVG isso não é necessário).
