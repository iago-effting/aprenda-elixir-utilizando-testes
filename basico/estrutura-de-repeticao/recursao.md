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

### Utilizando head | tail

Entendo uma vez o conceito de recursão, temos algumas facilidades que o elixir nos proporciona. Chamamos de `head | tail` (cabeça e cauda). Ele diz respseito ao processamento de listas, como por exemplo `[1,2,3,4,5]`. O número 1 seria a cabeça (primeiro elemento da lista) enquanto o restante `[2, 3, 4, 5]` seria a cauda. Isso pode ser utilizado para saber qual os proximos elementos de uma recursão. Vamos a um exemplo.

Precisamos processar números de uma lista que vai do 1 até o 10. Cada iteração utilizaremos o Logger para falar que passamos por lá, muito parecido com o exemplo anterior. Mas nesse caso estamos lidando com uma lista.

Primeiro, vamos o teste de parada, para não termos um loop infinito:

{% code title="test/my_list_test.exs" lineNumbers="true" %}
```elixir
defmodule MyListTest do
  use ExUnit.Case

  import ExUnit.CaptureLog

  describe "process/1" do
    test "the list are empty" do
      elements = []

      execution = fn ->
        capture_log(fn -> MyList.process(elements) end)
      end
      
      assert capture_log(execution) =~ "the list are empty"
    end
  end
end

```
{% endcode %}

Simplesmente queremos que um looping seja feito e que seja impresso uma vez cada elemento da lista. Vamos rodar para ter nosso erro

```sh
mix test test/my_list_test.exs
Compiling 1 file (.ex)
warning: MyList.process/1 is undefined or private
  test/my_list_test.exs:11: MyListTest."test process/1 the list are empty"/1



  1) test process/1 the list are empty (MyListTest)
     test/my_list_test.exs:7
     ** (UndefinedFunctionError) function MyList.process/1 is undefined or private
     code: assert capture_log(execution) =~ "the list are empty"
     stacktrace:
       (hello_world 0.1.0) MyList.process([])
       (ex_unit 1.14.3) lib/ex_unit/capture_log.ex:108: ExUnit.CaptureLog.with_log/2
       (ex_unit 1.14.3) lib/ex_unit/capture_log.ex:77: ExUnit.CaptureLog.capture_log/2
       (ex_unit 1.14.3) lib/ex_unit/capture_log.ex:108: ExUnit.CaptureLog.with_log/2
       (ex_unit 1.14.3) lib/ex_unit/capture_log.ex:77: ExUnit.CaptureLog.capture_log/2
       test/my_list_test.exs:14: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Como esperado, o relatório nos avisou que `MyList.process/1` não foi definido. Vamos a implementação. Primeiramente precisamos decidir o critério de parada. O conceito de recursão continua o mesmo, a função vai se chamar, até que por algum motivo o parâmetro que inserimos na função faça com que outra função de mesmo nome seja invocada e faça a parada da recursão.

Utilizaremos o conceito de `head|tail`. Sendo `head` o primeiro elemento, e `tail` o restante. Com isso podemos entender que ao chamar a propria função na recursão enviaremos o `tail` para a proxima iteração, seguindo até o final onde teremos uma lista vazia e isso causará a parada da recursão.

Vamos implementar primeiro a condição de parada:

{% code title="lib/my_list.ex" lineNumbers="true" %}
```elixir
defmodule MyList do
  require Logger

  def process([]) do
    Logger.info("the list are empty")
  end
end

```
{% endcode %}

Utilizamos pattern matching para dizer que, ao receber uma lista vazia por parâmetro, não chamaremos a função process dentro dela mesmo. Isso causará a parada da recursão. Também logamos o registro `the list are empty`, para garantirmos que estamos corretos. Vamos rodar o teste novamente

```sh
mix test test/my_list_test.exs
Compiling 1 file (.ex)
.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 0 failures
```

Perfeito! Temos a parada, agora precisamos fazer a recursão utilizando `head|tail`. vamos começar pelo teste:

{% code title="lib/my_list.ex" lineNumbers="true" %}
```elixir
defmodule MyListTest do
  use ExUnit.Case

  import ExUnit.CaptureLog

  describe "process/1" do
    # ...
    test "print all elements" do
      elements = [1,2,3,4,5]

      execution = fn ->
        capture_log(fn -> MyList.process(elements) end)
      end

      assert capture_log(execution) =~ "head is 1 and tail is [2, 3, 4, 5]"
      assert capture_log(execution) =~ "head is 2 and tail is [3, 4, 5]"
      assert capture_log(execution) =~ "head is 3 and tail is [4, 5]"
      assert capture_log(execution) =~ "head is 4 and tail is [5]"
      assert capture_log(execution) =~ "head is 5 and tail is []"
      assert capture_log(execution) =~ "the list are empty"
    end
  end
end

```
{% endcode %}

De forma simples, queremos apenas que cada iteração faz um log do valor de head e tail para exemplificar como o looping se comporta. Novamente `head` é o primeiro valor da lista, sem ser uma lista e `tail` é o restante sendo uma lista. Se tivermos `[1,2,3]` o `head` é o número `1` e `tail` é a lista `[2,3]`.

{% hint style="info" %}
**Interpolação**.

\
Podemos inserir valores em uma string utilizando a interpolação. Dentro de uma string definida qualquer texto que tem envolta aspas duplas. Para demarcar o local utilizamos jogo da velha abrir chaves, a variável a ser impressa e fechar chaves.&#x20;
{% endhint %}

{% hint style="info" %}
**inspect**\
\
Para imprimir um valor complexo como listas e mapas como texto, precisamos utilizar a função `inspect/1` onde converterá os dados para texto.
{% endhint %}

Vamos a implementação

<pre class="language-elixir" data-title="lib/my_list.ex" data-line-numbers><code class="lang-elixir">defmodule MyList do
  require Logger

  def process([]) do
    Logger.info("the list are empty")
  end

<strong>  def process([head | tail]) do
</strong>    Logger.info("head is #{head} and tail is #{inspect(tail)}")

    process(tail)
  end
end

</code></pre>

Criamos uma nova função embaixo da criada como critério de parada com o mesmo nome. Utilizamos então o `head | tail` na entrada do parâmetro com a notação padrão `[head | tail]`, conseguindo nosso `head` e nosso `tail` de forma fácil. Agora precisamos nos ater a duas coisas

1. `head` é o valor atual, não sendo uma lista. Esse valor usaremos para realizar algo nessa iteração. Nesse nosso exemplo, logaremos `"Printed number #{head}"`.
2. `tail` é uma lista dos elementos restantes que deve ser passada para a proxima iteração.

Podemos rodar o teste novamente e ver o que acontece:

```sh
mix test test/my_list_test.exs
..
Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 0 failures
```

O relatório de sucesso é imprimido, isso quer dizer que nossa recursão utilizando `head | tail` foi um sucesso! Esse conteúdo pode ser um pouco estanho quando vemos pela primeira vez, mas com o tempo e prática vai se tornar claro.

### Conclusão

A utilização de recursão é altamente utilizado em programações funcionais. Elixir não é diferente. Ele possibilidade diversas funções. No próximo capítulo utilizaremos funções nativas do Elixir que se beneficiam da recursão.



