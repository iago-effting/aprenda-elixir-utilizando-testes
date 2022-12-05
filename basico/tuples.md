# Tuples

A tupla é uma estrutura de dados simples que aceita todos os tipos de dados. Diferente do map ela não aceita chave valor e sim, diretamente o valor. Iniciamos sua declaração com abrir chaves `{` e finalizamos com fechar chaves `}`.  Seus valores são separados por virgula.&#x20;

Exemplo :

<pre class="language-elixir" data-line-numbers><code class="lang-elixir">defmodule TupleTest do
  use ExUnit.Case

  test "simple tests about tuple" do
    int_value = 1
    atom_value = :ok
    map_value = %{name: "iago"}

<strong>    tuple = {int_value, atom_value, map_value, 10}
</strong>
<strong>    assert tuple == {1, :ok, %{name: "iago"}, 10}
</strong>  end
end

</code></pre>

```shell
mix test test/tuple_test.exs
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Você verá muito pela comunidade elixir o padrão de utilização de tuplas para simbolizar sucesso de uma função, separando elas como `{:ok, data}` e `{:error, reason}`. Diversas bibliotecas utilizam esse mesmo padrão e é uma forma de aprender a lidar com os dados.

Ao lidar com tuplas, temos algumas funções para nos ajudar

* ``[`Tuple.insert_at/2`](https://hexdocs.pm/elixir/1.12/Tuple.html#insert\_at/3) - Inserir valor ao final da tuple
* [`elem/2`](https://hexdocs.pm/elixir/1.12/Kernel.html#elem/2) - Acessar a tupla por um indice
* [`put_elem/3`](https://hexdocs.pm/elixir/1.12/Kernel.html#put\_elem/3) - Substituir o valor de um elemento da tupla por um indice
* [`tuple_size/1`](https://hexdocs.pm/elixir/1.12/Kernel.html#tuple\_size/1) - obter número de elementos na tupla

Outro exemplo simples:

{% code title="test/tuple_test.exs" lineNumbers="true" %}
```elixir
defmodule TupleTest do
  use ExUnit.Case

  #...

  test "inserting elements" do
    data = {:initial}
    
    data = Tuple.insert_at(data, 1, :foo)
    data = Tuple.insert_at(data, 2, :bar)

    assert data == {:initial, :foo, :bar}
    assert tuple_size(data) == 3
    assert elem(data, 1) == :foo
  end
end
```
{% endcode %}

```shell
mix test test/tuple_test.exs
..
Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 0 failures
```

Tuplas também podem ter valores complexos como `structures` ou `maps`.

### Utilização de tupla de `:ok` e `:error` em uma função

Vamos supor que faremos uma operação de cadastro em um fornecedor. Após a operação precisamos informar se a operação foi bem sucedida ou não. Para facilitar chamarei esse modulo de `Vendor` e a função de `register`. No registro precisamos apenas de um ID e uam descrição. Então passaremos dois parâmetros para a função. Queremos como retorno, a utilização de tuplas com `:ok` e :`error` chamado de [error\_handling](https://elixirschool.com/pt/lessons/intermediate/error\_handling) (ou, tratamento de erro).

{% code title="test/register_test.exs" lineNumbers="true" %}
```elixir
defmodule VendorTest do
  use ExUnit.Case

  describe "register/2" do
    test "when params are valid" do
      id = 54853
      description = "Novo ponto de venda"

      {:ok, result} = Vendor.register(id, description)

      assert result == %{description: "Novo ponto de venda", id: 54853}
    end
  end
end

