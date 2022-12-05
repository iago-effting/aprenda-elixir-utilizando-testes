# Estruturas

Estruturas em `elixir`, são implementados igual `Maps`. Os `Maps` podem ser lidos e gravados com eficiência, são imutáveis e as chaves podem ser qualquer termo, como `atoms`.

A estrutura é um map com uma chave já pré-definida chamada `__struct__`. Vamos criar uma estrutura.

{% code title="test/user_test.exs" lineNumbers="true" %}
```elixir
defmodule StructTest do
  use ExUnit.Case

  test "create a simple struct" do
    new_struct = %User{id: 100, name: "Iago"}

    assert new_struct == %{__struct__: User, id: 100, name: "Iago"}
  end
end
```
{% endcode %}

```shell
mix test test/user_test.exs  

== Compilation error in file test/user_test.exs ==
** (CompileError) test/struct_test.exs:5: User.__struct__/1 is undefined, cannot expand struct User. Make sure the struct name is correct. If the struct name exists and is correct but it still cannot be found, you likely have cyclic module usage in your code
    expanding struct: User.__struct__/1
    test/struct_test.exs:5: AtomTest."test create a simple struct"/1
```

Você pode perceber, que ele já fala no próprio relatório de teste que `User.__struct__/1` não existe. Esse campo `__structure__` que define que um `map` é uma `struct`.

Claro, ainda não temos esse módulo e estrutura criados. Então vamos cria-los dentro do arquivo `user.ex.` Para isso precisamos utilizar uma função em nosso módulo chamada defstruct, ela por natureza utiliza o nome do modulo como nome da estrutura, injetando dentro de `__struct__` o nome completo do módulo, como o nosso so tem um nível, será apenas `User`

<pre class="language-elixir" data-title="lib/user.ex" data-line-numbers><code class="lang-elixir">defmodule User do
<strong>  defstruct [:id, :name]
</strong>end
</code></pre>

Feito isso, basta rodar o teste e tudo funcionará.

```shell
mix test test/user_test.exs
Compiling 1 file (.ex)
.
Finished in 0.00 seconds (0.00s async, 0.00s sync)
1 test, 0 failures
```

Se `Struct` na verdade é um map com uma chave `__struct__`, porque eu deveria usar ela e não `map` diretamente?

Diferente do Map, não podemos inserir valores na utilização da struct. Exemplo:

<pre class="language-elixir" data-title="test/user_test.exs" data-line-numbers><code class="lang-elixir">defmodule StructTest do
  use ExUnit.Case

  # ...

  test "error when create a simple struct" do
    assert_raise CompileError, fn ->
      %User{
        id: 100,
        name: "Iago",
<strong>        field_does_not_exist_in_structure: 255
</strong>      }
    end
  end
end

</code></pre>

Ao rodar esse teste, irá gerar um relatório de erro de compilação acusando que `field_does_not_exist_in_structure` não foi encontrado na estrutura `User.__struct__/1`.

```shell
mix test test/user_test.exs

== Compilation error in file test/struct_test.exs ==
** (KeyError) key :field_does_not_exist_in_structure not found
    (hello_world 0.1.0) expanding struct: User.__struct__/1
    test/struct_test.exs:16: StructTest."test error when create a simple struct"/1
```

Esse comportamento é semelhante ao tentar adicionar algum valor a uma chave que não existe no `map`:

```shell
iex> data = %{a: 1}
iex> %{data | z: 9}

** (KeyError) key :z not found in: %{a: 1}
    (stdlib 4.1.1) :maps.update(:z, 3, %{a: 1})
    (stdlib 4.1.1) erl_eval.erl:309: anonymous fn/2 in :erl_eval.expr/6
    (stdlib 4.1.1) lists.erl:1350: :lists.foldl/3
```

A diferença entre os dois é que no `map` temos um erro em tempo de execução (runtime) e na `struct` temos um erro de compilação (compile-time).

Utilizamos estrutura para definir dados bem definidos, que já temos conhecimento de seus campos, como por exemplo `User`, `Article` e outros. `Maps` são mais dinâmicos. Existem outras diferenças que veremos mais a frente.

## Conclusão

Estruturas são tratados como `Maps` e conseguimos ter um entendimento melhor assim. Porém temos algumas diferenças. Não podemos adicionar a nossa utilização de `estrutura` um campo que não está definido nela, isso causará um erro de compilação.&#x20;
