---
description: Em breve ...
---

# 🔥 Pipes

A tradução direta de `pipes` é `canos`.  Você pode pensar sobre`Canos` como os canos de sua casa.  Eles funcionam para passar água de um lugar para outro. No meio dessa passagem podemos por algumas coisas como, válvulas e filtros, até chegar em nossa torneira pronta para uso.&#x20;

A água seria nossos dados. As válvulas e filtros os redutores e a torneira seria nosso transformador.&#x20;

Normalmente utilizamos funções dessa forma

```elixir
funcao(argumento1)
```

Com pipe, é um pouco diferente.&#x20;

```elixir
argumento_1
|> funcao()
```

A troca da sequência, onde colocamos o parâmetro antes da função nos possibilita encadear várias funções.

```elixir
arumento_1
|> funcao_1()
|> funcao_2()
|> funcao_3()
```

No exemplo acima, a `funcao_1` recebe o `argumento_1,` a `funcao_2` recebe a resposta da `funcao_1` e a `funcao_3` recebe a resposta da `funcao_2`.&#x20;

{% hint style="info" %}
**Aridade em funções**\
****

Se a aridade de uma função for maior do que `1`, Utilize parênteses. Elixir não se importa com isso, mas, é importante para outros programadores, pois podem interpretar mal o seu código. \


`foo # Isso é uma função ou variável?`

`bar() # Aaah, isso é uma função.`
{% endhint %}

Podemos utilizar pipes

* Com funções nativas;
* Em um linha só;

```shell
iex(1)> "Iago Effting" |> String.downcase() |> IO.inspect()
"iago effting"
```

* Com condicionais (mesmo não sendo tão legal de ler)

```shell
iex(1)> 100 \
...(1)> |> case do
...(1)>   100 -> "It's 100"
...(1)>   _ -> "it's something else"
...(1)> end

"It's 100"
```

Podemos utilizar basicamente qualquer função no pipe, seguindo a regra do primeiro parâmetro vir antes.

### Exemplo

Vamos fazer algo então. Precisamos fazer uma sequencia de operações para atender a uma regra.&#x20;

1. Dado um valor vamos
2. **Somar** o valor de entrada por 10;
3. **Multiplicar** o valor por 2;
4. **Diminuir** por 5

Vamos começar sem a utilização de `pipes`. Para isso adicionarei um parâmetro extra em nossa função, onde poderemos colocar  e cair na função que não utiliza pipes ou apenas passando um valor e cairá na função com `pipe`. Vamos sem `pipes` primeiro.

<pre class="language-elixir" data-title="test/pipes_test.exs" data-line-numbers><code class="lang-elixir">defmodule CalculatorTest do
  use ExUnit.Case

  test "Not using pipes" do
    value = 5
    
<strong>    assert Calculator.crazy_rule(value, :without_pipe) == 25
</strong>  end
end</code></pre>

```shell
mix test test/calculator_test.exs
warning: Calculator.crazy_rule/1 is undefined (module Calculation is not available or is yet to be defined)
  test/pipes_test.exs:7: PipesTest."test Not using pipes"/1



  1) test Not using pipes (PipesTest)
     test/pipes_test.exs:4
     ** (UndefinedFunctionError) function Calculator.crazy_rule/1 is undefined (module Calculation is not available)
     code: assert Calculator.crazy_rule(value) == 25
     stacktrace:
       Calculator.crazy_rule(5)
       test/calculator_test.exs:7: (test)


Finished in 0.04 seconds (0.00s async, 0.04s sync)
1 test, 1 failure
```

Não temos criado o modulo, vamos cria-lo.

{% code title="lib/calculator.ex" lineNumbers="true" %}
```elixir
defmodule Calculator do
  def crazy_rule(value, :not_using_pipes) do

  end
end
```
{% endcode %}

```shell
mix test test/calculator_test.exs


  1) test Not using pipes (PipesTest)
     test/calculator_test.exs:4
     Assertion with == failed
     code:  assert Calculator.crazy_rule(value) == 25
     left:  nil
     right: 25
     stacktrace:
       test/calculator_test.exs:7: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Agora podemos seguir.

Precisamos resolver esse modulo usando funções e elas ficam bem claras quais são. Basta pegar os passos que devem ser feitos `somar`, `diminuir` e `multiplicar`.

Podemos resolver isso, assim:

{% code title="" lineNumbers="true" %}
```elixir
defmodule Calculator do
  def crazy_rule(value, :without_pipes) do
    value = sum(value, 10)
    value = multiple(value, 2)
    value = decrease(value, 5)
    
    value # Retorna o valor final
  end
  
  defp sum(current_value, value), do: current + value
  defp multiple(current_value, value), do: current_value * value
  defp decrease(current_value, value), do: current_value - value
end
```
{% endcode %}

Criamos as funções referente a regra e juntamos tudo na função publica `crazy_rule`. Vamos rodar isso.

```shell
mix test test/calculator_test.exs
Compiling 1 file (.ex)
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Tudo funcionando bem e nosso teste passando.

Vamos agora criar utilizando pipes.

<pre class="language-elixir" data-title="test/calculator_test.exs" data-line-numbers><code class="lang-elixir">defmodule PipesTest do
  use ExUnit.Case
  
  # ...

  test "Using pipes" do
    value = 5
    
<strong>    assert Calculator.crazy_rule(value, :with_pipes) == 25
</strong>  end
end</code></pre>

Adicionando a função utilizando `pipes`:

<pre class="language-elixir" data-title="lib/calculator.ex" data-line-numbers><code class="lang-elixir">defmodule Calculator do
  def crazy_rule(value, :without_pipes) do
    value = sum(value, 10)
    value = multiple(value, 2)
    value = decrease(value, 5)

    value # Retorna o valor
  end
  
<strong>  def crazy_rule(value, :with_pipes) do
</strong><strong>    value
</strong><strong>    |> sum(10)
</strong><strong>    |> multiple(2)
</strong><strong>    |> decrease(5)
</strong><strong>  end
</strong>  
  defp sum(current_value, value), do: current_value + value
  defp multiple(current_value, value), do: current_value * value
  defp decrease(current_value, value), do: current_value - value
end</code></pre>

Execute os testes novamente

```shell
mix test test/calculator_test.exs
..
Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 0 failures
```

A utilização de pipes deixa o código mais elegante a fácil de entender. A leitura se torna mais fluída e colocar uma nova função no meio dele fica bem simples, enquanto sem pipes se torna repetitiva e verbosa.

{% hint style="warning" %}
Pipes

As funções no pipe deve seguir uma ordem lógica. Quando a ordem é alterada, o resultado tende a se alterar também. Existem formas de programar pipes, onde isso não será um problema, mas em nosso exemplo, é. Tenha isso em mente.
{% endhint %}

### Conclusão

A utilização pipes se torna simples em colocar funções em uma sequência lógica para atender um requisito, mesmo que estranho. É amplamente utilizado no elixir e recomendado. Devemos sempre seguir a regra de, o primeiro argumento deve vir antes da chamada da função&#x20;

```elixir
argumento_1
|> funcao(argumento_2) # funcao/2
|> outra(argumento_2) # outra/1
```

Pense bem ao utilizar pipes, podem ser uma boa forma de melhorar a legibilidade de seu código.

