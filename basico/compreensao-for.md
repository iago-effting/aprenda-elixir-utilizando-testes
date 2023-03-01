# Compreensão (for)

Vamos começar um pouco diferente nesse capítulo. Vamos direto para o exemplo e ver a necessidade aparecer com o tempo e assim, explicar melhor o conceito de compreensão.&#x20;

### Exemplo

Precisamos filtrar uma lista de números e obter apenas os números pares. Vamos ao teste. Ele deve seguir as regras

* Enviamos uma lista de inteiros para a função
* Essa função nos retorna uma lista de apenas números pares
* Também queremos apenas os numeros entre 0 e 10&#x20;

Vamos crianos nosso teste primeiro:

<pre class="language-elixir" data-title="test/comprehensions_test.exs" data-line-numbers><code class="lang-elixir">defmodule ComprehensionsTest do
  use ExUnit.Case

  test "just pair please" do
    numbers = 1..10
    
    result = Numbers.just_pair_please(numbers)

<strong>    assert is_list(result)
</strong>    assert result == [2,4,6,8,10]  
  end
end

</code></pre>

Na linha 9 adicionamos uma confirmação de que teremos de resultado da operação `for` uma lista. Se assemelhando ao [Enum.map](listas/enum.md#enum.map-2). Vamos rodar esse teste:

```sh
mix test test/numbers_test.exs       
warning: Numbers.just_pair_please/1 is undefined (module Numbers is not available or is yet to be defined)
  test/numbers_test.exs:7: NumbersTest."test just pair please"/1



  1) test just pair please (NumbersTest)
     test/numbers_test.exs:4
     ** (UndefinedFunctionError) function Numbers.just_pair_please/1 is undefined (module Numbers is not available)
     code: result = Numbers.just_pair_please(numbers)
     stacktrace:
       Numbers.just_pair_please(1..10)
       test/numbers_test.exs:7: (test)


Finished in 0.03 seconds (0.00s async, 0.03s sync)
1 test, 1 failure
```

Como esperado, o relatório de erro nos trouxe que a função não existe. Vamos criar ela. Sua definição é `Numbers.`just\_pair\_please`/1`. Vamos replicar isso em nosso código

{% code title="lib/numbers.ex" lineNumbers="true" %}
```elixir
defmodule Numbers do
  def just_pair_please(_number_list) do
    [2,4,6,8,10]
  end
end

```
{% endcode %}

Criado a função, retornamos os valores que precisamos para fazer o teste passar:

```sh
mix test test/numbers_test.exs
Compiling 1 file (.ex)
.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 0 failures
```

Certo, teste passando. Vamos adicionar uma nova lista, para garantir que tudo funciona. Vamos ao teste:

<pre class="language-elixir" data-title="test/numbers_test.exs" data-line-numbers><code class="lang-elixir">defmodule NumbersTest do
  use ExUnit.Case

  test "just pair please" do
    numbers = 1..10
    
    result = Numbers.just_pair_please(numbers)

    assert is_list(result)
    assert result == [2,4,6,8,10]
    
<strong>    result2 = Numbers.just_pair_please(1..5)
</strong><strong>    assert result2 == [2,4]
</strong>  end
end

</code></pre>

Fizemos uma nova chamada a função passando uma lista diferente que vai de 1 a 5. O restultado esperado é 2 e 4, os únicos pares da lista. Vamos rodar isso para ver se tudo funciona com deveria.

```sh
mix test test/numbers_test.exs


  1) test just pair please (NumbersTest)
     test/numbers_test.exs:4
     Assertion with == failed
     code:  assert result == [2, 4]
     left:  [2, 4, 6, 8, 10]
     right: [2, 4]
     stacktrace:
       test/numbers_test.exs:13: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Temos uma resposta diferente do esperado, isso porque estamos com valores estáticos na resposta da função. Precisamos agora deixar mais dinâmica as coisas. Precisamos percorrer a lista e retornar somente valores pares. Podemos fazer isso utilizando um `Enum.filter/2`. Vamos tentar:

<pre class="language-elixir" data-title="lib/numbers.ex" data-line-numbers><code class="lang-elixir">defmodule Numbers do
  def just_pair_please(numbers_list) do
<strong>    Enum.filter(numbers_list, fn number -> rem(number, 2) == 0 end)
</strong>  end
end

</code></pre>

Utilizamos `Enum.filter/2` para conseguir fazer essa operação. Seguinda ideia de que um número par deve ter como resto de divisão por 2 o valor 0. A função `rem/2` pega o resto da operação (casas decimais) e converte para `integer`. Uma vez que for 0 o valor é par. Em `Enum.filter/2` uma vez que seja true o valor, o elemento ficará na lista, quando for falso, será removido. Vamos rodar o teste:

```sh
mix test test/numbers_test.exs
Compiling 1 file (.ex)
.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 0 failures
```

Perfeito! As coisas estão melhorando.&#x20;

* ~~Enviamos uma lista de inteiros para a função~~
* ~~Essa função nos retorna uma lista de apenas números pares~~
* Também queremos apenas os numeros entre 0 e 10&#x20;

Dois pontos foram resolvidos. Precisamos agora permitir que o range de nosso filtro seja apenas para números até o número 10. Vamos criar uma teste para isso:

{% code title="test/numbers_test.exs" lineNumbers="true" %}
```elixir
defmodule NumbersTest do
  use ExUnit.Case

  # ...
  test "just pair please, until 10" do
    result = Numbers.just_pair_please(1..40)

    assert is_list(result)
    assert result == [2,4,6,8,10]
  end
end

```
{% endcode %}

Um teste praticamente igual ao anterior, porém, com o range maior, indo de 1 a 10. Nossa nova regra é, os números devem apenas ir até 10. Vamos rodar o teste":

```sh
mix test test/numbers_test.exs
Compiling 1 file (.ex)


  1) test just pair please, until 10 (NumbersTest)
     test/numbers_test.exs:16
     Assertion with == failed
     code:  assert result == [2, 4, 6, 8, 10]
     left:  [2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40]
     right: [2, 4, 6, 8, 10]
     stacktrace:
       test/numbers_test.exs:20: (test)

