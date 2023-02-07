# Nomeadas

A função é o principal elemento das linguagens funcionais (grande surpresa an?) e tem como objetivo realizar comportamentos.&#x20;

{% hint style="info" %}
**Aridade**\
****\
****De forma simples, a aridade é a quantidade de argumentos que uma função recebe.

Uma função `login` que recebe `user` e `password` por parâmetro tem aridade 2 e é representado com o nome da função, barra (/) e o número da aridade. Exemplo:\
\
`login(user, password) -> login/2`
{% endhint %}

Funções nomeadas devem existir somente dentro de módulos e são declaradas com `def/2` e `defp/2` para funções privadas. Como por exemplo:

<pre class="language-elixir" data-line-numbers><code class="lang-elixir">defmodule Authenticate do
<strong>  def login(user, password) do
</strong>    # ...
  end
  
<strong>  defp create_session(user), do: # ...
</strong>end
</code></pre>

Vamos criar um novo teste em nosso projeto onde iremos fazer uma soma simples. Na pasta `test` crie um novo arquivo chamado `math_test.exs` e nele utilizaremos nosso primeiro `describe`, descrevendo o que está sendo testado.

{% hint style="info" %}
**Describe**

\
O `describe/2` descreve um grupo de testes que tem correlação. Assim podemos criar diversos cenários para uma mesma função que está sendo testada. Exemplo:\


* Quando a função x funciona
* Quando a função x não funciona devido a parâmetros errados\


Normalmente o describe recebe o `module + função sendo testada + aridade`&#x20;
{% endhint %}

Podemos tirar algumas vantagens quando utilizamos describe. Uma delas é servir de documentação. Então vamos pensar. Precisamos fazer uma soma (função) e essa soma irá somar dois valores x e y (parâmetros). Analisando isso, podemos presumir que o contrato de nossa função seja algo assim `Math.sum/2`. Vamos adicionar isso a nossa describe para facilitar o entendimento

<pre class="language-elixir" data-title="test/math_test.exs" data-line-numbers><code class="lang-elixir">defmodule MathTest do
  use ExUnit.Case

<strong>  describe "Math.sum/2" do
</strong>    # ...
  end
end

</code></pre>

Criaremos o primeiro bloco de teste  para quando passarmos os parâmetros corretos.&#x20;

{% hint style="info" %}
**Parâmetros**

\
Forma de injetar dados dentro de funções de forma a poder ser usada la dentro. A assinatura se da a criação da função usando `def/2`, definindo o nome da função e em seguida adicionando parênteses. Dentro dos parênteses adicionamos as variáveis posicionais. \
\
`def nome_da_funcao(parametro_1, parametro_2)`\
\
Assim você poderá passar dados para dentro da função e os usa-los lá.\
\
`nome_da_funcao(5, 10)`&#x20;
{% endhint %}

Vamos adicionar ao código o bloco de teste usando a função `test/2`

<pre class="language-elixir" data-title="test/math_test.exs" data-line-numbers><code class="lang-elixir">defmodule MathTest do
  use ExUnit.Case

  describe "Math.sum/2" do
<strong>    test "when successed return result" do
</strong>      
    end
  end
end

</code></pre>

Precisamos agora entender o que seria um teste bem sucedido aqui e para isso devemos seguir algumas etapas do TDD para se tornar mais simples

1. Configurar o teste
2. Executar a chamada
3. Criar afirmações

Para configurar o teste, precisamos da função a ser chamada e dos parâmetros que queremos passar, então executamos essa função com os parâmetros e guardados sua resposta, para podermos utilizarmos em nossas afirmações.

Pelo básico, Queremos a soma do valor `x = 1` e  `y = 7` e a resposta deve ser 8. Vamos converter isso para o código

{% code title="test/math_test.exs" lineNumbers="true" %}
```elixir
defmodule MathTest do
  use ExUnit.Case

  describe "Math.sum/2" do
    test "when successed response the result" do
      # configurando
      x = 1
      y = 7
      
      # Executando a função
      result = Math.sum(x, y)
      
      # criando afirmações
      assert result == 8
    end
  end
end
```
{% endcode %}

Vamos rodar isso e ver o relatório

```shell
mix test
warning: Math.sum/2 is undefined (module Math is not available or is yet to be defined)
  test/math_test.exs:11: MathTest."test Math.sum/2 when successed response the result"/1

.

  1) test Math.sum/2 when successed response the result (MathTest)
     test/math_test.exs:5
     ** (UndefinedFunctionError) function Math.sum/2 is undefined (module Math is not available)
     code: result = Math.sum(x, y)
     stacktrace:
       Math.sum(1, 7)
       test/math_test.exs:11: (test)


Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 1 failure
```

Possuímos nosso primeiro erro. Decidimos usar TDD e o teste vem antes do código a ser testado, então, não possuímos o código e o teste avisou isso para nós. `Math.sum/2 is undefined`. Vamos criar o módulo Math com a função sum com 2 de aridade.

{% hint style="info" %}
**TDD**

\
**Testing driven design** tem como regra guiar nosso código iniciando ele pelos testes. Uma vez que ele falhe temos um ponto de ação a fazer. Sempre focando no menor esforço a se fazer para o teste passar.\
\
[Leia mais sobre](https://martinfowler.com/bliki/TestDrivenDevelopment.html)
{% endhint %}

Crie o novo arquivo e adicione o mínimo necessários para seguirmos em frente.

{% code title="lib/math.ex" lineNumbers="true" %}
```elixir
defmodule Math do
  def sum(_x, _y) do

  end
end

```
{% endcode %}

{% hint style="info" %}
**Utilizando prefixo underline (\_)**

\
Quando temos parâmetros e não o utilizamos, o interpretador nos emite um aviso dizendo que a variável não está sendo utilizada.\
\
`warning: variable "y" is unused (if the variable is not meant to be used, prefix it with an underscore) lib/math.ex:2: Math.sum/2`

``

Para esse aviso sumir, podemos colocar como prefixo o underline (\_) e ele irá ignorar essa validação.

`def sum (_x, _y) do`  &#x20;
{% endhint %}

Salve o arquivo e vamos tentar rodar o teste mais uma vez

```shell
mix test
Compiling 1 file (.ex)
Generated hello_world app
.

  1) test Math.sum/2 when successed response the result (MathTest)
     test/math_test.exs:5
     Assertion with == failed
     code:  assert result == 8
     left:  nil
     right: 8
     stacktrace:
       test/math_test.exs:14: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 1 failure
```

Temos um erro diferente (progresso!). O relatório nos conta que o lado esquerdo de nossa assertion está `nil`, mas o lado direito esperava `8` . Nossa função não esta retornando nada. Podemos ler assim: o resultado da função esta nulo enquanto o esperado no teste é 8.&#x20;

Agora podemos implementar nosso código real. Vamos fazer a soma e retornar o valor.

<pre class="language-elixir" data-title="lib/math.ex" data-line-numbers><code class="lang-elixir">defmodule Math do
  def sum(x, y) do
<strong>    x + y
</strong>  end
end
</code></pre>

Não precisamos de nenhuma palavra chave para indicar que um valor vai ser retornado na função. Elixir faz isso na última linha da função.

Basta agora rodar o teste e ver tudo funcionar.

```shell
mix test
Compiling 1 file (.ex)
..
Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 0 failures
```

## Conclusão

Vimos o básico de funções. Descobrimos que uma função é definida por seu nome e sua aridade. Que toda função nomeada deve estar dentro de um módulo e que podemos passar dados para dentro dela usando parâmetros posicionais.

Entendemos um pouco mais sobre TDD e criamos nosso primeiro teste do zero.
