# cond

Diferente de `case/2` que verificamos o valor e escolhemos o caso especifico para ela, `cond/1`  **verificamos a condição**, como feito no `if/2` com o adicional de termos várias condições alinhadas onde retornamos a primeira que for verdadeira.

A definição dele segue a palavra chave `cond` seguido de `do`. Todo o controle de fluxo é tido dentro dele, exemplo:

{% code lineNumbers="true" %}
```elixir
x = 3 # declaração de váriavel que será usada no cond

cond do
  x == 5 -> 
   "Isso não é verdadeiro" # não entrará nesse escopo
   
  (x + 8) > 10 ->
    "Nem isso" # nem nesse
    
  x == 3 ->
    "Mas isso será" # Essa condição é verdadeira pois x é igual a 3 então esse bloco será executado.
    
  true ->
    "Nunca virá aqui, pois a clausura anterior é verdadeira"
end
```
{% endcode %}

Vamos a um exemplo mais útil. Você é um cientista em uma grande empresa farmacêutico. Temos vários níveis de acesso que vão de 1 a 3, sendo 1 o nível de visitantes e 3 o nível de acesso a armas biológicas.&#x20;

Precisamos então dar permissão a cada área de acordo com seu nível de acesso.&#x20;

{% hint style="danger" %}
**Lembrando que estamos falando de autorização e não autenticação.**&#x20;
{% endhint %}

Nossos critérios de aceite serão:

* Quando usuário tiver acesso 1, exibir mensagem "_**Bem vindo {nome}, sinta-se**_** à vontade **_**em nossas estruturas!**_**"**
* Quando usuário tiver acesso 2, mostrar ultima pendência no setor da saúde junto com o nome do usuário _**"Bem vindo João, o Ibuprofeno x42 esta nas fases finais"**_
* Quando o usuário tiver acesso 3, mostrar ultimo status do nemesis. **"Sr. Iago, já mandamos nemesis para Raccoon city"**

Vamos começar criando nosso teste:

{% code title="tests/authorization_test.exs" lineNumbers="true" %}
```elixir
defmodule AuthorizationTest do
  use ExUnit.Case
  
  describe "access/1" do
    test "User is a guest" do
      person = %{name: "Paulo", level: 1}
      result = Authorization.access(person)
  
      assert result == "Bem vindo #{person.name}, sinta-se à vontade em nossas estruturas!"
    end
  end
end
```
{% endcode %}

```shell
mix test test/authorization_test.exs
warning: Authorization.access/1 is undefined (module Authorization is not available or is yet to be defined)
  test/authorization_test.exs:7: AuthorizationTest."test access/1 User is a guest"/1



  1) test access/1 User is a guest (AuthorizationTest)
     test/authorization_test.exs:5
     ** (UndefinedFunctionError) function Authorization.access/1 is undefined (module Authorization is not available)
     code: result = Authorization.access(person)
     stacktrace:
       Authorization.access(%{level: 1, name: "Iago"})
       test/authorization_test.exs:7: (test)


Finished in 0.03 seconds (0.00s async, 0.03s sync)
1 test, 1 failure
```

Recebemos um relatório dizendo que Authorization.access não está disponível. Claramente, por que não possuímos esse modulo. Criaremos ele

{% code title="lib/authorization.ex" lineNumbers="true" %}
```elixir
defmodule Authorization do
  def access(_user) do

  end
end
```
{% endcode %}

Criamos o modulo de `Authorization` com uma função chamada `access/1` que contém um variável `_user`. Se rodarmos o teste conseguiremos outro erro.