.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 1 failure
```

Queriamos os números até 10. Mas temos a resposta dos pares com valores até 40. Então um relatório de erro foi mostrado. Vamos arrumar isso.

<pre class="language-elixir" data-title="lib/numbers.ex" data-line-numbers><code class="lang-elixir">defmodule Numbers do
  def just_pair_please(numbers_list) do
    Enum.filter(numbers_list, fn number ->
<strong>      if number > 10, do: false, else: rem(number, 2) == 0
</strong>    end)
  end
end

</code></pre>

Adicionamos uma nova validação em nosso filtro. Uma condição if em que o número que for maior que 10, deve voltar `false` e com isso, não entrar na lista.

Vamos rodar o teste:

```sh
mix test test/numbers_test.exs
Compiling 1 file (.ex)
..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 0 failures
```

Funcionou perfeitamente. Agora vamos adicionar um novo comportamento. Os dados que restaram em nosso filtro devem ser multiplicados por 3.

* ~~Enviamos uma lista de inteiros para a função~~
* ~~Essa função nos retorna uma lista de apenas números pares~~
* ~~Também queremos apenas os numeros entre 0 e 10~~
* Valores restantes na lista devem ser multiplicados por 3

Vamos alterar nossos testes:

```elixir
defmodule NumbersTest do
  use ExUnit.Case

  test "just pair please" do
    result = Numbers.just_pair_please(1..10)

    assert is_list(result)
    assert result == [6,12,18,24,30]

    result2 = Numbers.just_pair_please(1..5)

    assert is_list(result2)
    assert result2 == [6,12]
  end

  test "just pair please, until 10" do
    result = Numbers.just_pair_please(1..40)

    assert is_list(result)
    assert result == [6,12,18,24,30]
  end
end

