# iEx

O iEx é um shell iterativo onde você pode executar código em elixir. Por shell iterativo, você pode chamar de terminal iterativo. O importante é, você consegue rodar código elixir em uma tela preta.

Vamos abrir nosso terminal e executar o comando `iex`.

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

### Executando iex em um projeto

Uma vez que você já possui um projeto e queira usar os módulos que você criou nele, basta adicionar o mix de seu projeto junto. Para isso, temos um simples comando. Vá na pasta raiz de seu projeto (aquele lugar onde tem o `mix.ex` e execute o comando para abrir o iex junto com o `mix`

<pre class="language-sh" data-line-numbers><code class="lang-sh">iex -S mix
Erlang/OTP 25 [erts-13.1.4] [source] [64-bit] [smp:16:16] [ds:16:16:10] [async-threads:1] [jit:ns]

Compiling 4 files (.ex)

<strong>Generated hello_world app
</strong>Interactive Elixir (1.14.3) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)>
</code></pre>

Eu executei dentro do projeto [`hello_world`](meu-hello-world.md) e como você pode ver ele foi compilado para dentro do terminal iterativo na linha 8.  Agora basta usar alguma função que você ja tenha criado.



### Dados de suporte ao iex



###
