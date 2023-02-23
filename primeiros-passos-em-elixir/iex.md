# IEx

O iEx é um shell iterativo onde você pode executar comando elixir. Vamos abrir nosso terminal e executar o comando `iex`.

```elixir
iex
Erlang/OTP 25 [erts-13.1.4] [source] [64-bit] [smp:16:16] [ds:16:16:10] [async-threads:1] [jit:ns]

Interactive Elixir (1.14.3) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)>
```

Temos diversas informações interessantes sobre sua maquina, quando processos ela suporte, arquitetura e outras coisas. Mas vamos nos ater ao simples.&#x20;

Uma vez com o `iex` aberto podemos rodar qualquer tipo de comando elixir. Por exemplo, eu quero imprimir na tela o meu nome.

```
iex(1)> "Effting"
"Effting"
```

Ou quero inverter esse texto

{% hint style="info" %}
**Autocomplete**\
\
IEx tem função de autocomplete, podendo escrever pedaço da palavra e apertar em tab, ela irá dar as opções para você. Tanto as nativas do elixir quanto criadas por você.
{% endhint %}

```
iex(2)> String.reverse("̀Effting")
"gnitffe"
```

Você pode usar qualquer tipo de função que existe no elixir no iex, isso ajuda muito na hora de debugar o código.

{% hint style="info" %}
Caso tenha mais interesse em debugging tenho um vídeo no canal\
\
[https://www.youtube.com/watch?v=QL8gIwNI\_7U](https://www.youtube.com/watch?v=QL8gIwNI\_7U)
{% endhint %}

### Executando iex em seu projeto

Uma vez que você já possui um projeto e queira usar os módulos que você criou nele, basta adicionar o mix de seu projeto junto. Para isso, temos um simples comando. Vá na pasta raiz de seu projeto (aquele lugar onde tem o `mix.ex` e execute o comando para abrir o iex junto com o `mix`

<pre class="language-sh" data-line-numbers><code class="lang-sh">iex -S mix
Erlang/OTP 25 [erts-13.1.4] [source] [64-bit] [smp:16:16] [ds:16:16:10] [async-threads:1] [jit:ns]

Compiling 4 files (.ex)

<strong>Generated hello_world app
</strong>Interactive Elixir (1.14.3) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)>
</code></pre>

Eu executei dentro do projeto [`hello_world`](meu-hello-world.md) e como você pode ver ele foi compilado para dentro do terminal iterativo na linha 8.  Agora basta usar alguma função que você ja tenha criado.

### Arquivo .iex.exs

Uma das melhores coisas a se conhecer é o arquivo `.iex.exs`. De início você pode não precisar, mas quando o projeto começar a ficar maior ou os tipoes de dados mais complexas, você vai querer um solução que esse arquivo proporciona.

Quando inicamos uma sessão usitlizando `iex`, a ferramenta procura por um arquivo local chamado `.iex.exs`, normalmente criado no root do projeto, lado-a-lado do `mix.ex`. Então depois procura por um global localizado em `~/.iex.exs` carregando o primeiro que encontrar, dando sempre preferencia para o arquivo local do projeto.

Tudo que está dentro desse arquivo é carregado para o contexto do IEx. Tudo que for criado ali vai ser carregado na sessão. Seja uma [map](../basico/colecoes/mapas.md), uma [estrutura](../basico/colecoes/estruturas.md) ou um [modulo](../basico/modulos.md).&#x20;

Vamos criar um .iex.exs no root do projeto [Hello, world ](meu-hello-world.md)criado no capítulo anterior. Adicionaremos lá uma variável simples

{% code title=".iex.exs" lineNumbers="true" %}
```elixir
my_first_variable = "awesome!"
```
{% endcode %}

Apenas isso. Agora vamos rodar o iex no root do projeto.

```sh
iex       
Erlang/OTP 25 [erts-13.1.4] [source] [64-bit] [smp:16:16] [ds:16:16:10] [async-threads:1] [jit:ns]

Interactive Elixir (1.14.3) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> my_first_variable
"awesome!"
```

Simples. Isso funciona para qualquer tipo de dado o que torna as coisas bem interessantes. Podendo criar ferramentas para ajudar o debug, por exemplo. Se bem pensado, esse arquivo pode lhe dar bons recursos para te ajudar no dia-a-dia.

### Conclusão

IEx é uma poderosa ferramenta que se bem utilizada pode te livrar de muito estresse. Crie funções de suporte que facilitem seu trabalho, não tenham medo de criar código por lá, isso pode te poupar muito tempo.

###