```

Vamos rodar o teste:

```sh
mix test test/numbers_test.exs


  1) test just pair please (NumbersTest)
     test/numbers_test.exs:4
     Assertion with == failed
     code:  assert result == [6, 12, 18, 24, 30]
     left:  [2, 4, 6, 8, 10]
     right: [6, 12, 18, 24, 30]
     stacktrace:
       test/numbers_test.exs:8: (test)



  2) test just pair please, until 10 (NumbersTest)
     test/numbers_test.exs:16
     Assertion with == failed
     code:  assert result == [6, 12, 18, 24, 30]
     left:  [2, 4, 6, 8, 10]
     right: [6, 12, 18, 24, 30]
     stacktrace:
       test/numbers_test.exs:20: (test)


Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 2 failures
```

Duas falhas. Nosso teste esta pedindo que os valores retornados sejam multiplicado por e. com isso precisamos realizar uma transformação em nossa lista. Mas `Enum.filter/2` não serve para isso, ele serve apenas para filtrar. Quem faz transformações de um [enumerável](../conceitos/enumeraveis.md) é o [`Enum.map`](listas/enum.md#enum.map-2)`/2`. Vamos ter que obter o resultado do `Enum.filter/2`, para conseguir usar em um [`Enum.map/2`](listas/enum.md#enum.map-2). Vamos implementar:

{% code title="lib/numbers.ex" lineNumbers="true" %}
```elixir
defmodule Numbers do
  def just_pair_please(numbers_list) do
    list_filtered = Enum.filter(numbers_list, fn number ->
      if number > 10, do: false, else: rem(number, 2) == 0
    end)
    
    Enum.map(list_filtered, fn number -> 
      number * 3
    end)
  end
end

```
{% endcode %}

Colocamos a lista filtrada em uma variável chamada `list_filtered`. Depois utilizamos o `Enum.map/2` para realizar a transformação, passando `list_filtered` como primeiro argumento e o segundo a função de transformação, obtendo o valor de um número e multiplicando por 3.&#x20;

Vamos executar o teste para ver como estamos:

```sh
mix test test/numbers_test.exs
Compiling 1 file (.ex)
..
Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 0 failures
```

Conseguimos realizar todas as regras de negócio que precisamos.

* ~~Enviamos uma lista de inteiros para a função~~
* ~~Essa função nos retorna uma lista de apenas números pares~~
* ~~Também queremos apenas os numeros entre 0 e 10~~
* ~~Valores restantes na lista devem ser multiplicados por 3~~

Mas em um problema relativamente simples, temos um código maior. Não é necessariamente um problema, o código é claro e fácil de entender após lermos com atenção. Porém, existe uma forma de resumir isso caso você queira. Chamamos isso de sintaxy sugar.

Em elixir temos a syntax sugar chamada `Comprehension` que é criado em três partes: filtro, transformação e coleção. Justamente o que precisamos agora. Uma explicação rápida:

### Compreensão

Compreensões em Elixir são uma forma concisa e expressiva de criar, filtrar, transformar e combinar coleções em uma única linha de código. As compreensões em Elixir são inspiradas nas compreensões de listas em Python e nas expressões de conjunto em matemática.

Uma compreensão é composta por três partes principais:

* A cláusula `for`: que especifica uma ou mais variáveis e as expressões que geram os valores para essas variáveis.

```elixir
for n <- 1..10
```

* A cláusula `do`: que define as transformações a serem aplicadas a cada valor gerado pela cláusula `for`.

```elixir
for n <- 1..10, do: n*n
```

* Opcionalmente, cláusulas adicionais, como `if` ou `unless`, que filtram os valores gerados pela cláusula `for`.

```elixir
for n <- 1..10, rem(n, 2), do: n*n # usando if
for n <- 1..10, unless rem(n, 2) == 0, do: n # usando unless
```

Vamos voltar ao exemplo

### Refatorando

Não precisamos mais tocar no teste, uma vez que toda a regra de negócio está refletida lá. O que precisamos agora é deixar nosso implementação melhor, vamos lá:

<pre class="language-elixir"><code class="lang-elixir">defmodule Numbers do
  def just_pair_please(numbers_list) do
<strong>    for number &#x3C;- numbers_list, rem(number, 2) == 0 &#x26;&#x26; number &#x3C;= 10  do
</strong><strong>      number * 3
</strong><strong>    end
</strong>  end
end

</code></pre>

O que vocês acham da nova implementação? Bem mais clara, não acham? Vamos rodar os testes:

```sh
mix test test/numbers_test.exs
Compiling 1 file (.ex)
..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 0 failures
```

Perfeito, refatoramos de forma a ficar mais claro e fácil de entender. Mantivemos todos os testes passando o que garante o funcionamento de nossa implementação.&#x20;

### Conclusão

Compreensão pode ser útil para diminuir o tamanho de nosso código. Porém isso pode causar problemas de legibilidade, então, minha dica é: entendam o que vocês precisam no momento e tomem uma decisão conciente. Tanto um quanto o outro estão corretos.