```
{% endcode %}

Conseguimos extrair o result utilizando `pattern matching`. O `:ok`  garante que so poderá ter um matching caso venha um `:ok` no primeiro elemento da tupla.&#x20;

Se rodarmos isso teremos os primeiros relatórios de erro

```shell
mix test test/vendor_test.exs
warning: Vendor.register/2 is undefined (module Vendor is not available or is yet to be defined)
  test/vendor_test.exs:8: VendorTest."test register/2"/1



  1) test register/2 (VendorTest)
     test/vendor_test.exs:4
     ** (UndefinedFunctionError) function Vendor.register/2 is undefined (module Vendor is not available)
     code: {:ok, result} = Vendor.register(id, description)
     stacktrace:
       Vendor.register(54853, "Novo ponto de venda")
       test/vendor_test.exs:8: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Precisamos criar nosso módulo com nossa função

<pre class="language-elixir" data-title="lib/vendor.ex" data-line-numbers><code class="lang-elixir">defmodule Vendor do
  def register(id, description) do
    result = %{id: id, description: description}
<strong>    {:ok, result} # temos a tupla com o :ok simbolizando que tudo deu certo
</strong>  end
end
</code></pre>

```shell
mix test test/vendor_test.exs
Compiling 1 file (.ex)
.
Finished in 0.00 seconds (0.00s async, 0.00s sync)
1 test, 0 failures
```

O cenário feliz foi definido e tudo esta funcionando. Agora, precisamos também garantir o comportamento para a tupla com `:error`. Para isso iremos garantir que so possa ser enviado por parâmetro IDs do tipo inteiro.

{% code title="" lineNumbers="true" %}
```elixir
defmodule VendorTest do
  use ExUnit.Case

  describe "register/2" do
    # ...

    test "when params are not valid" do
      id = "invalid-id"
      description = "Novo ponto de venda"

      {:error, reason} = Vendor.register(id, description)

      assert reason == "Params are not valid"
    end
  end
end
```
{% endcode %}

Se rodarmos isso veremos que ele gera um novo erro para nós

```shell
mix test test/vendor_test.exs
Compiling 1 file (.ex)
.

  1) test register/2 when params are not valid (VendorTest)
     test/vendor_test.exs:14
     ** (FunctionClauseError) no function clause matching in Vendor.register/2

     The following arguments were given to Vendor.register/2:
     
         # 1
         "invalid-id"
     
         # 2
         "Novo ponto de venda"
     
     Attempted function clauses (showing 1 out of 1):
     
         def register(id, description) when is_integer(id)
     
     code: {:error, reason} = Vendor.register(id, description)
     stacktrace:
       (hello_world 0.1.0) lib/vendor.ex:2: Vendor.register/2
       test/vendor_test.exs:18: (test)


Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 1 failure
```

Isso significa que nao foi encontrado uma função que atenda as espectativas dessa chamada. Devido a precisar ser inteiro e estarmos enviando String o código quebra.

Para resolver esse problema, precisamos criar uma nova função de mesmo nome que atenda a nova especificação. No nosso caso, criaremos uma apenas para informar que algo esta errado e retornar o `:error`.

<pre class="language-elixir" data-title="lib/vendor.ex" data-line-numbers><code class="lang-elixir">defmodule Vendor do
  # ...
  def register(_id, _description) do
<strong>    {:error, "Params are not valid"} # temos a tupla com :error, algo deu errado
</strong>  end
end
</code></pre>

Basta rodar e ver que agora temos o tratamento correto.

```shell
mix test test/vendor_test.exs
Compiling 1 file (.ex)
..
Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 0 failures
```

{% hint style="warning" %}
**Tratamento de erro**\
\
Esta não é a melhor forma de tratar erros. Aqui foi simplificado para facilitar a explicação e exemplificar a utilização de tuplas. Mais a frente falaremos sobre tratemento de erros.\
\
[Veja mais  sobre isso aqui](https://elixirschool.com/pt/lessons/intermediate/error\_handling)
{% endhint %}

### Conclusão

Tuplas são uma estrutura de dados simples onde guarda valores de todos os tipos. Vimos formas de utiliza-lo e algumas funções de apoio. Também demos uma olhada em um cenário comum que a tupla é utilizada.
