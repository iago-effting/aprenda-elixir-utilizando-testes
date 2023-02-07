# if / else

O mais popular no mundo do desenvolvimento é o `if/else`. Definido com `if/2` ele espera uma expressão e um bloco de execução inicado com `do`.

```elixir
if some_expression do
  # se a expressão for verdadeira, rodar contexto de if
end
```

Só será executado o contexto de `if` caso a expressão seja verdadeira. Vamos a um exemplo

Precisamos de uma função que retorna se uma pessoa é maior de idade. Então expressão seria: `idade >= 18`. caso seja verdadeira, retorna `true`, case não, retorna `false`.

Para estruturar nossa solução, iremos utiliza maps (caso não saiba o que é, de uma olhada no [capitulo de maps](../map.md)). Teremos no map o atributo `name` e o atributo `age`.&#x20;

Vamos ao nosso teste:

{% code title="test/person_test.exs" lineNumbers="true" %}
```elixir
defmodule PersonTest do
  use ExUnit.Case

  describe "is_adult/1" do
    test "Person is an adult" do
      person = %{name: "Iago", age: 30}
      result = Person.is_adult?(person)
  
      assert result == true
    end
  end
end
```
{% endcode %}

Rodando o teste, teremos o primeiro relatório de erro

```shell
mix test test/person_test.exs
warning: Person.is_adult?/1 is undefined or private
  test/person_test.exs:6: PersonTest."test Person is an adult"/1



  1) test Person is an adult (PersonTest)
     test/person_test.exs:4
     ** (UndefinedFunctionError) function Person.is_adult?/1 is undefined or private
     code: result = Person.is_adult?(person)
     stacktrace:
       (hello_world 0.1.0) Person.is_adult?(%{age: 30, name: "Iago"})
       test/person_test.exs:6: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Ainda não possuimos o modulo nem a função criada, mas ja temos uma ideia do que precisamos. Vamos lá. Criaremos agora o arquivo `person.ex` e escreveremos a menor logica para o teste passar:

{% code title="lib/person.ex" lineNumbers="true" %}
```elixir
defmodule Person do
  def is_adult(_person) do
    true
  end
end
```
{% endcode %}

Se rodarmos o teste novamente, teremos sucesso.

```elixir
mix test test/person_test.exs
Compiling 1 file (.ex)
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Caso de verdadeiro cobrido, agora precisamos do caso falso, quando não se é maior de idade. Vamos ao teste

{% code title="test/person_test.exs" lineNumbers="true" %}
```elixir
defmodule PersonTest do
  use ExUnit.Case

  describe "is_adult/1" do
    # ...
    test "Person isn't an adult" do
      person = %{name: "Edgar", age: 2}
      result = Person.is_adult?(person)
  
      assert result == false
    end
  end
end
```
{% endcode %}

```shell
mix test test/person_test.exs
.

  1) test is_adult/1 Person isn't an adult (PersonTest)
     test/person_test.exs:12
     Assertion with == failed
     code:  assert result == false
     left:  true
     right: false
     stacktrace:
       test/person_test.exs:16: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 1 failure
```

Obviamente, a resposta da função `is_adult?/1` sempre será true, já que fixamos um valor estatico para ela. Como podemos resoltar esse problema? Condicionais foram criados para esse tipo de tratamento.&#x20;

Todas as expressões `if` que não são verdadeiras, caem no contexto `else`, caso definido. Podemos escrever o códio if de duas formas, normal ou encurtada:

{% code lineNumbers="true" %}
```elixir
# Normal
if true do
  # contexto caso expressão do if for verdadeiro
else
  # contexto caso expressão do if nao for verdadeiro
end

# Encurtado
if(5 > 1, do: true, else: false)
```
{% endcode %}

As expressoes lidam com `operadores de comparação`, algum deles são:

* `==` igual
* `===` identico
* `!=` diferente
* `>` maior
* `<` menor
* `>=` maior igual
* `<=` menor igual

#### Exemplos simples

{% code lineNumbers="true" %}
```elixir
if(5 > 1, do: true, else: false) # true
if(5 < 1, do: true, else: false) # false
if(1 >= 1, do: true, else: false) # true
if(0 <= 1, do: true, else: false) # true

if(5 == 5, do: true, else: false) # true
if(2 === 5, do: true, else: false) # true
if(2.0 === 2.0, do: true, else: false) # false

if(5 != 5, do: true, else: false) # false
```
{% endcode %}

Vamos alterar nosso código e fazer o teste passar.

Precisamos de nossa regra que a idade seja `maior a 17 anos` ou `maior e igual a 18`.&#x20;

{% code title="lib/person.ex" lineNumbers="true" %}
```elixir
defmodule Person do
  def is_adult?(person) do
    if person.age >= 18 do
      true
    else
      false
    end
  end
end
```
{% endcode %}

```shell
mix test test/person_test.exs
Compiling 1 file (.ex)
..
Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 0 failures
```

Podemos também alterar para inline.&#x20;

{% hint style="warning" %}
**Códigos inline**

São codigos que podem ser executado em uma linha só. Server para simplificar a escreita e se tornar mais fácil entendimento. Porém, deve ser tomado cuidado em não deixar as coisas mais complexas com isso, sempre pense nisso antes de seguir essa estrategia.
{% endhint %}

{% code title="lib/person.ex" lineNumbers="true" %}
```elixir
defmodule Person do
  def is_adult?(person) do
    if person.age >= 18, do: true, else: false
  end
end
```
{% endcode %}

```shell
mix test test/person_test.exs
Compiling 1 file (.ex)
..
Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 0 failures
```

If é largamente utilizado em outras linguagens. Mesmo elixir possuindo `if`, não é tão utilizado devido ao [`pattern matching`](../pattern-matching.md). Porém, existem diversos cenários que são importantes.
