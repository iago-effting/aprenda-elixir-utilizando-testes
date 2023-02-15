# Guards

Os guards ajudam a aumentar a força na utilização de pattern matching em funções. Eles podem servir de controle de fluxo para uma determinada função ou até garantir que um tipo especifico esta passando.

Vamos supor que precisamos imprimir o dado que é passado como argumento da função, mas temos uma peculiaridade. Podemos passar tanto número inteiro, string e até map. A forma como esses três devem ser imprimidos é diferente. Vamos a um teste simples:

{% code title="test/printer_test.exs" lineNumbers="true" %}
```elixir
defmodule PrinterTest do
  use ExUnit.Case

  test "print/1" do
    assert Printer.print(1) == "Number: 1"
    assert Printer.print("Hello") == "Text: Hello"
    assert Printer.print(%{name: "iago"}) == "Map with name: iago"
  end
end

```
{% endcode %}

Vamos rodar-lo:

```sh
mix test test/printer_test.exs
Compiling 1 file (.ex)
Generated hello_world app
warning: Printer.print/1 is undefined (module Printer is not available or is yet to be defined)
Invalid call found at 3 locations:
  test/printer_test.exs:5: PrinterTest."test print/1"/1
  test/printer_test.exs:6: PrinterTest."test print/1"/1
  test/printer_test.exs:7: PrinterTest."test print/1"/1



  1) test print/1 (PrinterTest)
     test/printer_test.exs:4
     ** (UndefinedFunctionError) function Printer.print/1 is undefined (module Printer is not available)
     code: assert Printer.print(1) == "Number: 1"
     stacktrace:
       Printer.print(1)
       test/printer_test.exs:5: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Recebemos relatório de erro por não possuir o módulo e função utilizados. Vamos cria-los. Para reoslver esse problema, poderiamos usar cond facilmente:

{% code title="lib/printer.ex" lineNumbers="true" %}
```elixir
defmodule Printer do
  def print(arg) do
    cond do
      is_integer(arg) -> "Number: #{arg}"
      is_bitstring(arg) -> "Text: #{arg}"
      is_map(arg) -> "Map with name: #{arg.name}"
      true -> "Noops"
    end
  end
end

```
{% endcode %}

```sh
mix test test/printer_test.exs
Compiling 1 file (.ex)
Generated hello_world app
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Isso parece bom. Resolve nosso problema de forma elegante, acredito. Mas estamos lidando com um problema que resolvemos basicamente uma linha por tipo. Mas e se tivermos que fazer uma operação maior. Começa a se formar uma bagunça. Vamos adicionar uma operação superficial para simular isso. Vamos alterar o primeiro teste para saber se um número é par ou impar.

```elixir
defmodule PrinterTest do
  use ExUnit.Case

  test "print/1" do
    assert Printer.print(2) == "Number 2 is even"
    assert Printer.print(1) == "Number 1 is odd"
    assert Printer.print("Hello") == "Text: Hello"
    assert Printer.print(%{name: "iago"}) == "Map with name: iago"
  end
end

```

Vamos alterar nossa implementação, utilizando ainda o `cond/2`.

{% code title="lib/printer.ex" lineNumbers="true" %}
```elixir
defmodule Printer do
  require Integer

  def print(arg) do
    cond do
      is_integer(arg) ->
        if (Integer.is_even(arg) == true) do
          "Number #{arg} is even"
        else
          "Number #{arg} is odd"
        end

      is_bitstring(arg)
        -> "Text: #{arg}"

      is_map(arg)
        -> "Map with name: #{arg.name}"
      true -> "Noops"
    end
  end
end

```
{% endcode %}

A leitura começou a ficar um pouco bagunçada, nao? Isso foi uma mudança pequena. Você pode me dizer que poderiamos usar uma função ali. E você está certo em relação a isso. Mas ao invez disso, porque não isolamos a função `print/1` para cada tipo de dado de entrar? Podemos fazer isso utilizando clauses. Elas são definidas ao lado da definição da função iniciando com a palavra chave `when` e uma operação a seguir. Vamos la:

<pre class="language-elixir" data-title="lib/printer.ex" data-line-numbers><code class="lang-elixir">defmodule Printer do
  require Integer

<strong>  def print(arg) when is_integer(arg) do
</strong>    if (Integer.is_even(arg) == true) do
      "Number #{arg} is even"
    else
      "Number #{arg} is odd"
    end
  end

<strong>  def print(arg) when is_bitstring(arg), do: "Text: #{arg}"
</strong><strong>  def print(arg) when is_map(arg), do: "Map with name: #{arg.name}"
</strong>end

</code></pre>

Não preciso falar o quanto a leitura melhorou nesse exemplo certo? Temos diversas outras vantagens como, facilidade de extração e adição de novos tipos. Você pode perceber que as funções são controladas pelas funções seguidas do `when`. É ali que os guards moram.

Rodamos os teste e pronto:

```sh
mix test test/printer_test.exs
Compiling 1 file (.ex)
.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 0 failures
```

### Limitações

Podemos pensar que guards podem ser usados até em operações complexas. Mas existe uma regras de utilização dos guards. Não podemos adicionar módulos nosso em nossas condicionais. Precisamos utilizar somente o básico da linguage. Foi uma decisão deliberada pela linguagem para evitar certos problemas e complexidades em cima dos `guards`.

[Aqui uma lista do que é permitido.](https://hexdocs.pm/elixir/guards.html#list-of-allowed-expressions)