```elixir
mix test test/authorization_test.exs
Compiling 1 file (.ex)
Generated hello_world app


  1) test access/1 User is a guest (AuthorizationTest)
     test/authorization_test.exs:5
     Assertion with == failed
     code:  assert result == "Bem vindo #{person.name}, sinta-se à vontade em nossas estruturas!"
     left:  nil
     right: "Bem vindo Paulo, sinta-se à vontade em nossas estruturas!"
     stacktrace:
       test/authorization_test.exs:9: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Recebemos agora outro erro, dizendo que precisamos de um texto, mas o que recebemos foi um `nil`. Isso por que não estamos retornando nada da função chamada. Vamos fazer o mínimo para passar esse teste, retornaremos da função, o texto completo.

<pre class="language-elixir" data-title="lib/authorization.ex" data-line-numbers><code class="lang-elixir">defmodule Authorization do
  def access(_user) do
<strong>    "Bem vindo Paulo, sinta-se à vontade em nossas estruturas!"
</strong>  end
end
</code></pre>

Se rodarmos agora, os testes irão passar.

```shell
mix test test/authorization_test.exs
.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 0 failures
```

Uma vez os testes passando podemos refatorar. O primeiro passo, é deixa-lo dinamico. Vou adicionar uma nova validação junto no mesmo teste, apenas para comprovar que está correto. Vamos alterar nosso teste:

<pre class="language-elixir"><code class="lang-elixir">defmodule AuthorizationTest do
  use ExUnit.Case

  describe "access/1" do
    test "User is a guest" do
<strong>      person1 = %{name: "Paulo", level: 1}
</strong><strong>      person2 = %{name: "José", level: 1}
</strong>
<strong>      result1 = Authorization.access(person1)
</strong><strong>      result2 = Authorization.access(person2)
</strong>
<strong>      assert result1 == "Bem vindo #{person1.name}, sinta-se à vontade em nossas estruturas!"
</strong><strong>      assert result2 == "Bem vindo #{person2.name}, sinta-se à vontade em nossas estruturas!"
</strong>    end
  end
end
</code></pre>

Se rodarmos o teste assim ele acusará um erro

```shell
mix test test/authorization_test.exs


  1) test access/1 User is a guest (AuthorizationTest)
     test/authorization_test.exs:5
     Assertion with == failed
     code:  assert result2 == "Bem vindo #{person2.name}, sinta-se à vontade em nossas estruturas!"
     left:  "Bem vindo Paulo, sinta-se à vontade em nossas estruturas!"
     right: "Bem vindo José, sinta-se à vontade em nossas estruturas!"
     stacktrace:
       test/authorization_test.exs:13: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failureshe
```

Isso por que, em nossa implementação não utilizamos a variável que mandamos por parâmetro. Logo, ela está fixado como Paulo. Vamos arrumar isso.

<pre class="language-elixir" data-title="lib/authorization.ex" data-line-numbers><code class="lang-elixir">defmodule Authorization do
  def access(user) do
<strong>    "Bem vindo #{user.name}, sinta-se à vontade em nossas estruturas!"
</strong>  end
end
</code></pre>

Removemos o underline (\_) de user e utilizamos na interpolação da linha 3. Se rodarmos agora esse teste, teremos sucesso.

```shell
mix test test/authorization_test.exs
Compiling 1 file (.ex)
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Agora possuímos apenas mais um ponto para ver. O que acontece caso mandamos um map incorreto para dentro da função `access`? Por exemplo `Authorization.access("lalala")`.

Vamos validar isso no terminal iterativo do elixir. Para isso queremos que esse projeto que estamos mexendo compile junto para que possamos usar o modulo `Authorization`. Para isso, basta executar na pasta raiz de seu projeto `iex -S mix` e o terminal iterativo será aberto:

```shell
iex -S mix
Erlang/OTP 25 [erts-13.1.4] [source] [64-bit] [smp:16:16] [ds:16:16:10] [async-threads:1] [jit:ns]

Interactive Elixir (1.14.3) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> 
```

Caso não saiba o que é isso, vá para a sessão [iEX](../../../primeiros-passos-em-elixir/iex.md) do livro.&#x20;

Vamos executar a função Authorization.access/1 utilizando como parâmetro algo incorreto, como "lalala"

```shell
iex(1)> Authorization.access("lalala")
** (KeyError) key :name not found in: "lalala". If you are using the dot syntax, such as map.field, make sure the left-hand side of the dot is a map
    (hello_world 0.1.0) lib/authorization.ex:3: Authorization.access/1
```

Isso acontece porque na função `access/1` tentamos acessar um elemento chamado `name` dentro do map `user`. Já que user agora é "lalala", uma string, ele não possui esse elemento. Ele nem é um map, logo, não temos como acessa-lo. Temos várias formas de resolver esse problema.&#x20;

Primeira coisa que iremos fazer é criar um teste para entender o que precisamos que seja retornado caso seja invalido. Criaremos um teste chamado `invalid_params` e adicionaremos o cenário de falha ali.

