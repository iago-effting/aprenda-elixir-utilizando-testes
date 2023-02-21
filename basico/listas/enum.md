# Enum

Algumas funções nativas no Elixir utilizam recursão estão disponíveis para utilizarmos com uma API  limpa. O módulo [`Enum`](https://hexdocs.pm/elixir/1.12/Enum.html) é um deles. Para entender melhor o que é um Enum, leia o [conceito de Enumeráveis](../../conceitos/enumeraveis.md).

Vamos a um exemplo real com base em algumas funções que Enum tem.

### Enum.each/2

Supomos que precisamos percorrer todos os itens de uma lista mandando um email para cada email da lista.&#x20;

Utilizaremos para esse looping onde só precisamos percorrer a lista a função `Enum.each/2` (caso não esteja familiado com o conceito de aridade, recomendo o capítulo sobre [Aridade](../../conceitos/aridade.md))

Para rodar o `Enum.each/2` precisamos um primeiro argumento onde será nossa lista e o segundo argumento uma função anonima com apenas um argumento (que será cada item da nossa lista, passada no primeiro argumento).

Vamos criar um teste para isso

{% code title="test/send_email_test.exs" lineNumbers="true" %}
```elixir
defmodule EnumTest do
  use ExUnit.Case

  describe "Enum.each/2" do
    test "simple tests about Enum.each" do
      my_list = ["test@example.com", "another_test@example.com"]

      Enum.each(my_list, fn email ->
        assert "Email sent to #{email}" == Email.send(email)
      end)
    end
  end
end

```
{% endcode %}

```sh
mix test test/enum_test.exs    
warning: Sender.email/1 is undefined (module Sender is not available or is yet to be defined)
  test/sender_test.exs:8: EnumTest."test simple tests about Enum.each"/1



  1) test simple tests about Enum.each (SenderTest)
     test/enum_test.exs:4
     ** (UndefinedFunctionError) function Sender.email/1 is undefined (module Sender is not available)
     code: Enum.each(my_list, fn email ->
     stacktrace:
       Sender.email("test@example.com")
       test/enum_test.exs:8: anonymous fn/1 in SenderTest."test simple tests about Enum.each"/1
       (elixir 1.14.3) lib/enum.ex:975: Enum."-each/2-lists^foreach/1-0-"/2
       test/enum_test.exs:7: (test)


Finished in 0.08 seconds (0.00s async, 0.08s sync)
1 test, 1 failure
```

Não possuímos o módulo Email nem a função send. Vamos cria-los.

```elixir
defmodule Sender do
  def email(email), do: "Email sent to #{email}"
end

```

Fizemos uma função inline, somente para exemplificar o looping. Basta rodar os testes e tudo estará bem.

```sh
mix test test/enum_test.exs
Compiling 1 file (.ex)
Generated hello_world app
.
Finished in 0.04 seconds (0.00s async, 0.04s sync)
1 test, 0 failures
```

### Enum.map/2

Agora supomos que precisamos alterar a lista para, ao invez de enviar o email, gere links para clicarmos e sermos redirecionados a um cliente de email. Uma vez que precisamos alterar os dados, utilizaremos Enum.map/2 que tem como ideia mapear novamente os dados dentro dele.

Vamos ao teste

{% code title="" lineNumbers="true" %}
```elixir
defmodule EnumTest do
  use ExUnit.Case

  describe "Enum.map/2" do
    test "simple tests about Enum.each" do
      my_list = ["test@example.com", "another_test@example.com"]
      expected_list = ["mailto:test@example.com", "mailto:another_test@example.com"]

      new_list = Enum.map(my_list, fn email ->
        "mailto:#{email}"
      end)

      assert new_list == expected_list
    end
  end
end

```
{% endcode %}

Perceba que criamos uma nova variável e assinamos o retorno da função map/2 lá. Como Elixir é uma linguagem funcional, nada se muta, precisamos sempre re-assinar um valor para que a variável tenha um outro valor. (Veja mais sobre imutabilidade no capítulo de [Imutabilidade](../../conceitos/imutabilidade.md))

A função map serve para alterar o valor que vem de uma lista, sem precisarmos abrir um por um, ja possuindo uma estrutura de repetição onde você passa a lista e o que quer que faça com cada elemento da lista, retornando para seu próprio valor.

Depois disso, apenas comparamos a um valor experado que definimos. Basta rodar o testes e verá a mágica acontecer.

```sh
mix test test/enum_test.exs
..
Finished in 0.04 seconds (0.00s async, 0.04s sync)
2 tests, 0 failures
```
