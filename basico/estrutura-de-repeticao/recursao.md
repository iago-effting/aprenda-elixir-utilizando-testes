# Recursão

Um assunto interessante em programação funcional é a recursão. Um recurso usado para lidar com a impossibilidade de manter estado falado no capítulo sobre [imutabilidade](../../conceitos/imutabilidade.md).

O básico de uma recursão é: Uma função que se chama, criando um looping onde vai até sair fora dessa chamada. Vamos a um exemplo real.

{% hint style="info" %}
[capture\_log](https://hexdocs.pm/ex\_unit/1.13.4/ExUnit.CaptureLog.html) como o próprio nome diz, captura o log tendo o objetivo de ser usado em testes.
{% endhint %}

Primeiro o teste:

{% code title="test/recursion_test.exs" lineNumbers="true" %}
```elixir
defmodule RecursionTest do
  use ExUnit.Case

  import ExUnit.CaptureLog

  describe "print_loop/1" do
    test "Print x until x = 0" do
      x = 5

      execution = capture_log(fn ->
        Recursion.print_loop(x)
      end)

      assert capture_log(execution) =~ "Printed number 5"
      assert capture_log(execution) =~ "Printed number 4"
      assert capture_log(execution) =~ "Printed number 3"
      assert capture_log(execution) =~ "Printed number 2"
      assert capture_log(execution) =~ "Printed number 1"
    end
  end
end

```
{% endcode %}

Queremos registrar 5 vezes uma mensagem para entender que fez um loop de 5 vezes, onde o único dado alterado é o x onde vai de 5 a 1. Para isso utilizaremos o [capture\_log](https://hexdocs.pm/ex\_unit/1.13.4/ExUnit.CaptureLog.html) que serve para capturar logs feitos na aplicação.

Vamos rodar esse código

```sh
mix test test/recursion_test.exs
warning: Recursion.print_loop/1 is undefined (module Recursion is not available or is yet to be defined)
  test/recursion_test.exs:11: RecursionTest."test print_loop/1 Print x until x = 0"/1



  1) test print_loop/1 Print x until x = 0 (RecursionTest)
     test/recursion_test.exs:7
     ** (UndefinedFunctionError) function Recursion.print_loop/1 is undefined (module Recursion is not available)
     code: execution = capture_log(fn ->
     stacktrace:
       Recursion.print_loop(5)
       (ex_unit 1.14.3) lib/ex_unit/capture_log.ex:108: ExUnit.CaptureLog.with_log/2
       (ex_unit 1.14.3) lib/ex_unit/capture_log.ex:77: ExUnit.CaptureLog.capture_log/2
       test/recursion_test.exs:10: (test)


Finished in 0.04 seconds (0.00s async, 0.04s sync)
1 test, 1 failure
```

Obtivemos o relatório de erro por não termos o módulo nem a função criados. Vamos cria-las.

<pre class="language-elixir" data-title="lib/recursion.ex" data-line-numbers><code class="lang-elixir">defmodule Recursion do
  require Logger

<strong>  def print_loop(x) do
</strong>    Logger.info("Printed number #{x}")

<strong>    print_loop(x - 1)
</strong>  end
end

</code></pre>

Como podemos ver, na linha 7 temos a chamada para a mesma função que definimos na linha 4. Isso é a recursão, algo chamando a si mesmo, criando um laço de repetição. Porém, se rodarmos esse código, teremos um loop infinito, pois não temos um condição de parada. Caso execute esse código teremos o terminal congelado esperando a operação finalziar, mas isso não irá acontecer, então precisamos apertar CTRL + C e abortar a operação.

```sh
mix test test/recursion_test.exs
^C
BREAK: (a)bort (A)bort with dump (c)ontinue (p)roc info (i)nfo
       (l)oaded (v)ersion (k)ill (D)b-tables (d)istribution
^C%
```

Para isso não acontecer, precisamos de uma condição de parada, sendo a primeira coisa que definimos em nossa recursão. Nesse exemplo queremos que o X seja maior que 0. Essa é nossa condição. Para por isso em nossa função utilizaremos os [guards](../guards.md). Vamos alterar nossa implementação.

<pre class="language-elixir" data-title="lib/recursions.ex" data-line-numbers><code class="lang-elixir">defmodule Recursion do
  require Logger

<strong>  def print_loop(x) when x > 0 do
</strong>    Logger.info("Printed number #{x}")

    print_loop(x - 1)
  end
end

</code></pre>

Na linha 4, ao lado da definição da função temos a palavra chave when seguida de uma condição. Ele garantirá que nosso x seja maior que 0. Caso não seja, ele não ta match com esse função, procurando uma outra definição para seguir o processo do programa. Caso não encontre, extoura uma exceção. Vamos rodar esse teste.

```sh
mix test test/recursion_test.exs
Compiling 1 file (.ex)


  1) test print_loop/1 Print x until x = 0 (RecursionTest)
     test/recursion_test.exs:7
     ** (FunctionClauseError) no function clause matching in Recursion.print_loop/1

     The following arguments were given to Recursion.print_loop/1:
     
         # 1
         0
     
     Attempted function clauses (showing 1 out of 1):
     
         def print_loop(x) when x > 0
     
     code: assert capture_log(execution) =~ "Printed number 5"
     stacktrace:
       (hello_world 0.1.0) Recursion.print_loop/1
       (ex_unit 1.14.3) lib/ex_unit/capture_log.ex:108: ExUnit.CaptureLog.with_log/2
       (ex_unit 1.14.3) lib/ex_unit/capture_log.ex:77: ExUnit.CaptureLog.capture_log/2
       (ex_unit 1.14.3) lib/ex_unit/capture_log.ex:108: ExUnit.CaptureLog.with_log/2
       (ex_unit 1.14.3) lib/ex_unit/capture_log.ex:77: ExUnit.CaptureLog.capture_log/2
       test/recursion_test.exs:14: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Uma vez que o `x` chegou a `0`, nossa função não se apresentava mais como um match e ganhamos com isso uma `FunctionClauseError` que significa que que a clausura para acessar essa função não foi atendida.

O que esta correto, não queremos a recursão de valores abaixo de 1. Queremos apenas maiores que 0. O que precisamos fazer agora é silenciar essa exceção. Para isso, podemos criar uma função de mesmo nome abaixo da criada anteriormente, fazer com que aceite valores abaixo de 1 e retornar algum valor como por exemplo `nil`. Vamos alterar nossa implementação:

<pre class="language-elixir" data-title="lib/recursion_test.exs" data-line-numbers><code class="lang-elixir">defmodule Recursion do
  require Logger

  def print_loop(x) when x > 0 do
    Logger.info("Printed number #{x}")

    print_loop(x - 1)
  end

<strong>  def print_loop(_x), do: nil
</strong>end

</code></pre>

Na linha 10, adicionamos uma nova definição da função `print_loop/1`, onde apenas espera receber um valor qualquer e retorna um `nil`. Não temos nenhuma condição em relação a isso porque esse é um cenário onde esperamos parar o looping. Então, quando chegamos a x = 0, a primeira fução que cotém o codicionameto `x >0` não será mais atendido e irá para a proxima função de mesmo nome. Nessa nova função da linha 10 espera um valor qualquer, sem qualquer regra ou condição, logo, nosso `x = 0` será atendido e iremos executar essa função. Executando essa função, a unica coisa que ele faz é retornar `nil`. Ela não executa novamente a própria função, isso causa a parada da repetição e a finalização da recursão.

Vamos rodar esse teste:

```sh
mix test test/recursion_test.exs
Compiling 1 file (.ex)
.
Finished in 0.08 seconds (0.00s async, 0.08s sync)
1 test, 0 failures
```

Nosso relatório de teste nos trouxe sucesso. Tivemos então um looping em cima da função `print_loop/1`. Finalizando a recursão utilizando [guards](../guards.md).

