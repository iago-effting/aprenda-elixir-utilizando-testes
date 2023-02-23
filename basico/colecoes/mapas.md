# Mapas

`Map` são estruturas de dados no modelo chave valor (dicionário) que aceitam todos os tipos de dados. A definição dela segue o modelo `%{}` e os valores vão dentro das chaves.

Vamos criar uma estrutura simples:

{% code title="test/maps_test.exs" lineNumbers="true" %}
```elixir
defmodule MapsTest do
  use ExUnit.Case

  test "maps" do
    data = %{
      name: "iago"
    }

    # utilizando pattern matching
    %{name: my_name_1} = data
    assert my_name == "iago"
    
    # Utilizando fech
    {:ok, my_name_2 } = Map.fetch(data, :name)
    assert my_name_2 == "iago"
    
    # Utilizando acesso por indice
    my_name_3 = data[:name]
    assert my_name_3 == "iago"
    
    # utilizando encadeamento
    my_name4 = data.name
    assert my_name4 == "iago"
    
    # Quando acessado por indice, o que acontece quando o elemento não existe
    my_name_5 = data[:not_exists_index]
    assert my_name_5 == nil
    
    # Quando acessado por encademamento, o que acontece quando o elemento não existe
    assert_raise KeyError, fn ->
      data = %{name: "iago"}
      data.nonexistent_element
    end
  end
end
```
{% endcode %}

Rodando esse código o test passará, mas dará um aviso que algo deu errado, no nosso caso, é a `linha 26`, que dispara um erro dizendo que não existe o elemento `nonexistent_element`.

A função [`assert_raise/2`](https://hexdocs.pm/ex\_unit/ExUnit.Assertions.html#assert\_raise/2) lida com expectativas de exceção do código, podendo fazer parte do teste para garantirmos o comportamento correto.

```shell
mix test test/maps_test.exs
warning: undefined field "not_exists_index" in expression:

    # test/maps_test.exs:26
    data.not_exists_index

expected one of the following fields: name

where "data" was given the type map() in:

    # test/maps_test.exs:25
    data = %{name: "iago"}

Conflict found at
  test/maps_test.exs:26: MapsTest."test maps"/1

.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
1 test, 0 failures
```

Existem diversas formas de lidar com Map, bastante entender como fica melhor para você.

### Alterar valores

Tendo uma vez o Map criado, podemos também alterar os valores.&#x20;

{% code title="" lineNumbers="true" %}
```elixir
defmodule MapsTest do
  use ExUnit.Case
  
  # ...
  
  test "adding value in map" do
    data = %{
      name: "Iago"
    }

    new_data = %{data | name: "Elixir"}

    assert data.name == "Iago"
    assert new_data.name == "Elixir"
  end
end
```
{% endcode %}

```shell
mix test
..
Finished in 0.05 seconds (0.00s async, 0.05s sync)
2 tests, 0 failures
```

{% hint style="info" %}
**Imutabilidade**\
\
Elixir é uma linguagem que utiliza o [conceito de  imutabilidade](../../conceitos/imutabilidade.md). Isso quer dizer que não podemos alterar valores ja definidos. Sempre que precisarmos fazer isso, devemos responder um novo elemento e re-assinar na estrutura de dados que estamos. Exemplo\


`data = %{ name: "Iago" }`

`new_data = %{data | name: "Elixir"}`

``\
`a variáveldata` nunca será alterará, se quiser usar o valor novo, precisamos substituir o antigo pelo novo, onde perderemos tudo do valor inicial.&#x20;
{% endhint %}

### Conclusão

Existem diversas outras funções de apoio ao Map, voce poderá ver elas acessando [esse link](https://hexdocs.pm/elixir/1.12/Map.html#summary). Vimos aqui formas de criar, alterar e acessar dados de um Map. Também falamos um pouco sobre imutabilidade, conceito importante em linguagens funcionais.