{% code title="test/authorization_test.exs" lineNumbers="true" %}
```elixir
defmodule AuthorizationTest do
  use ExUnit.Case

  describe "access/1" do
    test "invalid params" do
      invalid_params = "lalala"
      {:error, reason} = Authorization.access(invalid_params)
      
      assert reason == "invalid_params"
    end
    
    # ...
  end
end
```
{% endcode %}

Criamos um teste simples para garantir o comportamento. Se você analisar o código, verá que agora adicionamos a convenção de [:ok/:error](../../../conceitos/convencoes.md#tupla-ok-result-e-error-reason) para conseguirmos facilmente identificar quando algo da certo e quando algo da errado.

Se rodarmos o teste, vamos ver que conseguimos reproduzir o erro no teste, o que é um ótimo sinal.

```shell
mix test test/authorization_test.exs


  1) test access/1 invalid params (AuthorizationTest)
     test/authorization_test.exs:5
     ** (KeyError) key :name not found in: "lalala". If you are using the dot syntax, such as map.field, make sure the left-hand side of the dot is a map
     code: {:error, reason} = Authorization.access(invalid_params)
     stacktrace:
       (hello_world 0.1.0) lib/authorization.ex:3: Authorization.access/1
       test/authorization_test.exs:7: (test)

.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 1 failure
```

Agora precisamos lidar com ele. Para isso, utilizaremos a validação de tipo usando uma estrutura que ja possuímos no projeto. A estrutura `User`. Nela já possuímos o `name`.&#x20;

{% hint style="info" %}
Utilizo uma estrutura ja criada para mostrar a reutilização de módulos e também como seria um fluxo de atualização do mesmo, chegando mais perto do mundo real.
{% endhint %}

Caso você não a tenha por algum motivo, vou colocar aqui para facilitar

{% code title="lib/user.ex" lineNumbers="true" %}
```elixir
defmodule User do
  defstruct [:id, :name]
end
```
{% endcode %}

Tendo a estrutura, precisamos garantir que nossa função `access` aceite receber apenas ela. Vamos atualizar nosso código.

{% code title="lib/authorization.ex" lineNumbers="true" %}
```elixir
defmodule Authorization do
  def access(user = %User{}) do
    "Bem vindo #{user.name}, sinta-se à vontade em nossas estruturas!"
  end
end
```
{% endcode %}

Se rodar novamente o erro, teremos um relatório diferente

```shell
mix test test/authorization_test.exs
Compiling 1 file (.ex)


  1) test access/1 User is a guest (AuthorizationTest)
     test/authorization_test.exs:12
     ** (FunctionClauseError) no function clause matching in Authorization.access/1

     The following arguments were given to Authorization.access/1:
     
         # 1
         %{level: 1, name: "Paulo"}
     
     Attempted function clauses (showing 1 out of 1):
     
         def access(user = %User{})
     
     code: result1 = Authorization.access(person1)
     stacktrace:
       (hello_world 0.1.0) lib/authorization.ex:2: Authorization.access/1
       test/authorization_test.exs:16: (test)



  2) test access/1 invalid params (AuthorizationTest)
     test/authorization_test.exs:5
     ** (FunctionClauseError) no function clause matching in Authorization.access/1

     The following arguments were given to Authorization.access/1:
     
         # 1
         "lalala"
     
     Attempted function clauses (showing 1 out of 1):
     
         def access(user = %User{})
     
     code: {:error, reason} = Authorization.access(invalid_params)
     stacktrace:
       (hello_world 0.1.0) lib/authorization.ex:2: Authorization.access/1
       test/authorization_test.exs:7: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 2 failuresshel
```

Recebemos dois erros, vamos focar no primeiro agora. Já que nossa função access permite apenas receber a estrutura `User` agora, precisamos alterar nossos dados de testes para enviar o `User` para `access`.

<pre class="language-elixir" data-title="test/authorization_test.exs" data-line-numbers><code class="lang-elixir">defmodule AuthorizationTest do
  use ExUnit.Case

  describe "access/1" do
    test "User is a guest" do
<strong>      person1 = %User{name: "Paulo", level: 1}
</strong><strong>      person2 = %User{name: "José", level: 1}
</strong>
      result1 = Authorization.access(person1)
      result2 = Authorization.access(person2)

      assert result1 == "Bem vindo #{person1.name}, sinta-se à vontade em nossas estruturas!"
      assert result2 == "Bem vindo #{person2.name}, sinta-se à vontade em nossas estruturas!"
    end
  end
end
</code></pre>

```shell
mix test test/authorization_test.exs

== Compilation error in file test/authorization_test.exs ==
** (KeyError) key :level not found
    (hello_world 0.1.0) expanding struct: User.__struct__/1
    test/authorization_test.exs:13: AuthorizationTest."test access/1 User is a guest"/1
```

Recebemos um outro erro, dizendo que a `key :level not found`. Quando utilizamos structure, precisamos utilizar apenas a estrutura de dados criada nele. Em user não possuímos um campo :level e isso faz com que a compilação falhe.&#x20;

{% hint style="danger" %}
A utilização de estrutura engessa os dados nele, garantindo que a estrutura seja sempre a mesma. Isso é um bom ganho quando tratamos com dados.
{% endhint %}

Precisamos adicionar `:level` em `User`. É uma simples mudança

{% code title="lib/user.ex" lineNumbers="true" %}
```elixir
defmodule User do
  defstruct [:id, :name, level: 1]
end
```
{% endcode %}

Para garantir consistência e segurança em nosso projeto, podemos configurar que, caso não passado o `level`, ele será automaticamente 1, que significa que ele é visitante.

Podemos agora rodar os testes&#x20;

```shell
mix test test/authorization_test.exs


  1) test access/1 invalid params (AuthorizationTest)
     test/authorization_test.exs:5
     ** (FunctionClauseError) no function clause matching in Authorization.access/1

     The following arguments were given to Authorization.access/1:
     
         # 1
         "lalala"
     
     Attempted function clauses (showing 1 out of 1):
     
         def access(user = %User{})
     
     code: {:error, reason} = Authorization.access(invalid_params)
     stacktrace:
       (hello_world 0.1.0) lib/authorization.ex:2: Authorization.access/1
       test/authorization_test.exs:7: (test)

.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 1 failure
```

Resolvemos o primeiro erro. Agora vamos para o proximo tratamento. Esse aqui esta falando que "lalala" não é uma estrutura User e ele esta certo. Agora precisamos fazer com que o erro seja retornado. Já possuímos o texto que precisamos no teste criado em `authorization_test.exs`.

Se você analisar, estamos usando pattern matching no parâmetro da função e devido não ter sido correspondido ele não entrou na primeira função access. Podemos então criar uma nova função de mesmo nome, fazer dar matching e então retornar a mensagem de erro. Vamos lá



{% code title="lib/authorization.ex" lineNumbers="true" %}
```elixir
defmodule Authorization do
  def access(user = %User{}) do
    "Bem vindo #{user.name}, sinta-se à vontade em nossas estruturas!"
  end

  def access(_user) do
    {:error, "invalid_params"}
  end
end
```
{% endcode %}

Se rodarmos o teste agora, teremos sucesso.

```shell
mix test test/authorization_test.exs
..
Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 0 failures
```

Tudo finalizado no primeiro caso, porém, temos um inconsistência. Quando temos sucesso, recebemos a `message`, mas quando temos um erro temos uma estrutura diferente `{:error, reason}`. Devemos deixar elas similares e como estamos usando o padrão de [tuplas de ok/error](../../../conceitos/convencoes.md#tupla-ok-result-e-error-reason), devemos alterar a resposta de sucesso para recebermos `{:ok, result}`. Mudança simples, apenas alterar primeiro os testes e depois a implementação.

<pre class="language-elixir" data-title="test/authorization_test.exs" data-line-numbers><code class="lang-elixir">defmodule AuthorizationTest do
  use ExUnit.Case

  describe "access/1" do
    # ...
    test "User is a guest" do
      person1 = %User{name: "Paulo", level: 1}
      person2 = %User{name: "José", level: 1}

<strong>      {:ok, result1} = Authorization.access(person1)
</strong><strong>      {:ok, result2} = Authorization.access(person2)
</strong>
      assert result1 == "Bem vindo #{person1.name}, sinta-se à vontade em nossas estruturas!"
      assert result2 == "Bem vindo #{person2.name}, sinta-se à vontade em nossas estruturas!"
    end
  end
end
</code></pre>

<pre class="language-elixir" data-title="lib/authorization.ex" data-line-numbers><code class="lang-elixir">defmodule Authorization do
  def access(user = %User{}) do
<strong>    {:ok, "Bem vindo #{user.name}, sinta-se à vontade em nossas estruturas!"}
</strong>  end

  # ...
end
</code></pre>

Feito isso, so rodar os testes para garantir que esta tudo certo.

```shell
mix test test/authorization_test.exs
Compiling 1 file (.ex)
..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 0 failures
```

{% hint style="danger" %}
**Uma pausa rápida**\
\
Sei que não vimos nada sobre `cond/0` até então, mas isso foi um bom exercício para pensar em como desenvolver em elixir. Espero que me perdoem por essa volta, mas as vezes o atalho tem mais perigos que a longa jornada.\
\
Seguimos
{% endhint %}

Conseguimos finalizar o primeiro ponto, vamos seguir para o ponto dois.

* ~~Quando usuário tiver acesso 1, exibir mensagem "_**Bem vindo {nome}, sinta-se**_** ****à vontade**** **_**em nossas estruturas!**_**"**~~
* Quando usuário tiver acesso 2, mostrar ultima pendência no setor da saúde junto com o nome do usuário _**"Bem vindo João, o Ibuprofeno x42 esta nas fases finais"**_
* Quando o usuário tiver acesso 3, mostrar ultimo status do nemesis. **"Sr. Iago, já mandamos nemesis para Raccoon city"**

Vamos agora para o nível 2, a autorização de um funcionário. Vamos agora criar um teste para lidar com esse cenário.

{% code title="test/authorization_teste.exs" lineNumbers="true" %}
```elixir
defmodule AuthorizationTest do
  use ExUnit.Case

  describe "access/1" do
    # ...
    test "User is an employee" do
      person = %User{name: "Carlos", level: 2}

      {:ok, result} = Authorization.access(person)


      assert result == "Bem vindo #{person.name}, o Ibuprofeno x42 esta nas fases finais"
    end
  end
end
```
{% endcode %}

O teste agora quer garantir que usuários com nível 2 de acesso, tenham informação sobre seu dia de trabalho, pois nível dois se da a funcionários. Se rodarmos esse teste, teremos um relatório de erro.

```shell
mix test test/authorization_test.exs


  1) test access/1 User is an employee (AuthorizationTest)
     test/authorization_test.exs:5
     Assertion with == failed
     code:  assert result == "Bem vindo #{person.name}, o Ibuprofeno x42 esta nas fases finais"
     left:  "Bem vindo Carlos, sinta-se à vontade em nossas estruturas!"
     right: "Bem vindo Carlos, o Ibuprofeno x42 esta nas fases finais"
     stacktrace:
       test/authorization_test.exs:11: (test)

..
Finished in 0.04 seconds (0.00s async, 0.04s sync)
3 tests, 1 failure
```

A mensagem que esperavamos não foi entregue. Isso devido a função access so esta implementado para devolver uma mensagem de primeiro nível. Vamos alterar utilizando cond/0 para tomar a decisão de fluxo que queremos.

{% hint style="info" %}
Relembrando que

\
\* nivel 1 -> mensagem para visitantes\
\* nivel 2 -> mensagem de funcionarios base\
\* nivel 3 -> mensagem de postos altos
{% endhint %}

Vamos alterar nosso código

{% code title="lib/authorization.ex" lineNumbers="true" %}
```elixir
defmodule Authorization do
  def access(user = %User{}) do
    cond do
      user.level == 1 ->
        {:ok, "Bem vindo #{user.name}, sinta-se à vontade em nossas estruturas!"}

      user.level == 2 ->
        {:ok, "Bem vindo #{user.name}, o Ibuprofeno x42 esta nas fases finais"}
    end
  end

  def access(_user) do
    {:error, "invalid_params"}
  end
end
```
{% endcode %}

Adicionamos a condicional `cond/0` onde ela lida com o `level` de acesso do usuário. Percebe como fica simples adicionar novos desvios de fluxo?&#x20;

```shell
mix test test/authorization_test.exs
Compiling 1 file (.ex)
...
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 0 failures
```

Simples assim, fechamos mais um critério de aceite.\


* ~~Quando usuário tiver acesso 1, exibir mensagem "_**Bem vindo {nome}, sinta-se**_** ****à vontade**** **_**em nossas estruturas!**_**"**~~
* ~~Quando usuário tiver acesso 2, mostrar ultima pendência no setor da saúde junto com o nome do usuário _**"Bem vindo João, o Ibuprofeno x42 esta nas fases finais"**_~~
* Quando o usuário tiver acesso 3, mostrar ultimo status do nemesis. **"Sr. Iago, já mandamos nemesis para Raccoon city"**

{% hint style="info" %}
**Desafio**\
\
Tente adicionar o level 3 para dar matching na frase: **Sr. {nome}, já mandamos Nemesis para Raccoon city.**\
****\
****Você deve:\
****\
****1. Escrever o teste em authorizatin\_test.exs\
2\. Implementar a solução em authorization.ex
{% endhint %}

Espero que tenha feito o desafio, essas ações que fazem internalizarmos o conteúdo. Não irei implementar ele. Esse é o desafio.&#x20;

Existe apenas mais uma coisa a se fazer. Vamos dizer que eu coloque o nível de acesso 10. O que aconteceria? Vamos abrir novamente nosso terminal iterativo `iex -S mix` e executaremos a chamada da função usando o level 10.

```shell
iex(1)> Authorization.access(%User{name: "iago", level: 10})
** (CondClauseError) no cond clause evaluated to a truthy value
    (hello_world 0.1.0) lib/authorization.ex:7: Authorization.access/1
```

Recebemos um relatório de erro. A parte importante é `no cond clause evaluated to a truthy value`. Nenhuma condição foi saciada no cond, sem opção do que fazer, ele quebra. Temos que ter pelo menos uma condição que verdadeira para não causar problemas a nos. Fora que precisamos avisar ao usuário que é melhor ele se retirar antes que chame a segurança. Vamos primeiro criar nosso teste.

```elixir
defmodule AuthorizationTest do
  use ExUnit.Case

  describe "access/1" do
    test "You are supposed to not be here" do
      person = %User{name: "Eduardo", level: 10}

      {:error, reason} = Authorization.access(person)

      assert reason == "#{person.name}, você não deveria estar aqui. Peço que se retire"
    end

    # ...
  end
end
```

Execute esse código e verá que conseguimos reproduzir o problema que enfrentamos no terminal iterativo.&#x20;

```shell
mix test test/authorization_test.exs
Compiling 1 file (.ex)
...

  1) test access/1 You are supposed to not be here (AuthorizationTest)
     test/authorization_test.exs:5
     ** (CondClauseError) no cond clause evaluated to a truthy value
     code: {:error, reason} = Authorization.access(person)
     stacktrace:
       (hello_world 0.1.0) lib/authorization.ex:7: Authorization.access/1
       test/authorization_test.exs:8: (test)
```

Precisamos cuidar disso agora, mas seremos defensivos. Ao invez de fazer alguma regra, deixaremos uma clausura true no final de cond, onde, se nenhuma clausura for saciada, a ultima sera e uma mensagem de erro aparecerá.

<pre class="language-elixir" data-title="lib/authorization.ex" data-line-numbers><code class="lang-elixir">defmodule Authorization do
  def access(user = %User{}) do
    cond do
      user.level == 1 ->
        {:ok, "Bem vindo #{user.name}, sinta-se à vontade em nossas estruturas!"}

      user.level == 2 ->
        {:ok, "Bem vindo #{user.name}, o Ibuprofeno x42 esta nas fases finais"}

<strong>      true ->
</strong><strong>        {:error, "#{user.name}, você não deveria estar aqui. Peço que se retire"}
</strong>    end
  end

  def access(_user) do
    {:error, "invalid_params"}
  end
end
</code></pre>

Adiconamos uma condição que será sempre `true`, caso nenhuma condição acima seja `true`, ele caira nessa última e a mensagem de erro vai ser gerada. Uma solução simples, que funciona bem para nós aqui.&#x20;

### Conclusão

Nesse capítulo, seguimos um fluxo de desenvolvimento comum em elixir, onde criar e refatoramos uma implementação. Também entendemos a utilidade da condicional `cond/0` e fizemos um desafio. Lembrem-se, sempre existe outras formas de atender a uma expectativa, podendo muitas vezes, utilizar os mesmos recursos. Tente e re-tente criar de formas diferentes para conseguir extrair os melhores resultados.&#x20;
