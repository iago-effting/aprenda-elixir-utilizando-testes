# List - old

Como em toda linguagem de programação, temos listas dentro do elixir. Também conhecidos como `array` ou `slices`, são uma coleção de dados. Podemos ter listas simples com apenas dados primitivos `[1, "b", :c, 4]` como também pode conter dados complexos como estruturas ou maps `[%{}, %User{}]`.

Eles servem basicamente para transportar uma coleção de dados. Em elixir temos um módulo que lida com esse tipo de dado diretamente chamado [`List`](https://hexdocs.pm/elixir/1.12/List.html). Ele possue funções auxiliadoras como por exemplo

* `List.first/1`
* `List.last/1`
* `List.delete/2`

Listas utilizam o protocolo Enumerable, onde da suporte a diversas funções encontradas no módulo [enum](../basico/estrutura-de-repeticao/enum.md). Também tendo a possibilidade da utilização de algumas funções do módulo Kernel.

* [`++/2`](https://hexdocs.pm/elixir/1.12/Kernel.html#++/2)
* [`--/2`](https://hexdocs.pm/elixir/1.12/Kernel.html#--/2)
* [`hd/1`](https://hexdocs.pm/elixir/1.12/Kernel.html#hd/1)
* [`tl/1`](https://hexdocs.pm/elixir/1.12/Kernel.html#tl/1)
* [`in/2`](https://hexdocs.pm/elixir/1.12/Kernel.html#in/2)
* [`length/1`](https://hexdocs.pm/elixir/1.12/Kernel.html#length/1)

Como você pode ver, podemos brincar bastante com uma lista. Vamos a algum exemplo prático para entender melhor.

### Exemplo

Temos uma lista que vai de 1 a 5. Queremos fazer algumas coisas com essa lista.

1. Adicionar os elementos 6, 7 e 8 a lista.
2. Deletar o elemento 5;
3. Obter o tamanho da lista, sendo 7;
4. Verificar se o elemento 8 foi adicionado a ela.

Vamos ao nosso teste do primeiro caso.

### Oeprador `++/2`

<pre class="language-elixir" data-title="my_list_test.exs" data-line-numbers><code class="lang-elixir">defmodule MyListTest do
  use ExUnit.Case

  import ExUnit.CaptureLog

  describe "playing with a list" do
    test "adding items on the list" do
<strong>      list = [1, 2, 3, 4, 5] ++ [6, 7, 8]
</strong>      expected = [1, 2, 3, 4, 5, 6, 7, 8]

      assert list == expected
    end
  end
end

</code></pre>

```sh
mix test test/my_list_test.exs
.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 0 failures
```

### Operador `--/2`

O operado ++ junta duas listas retornarndo uma nova lista. Essa expressão é auto-explicativa e de fácil entendimento. Muito útil no dia-a-dia.

Vamos deletar o elemento 5 da lista.

{% code title="my_list_test.exs" lineNumbers="true" %}
```elixir
defmodule MyListTest do
  use ExUnit.Case

  import ExUnit.CaptureLog

  describe "playing with a list" do
    # ...
    test "removing item on the list" do
      list = [1, 2, 3, 4, 5] -- [2, 5]
      expected = [1, 3, 4]

      assert list == expected
    end
  end
end

```
{% endcode %}

```sh
mix test test/my_list_test.exs
.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
1 test, 0 failures
```

Temos o operador `--` que remove itens de uma lista. Lembrando que deve ser usado duas listas para isso, a do lado esquerdo que se mantera e do lado direito que será os itens a ser removidos.

### Operador `in/2`

Muitas vezes precisamos saber se um elemento existe dentro de uma lista. Para isso podemos usar o operador in quando temos dados simples ou dados primitivos.

{% code title="test/my_list_test.exs" lineNumbers="true" %}
```elixir
defmodule MyListTest do
  use ExUnit.Case

  import ExUnit.CaptureLog

  describe "playing with a list" do
    # ...
    test "the number is in the collection?" do
      list = [1,2,3,4,8]

      assert 8 in list == true
      assert 10 in list == false
    end
  end
end

```
{% endcode %}

```sh
mix test test/my_list_test.exs
...
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 0 failures
```

A semantica desse operador é clara. Queremos saber se um valor esta presente em uma lista. ele retorna sempre um boleano, sendo `true` ou `false`.

### Função `length/1`&#x20;

Muitas vezes precisamos saber o tamanho de uma lista. Temos a função `length/1` para isso.

{% code title="test/my_list_test.exs" lineNumbers="true" %}
```elixir
defmodule MyListTest do
  use ExUnit.Case

  import ExUnit.CaptureLog

  describe "playing with a list" do
    # ...
    test "what is the length of collectin" do
      list = [1, 2, 5, 7, 8]
      expected = 5

      assert length(list) == expected
    end
  end
end

```
{% endcode %}

```sh
mix test test/my_list_test.exs
....
Finished in 0.03 seconds (0.00s async, 0.03s sync)
4 tests, 0 failures
```

A função `length/1` retorna um inteiro com a quantidade de elementos em uma lista..

### Conclusão

Existem diversas formas de mexer com uma lista. Sendo útil em nosso dia-a-dia. Existem outras funções que são bons auxiliadores. Mais a frente iremos ver outros como o módulo [Enum](../conceitos/convencoes.md).
