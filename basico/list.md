# List

Listas são estruturas de dados que server para armazenar dados. Esses dados podem ser simples como tipos primitivos `integer`, `string`, `atom`, `string`. Mas também podem armazenar dados mais complexos como `estruturas`, `mapas e` outras `listas`.

Encontramos facilmente exemplos de onde utiliza-los. Seja em uma listagem de usuários do sistema. Uma lista de argumentos que precisa ser enviado para o banco de dados ou a resposta de alguma função ou configuração de nossa aplicação. Entender seu conceito e como podemos utiliza-la é de grande vantagem para nós. Vamos a um exemplo.

### Exemplo

Foi estipulado que tenhamos uma listagem de candidatos que foram aprovados no vestibular. Eles precisamos que tenhamos poder de aprovar alguém (adicionando o estudando a lista) e também desaprovar alguém (removendo o estudando da lista.) Com esse levantamento temos três pontos a lidar:

1. Ter uma lista de estudando aprovados
2. Conseguir adicionar estudantes nessa lista
3. Remover estudantes da lista.

Vamos ao primeiro teste:

{% code title="test/students_test.exs" lineNumbers="true" %}
```elixir
defmodule StudentsTest do
  use ExUnit.Case

  test "adding a new user on approved list" do
    current_list = ["Paulo", "João"]
    students = ["Iago"]
  
    assert Students.add_to_approved_list(current_list, students) == ["Paulo", "João", "Iago"]
  end
end
```
{% endcode %}

Criamos uma variável chamada `current_list` que vai conter nossa lista inicião de aprovados. Depois criamos uma variável chamada `students`, que é uma lista de usuário. Temo então na linha 8 a chamada `Students.add_to_approved_list/2`. Ele é nossa função que irá adicionar uma lista de usuário a uma lista ja existente. O retorno dessa função será uma nova lista com os estudantes adicionados. Com isso conseguimos criar nossa assertion dizendo que deve ser igual a nova lista.

Vamos rodar esse teste:

```sh
mix test test/students_test.exs
warning: Students.add_to_approved_list/2 is undefined (module Students is not available or is yet to be defined)
  test/students_test.exs:9: StudentsTest."test List of students approved adding a new user on approved list"/1



  1) test List of students approved adding a new user on approved list (StudentsTest)
     test/students_test.exs:5
     ** (UndefinedFunctionError) function Students.add_to_approved_list/2 is undefined (module Students is not available)
     code: assert Students.add_to_approved_list(current_list, students) == ["Paulo", "João", "Iago"]
     stacktrace:
       Students.add_to_approved_list(["Paulo", "João"], ["Iago"])
       test/students_test.exs:9: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Com somente o teste criado, recebemos a mensagem de que a função `Students.add_to_approved_list/2` não existe. Próximo passo é cria-la.

{% code title="lib/students.ex" lineNumbers="true" %}
```elixir
defmodule Students do
  def add_to_approved_list(_, _) do
    []
  end
end

```
{% endcode %}

Criamos o módulo Students e dentro a função `add_to_approved/2`. Como já tinhamos falado, retornaremos uma lista.&#x20;

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)


  1) test List of students approved adding a new user on approved list (StudentsTest)
     test/students_test.exs:5
     Assertion with == failed
     code:  assert Students.add_to_approved_list(current_list, students) == ["Paulo", "João", "Iago"]
     left:  []
     right: ["Paulo", "João", "Iago"]
     stacktrace:
       test/students_test.exs:9: (test)


Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 1 failure
```

No teste falhou novamente. Agora acusando que a nossa chamada esta retornando um `[]` e esperavamos `["Paulo", "João", "Iago"]`. Claro, não retornamos a lista enviada. Vamos fazer isso para seguir com a implementação. Vamos alterar `students.ex`. Ao chamar a função, o primeiro argumento que passamos é a lista atual e o segundo argumento é a lista de estudantes a serem adicionados. Vamos primeiro retornar apenas a primeira lista.

<pre class="language-elixir" data-title="lib/students.ex" data-line-numbers><code class="lang-elixir">defmodule Students do
  def add_to_approved_list(current_list, _) do
<strong>    current_list
</strong>  end
end

</code></pre>

Vamos executar novamente o teste:

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)


  1) test List of students approved adding a new user on approved list (StudentsTest)
     test/students_test.exs:5
     Assertion with == failed
     code:  assert Students.add_to_approved_list(current_list, students) == ["Paulo", "João", "Iago"]
     left:  ["Paulo", "João"]
     right: ["Paulo", "João", "Iago"]
     stacktrace:
       test/students_test.exs:9: (test)


Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 1 failure
```

Estamos chegando perto! Agora acusou apenas que não temos o estudante Iago em nossa lista. Isso porque o primeiro parâmetro de nossa função esta passando apenas uma lista sem `Iago`. Já o segundo parâmetro está passando uma lista e nessa lista possuímos o nome `Iago`. Essa segunda lista são os estudantes que queremos adicionar na lista de estudantes que passaram. Precisamos unir essas duas listas. Para isso, podemos usar o operador `++/2` do elixir.

<pre class="language-elixir" data-title="lib/students.ex" data-line-numbers><code class="lang-elixir">defmodule Students do
<strong>  def add_to_approved_list(current_list, new_students) do
</strong><strong>    current_list ++ new_students
</strong>  end
end

</code></pre>

Alteramos o nome do segundo parâmetro de underline (`_`) para new\_students para podermos usar em nossa função. Tendo os novos estudantes  em mãos, adicionamos o operador `++/2` ao lado de `current_list` e colocamos ao final a lista que queremos fazer a união, no nosso casso `new_students`. Vamos rodar novamente nosso teste:

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Agora temos uma nova lista retornada pela função `add_to_approved_list/2` contendo a união de duas listas. Com isso temos dois dos problemas resolvidos.&#x20;

1. ~~Ter uma lista de estudando aprovados~~
2. ~~Conseguir adicionar estudantes nessa lista~~
3. Remover estudantes da lista.

Precisamos agora remover os estudantes que foram desaprovados. Vamos ao teste:

{% code title="test/students_test.exs" lineNumbers="true" %}
```elixir
defmodule StudentsTest do
  use ExUnit.Case

  test "removing a user on approved list" do
    current_list = ["Paulo", "João"]
    students = ["João"]

    assert Students.remove_from_approved_list(current_list, students) == ["Paulo"]
  end
end
```
{% endcode %}

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)
warning: Students.remove_from_approved_list/2 is undefined or private
  test/students_test.exs:16: StudentsTest."test List of students approved removing an user on approved list"/1



  1) test List of students approved removing an user on approved list (StudentsTest)
     test/students_test.exs:12
     ** (UndefinedFunctionError) function Students.remove_from_approved_list/2 is undefined or private
     code: assert Students.remove_from_approved_list(current_list, students) == ["Paulo"]
     stacktrace:
       (hello_world 0.1.0) Students.remove_from_approved_list(["Paulo", "João"], ["João"])
       test/students_test.exs:16: (test)

.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 1 failure
```

Agora vamos fazer o oposto do que fizemos anteriormente. Queremos remove elementos de uma lista. Choque com a informação, mas o oposot de `++/2` é `--/2`. Acredito que nem precisaria fazer essa implementação. Mas vou fazer para deixar aqui completo. Vamos a implementação:

{% code title="lib/students.ex" lineNumbers="true" %}
```elixir
defmodule Students do
  # ...

  def remove_from_approved_list(current_list, students_to_remove) do
    current_list -- students_to_remove
  end
end

```
{% endcode %}

Fizemos a mesma implementação, porem, utilizamos o operador `--/2`. Pode rodar os testes e ser feliz.

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)
..
Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 0 failures
```

E nosso escopo esta fechado:

1. ~~Ter uma lista de estudando aprovados~~
2. ~~Conseguir adicionar estudantes nessa lista~~
3. ~~Remover estudantes da lista.~~

Vamos adicionar mais uma tarefa. Queremos saber a quantidade de estudantes que passaram no vestibular. Vamos ao teste:

{% code title="test/students_test.exs" lineNumbers="true" %}
```elixir
defmodule StudentsTest do
  use ExUnit.Case

  describe "List of students approved" do
    test "total approved" do
      current_list = ["Paulo", "João"]

      assert Students.total_approved(current_list) == 2
    end
  end
end

```
{% endcode %}

```sh
mix test test/students_test.exs
warning: Students.total_approved/1 is undefined or private. Did you mean:

      * add_to_approved_list/2

  test/students_test.exs:22: StudentsTest."test List of students approved total approved"/1

.

  1) test List of students approved total approved (StudentsTest)
     test/students_test.exs:19
     ** (UndefinedFunctionError) function Students.total_approved/1 is undefined or private. Did you mean:
     
           * add_to_approved_list/2
     
     code: assert Students.total_approved(current_list) == 2
     stacktrace:
       (hello_world 0.1.0) Students.total_approved(["Paulo", "João"])
       test/students_test.exs:22: (test)

.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
3 tests, 1 failure
```

Não possuímos a função. Logo, temos um erro. Vamos resolver isso criando ela:

{% code title="lib/students.ex" lineNumbers="true" %}
```elixir
defmodule Students do
  # ...
  def total_approved(_current_list) do
    2
  end
end

```
{% endcode %}

Quando lidamos com totais, sempre esperamos um `integer`, para manter consistente retornamos dessa função apenas 2 para nosso teste passar. Vamos ver o que o teste nos diz.

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)
...
Finished in 0.01 seconds (0.00s async, 0.01s sync)
3 tests, 0 failures
```

Vamos adicionar uma outra assertion para nosso teste, com uma quantidade diferente de itens na lista. Isso garante que ela funciona bem.

{% code title="test/students_test.exs" lineNumbers="true" %}
```elixir
defmodule StudentsTest do
  use ExUnit.Case

  describe "List of students approved" do
    test "total approved" do
      current_list = ["Paulo", "João"]
      another_current_list = ["Carlos", "Cris", "Jill"]

      assert Students.total_approved(current_list) == 2
      assert Students.total_approved(another_current_list) == 3
    end
  end
end

```
{% endcode %}

Vamos rodar o teste com a nova assertion:

```sh
mix test test/students_test.exs
..

  1) test List of students approved total approved (StudentsTest)
     test/students_test.exs:19
     Assertion with == failed
     code:  assert Students.total_approved(another_current_list) == 3
     left:  2
     right: 3
     stacktrace:
       test/students_test.exs:24: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 1 failure
```

Algo deu errado. A quantidade esperada na segunda assertion é diferente de dois, que colocamos diretamente na implementação. Precisamos de algo mais dinâmico. Por sorte (uma brincadeira), temos a função nativa `length/1`, onde espera uma lista e nos retorna o total de elementos dessa lista. Vamos lá:

{% code title="" lineNumbers="true" %}
```elixir
defmodule Students do
  # ...
  def total_approved(current_list) do
    length(current_list)
  end
end
```
{% endcode %}

Simples não? Vamos rodar novamente os testes:

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)
...
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 0 failures
```

Muito bem! Você está indo bem. Mas vamos pensar em alguma outra coisa. Você é um desses estudantes e quer saber se você passou. Como podemos fazer isso? Vamos ao teste:

{% code title="test/students_test.exs" lineNumbers="true" %}
```elixir
defmodule StudentsTest do
  use ExUnit.Case

  describe "List of students approved" do
    test "was student approved?" do
      current_list = ["Iago", "Paulo","João"]

      assert Students.was_approved?(current_list, "Iago") == true
    end
  end
end

```
{% endcode %}

```sh
mix test test/students_test.exs
warning: Students.was_approved?/2 is undefined or private. Did you mean:

      * total_approved/1

  test/students_test.exs:30: StudentsTest."test List of students approved was student approved?"/1

...

  1) test List of students approved was student approved? (StudentsTest)
     test/students_test.exs:27
     ** (UndefinedFunctionError) function Students.was_approved?/2 is undefined or private. Did you mean:
     
           * total_approved/1
     
     code: assert Students.was_approved?(current_list, "Iago") == true
     stacktrace:
       (hello_world 0.1.0) Students.was_approved?(["Iago", "Paulo", "João"], "Iago")
       test/students_test.exs:30: (test)


Finished in 0.03 seconds (0.00s async, 0.03s sync)
4 tests, 1 failure
```

Criamos um teste que chama uma função `was_approved?/2`, onde o primeiro argumento é a lista de aprovados e o segundo argumento é uma `string` com o nome do estudante que queremos saber se foi aprovado.&#x20;

{% hint style="info" %}
O nome da função possui uma interrogação, dando a entender que queremos uma resposta de verdadeiro ou falso, isso é uma convenção que também adotamos no Elixir, você pode ver mais sobre isso no [capítulo de conveções](../conceitos/convencoes.md#funcao-com-interrogacao).
{% endhint %}

Vamos resolver esse teste:

{% code title="" lineNumbers="true" %}
```elixir
defmodule Students do
  # ...
  def was_approved?(_approved_list, _student) do
    true
  end
end

```
{% endcode %}

Criamos a função no módulo `Students` e retornamos uma valor estático `true`. Assim nosso teste passará.

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)
....
Finished in 0.02 seconds (0.00s async, 0.02s sync)
4 tests, 0 failures
```

Vamos adicionar agora um caso de que o estudante que procuramos não passou.

<pre class="language-elixir" data-title="test/students_test.exs" data-line-numbers><code class="lang-elixir">defmodule StudentsTest do
  use ExUnit.Case

  describe "List of students approved" do
    test "was student approved?" do
      current_list = ["Iago", "Paulo","João"]

      assert Students.was_approved?(current_list, "Iago") == true
<strong>      assert Students.was_approved?(current_list, "Rui") == false
</strong>    end
  end
end

</code></pre>

Para isso, apenas adicionamos a linha 9 a nosso teste, procuranod por um estudante chamado Rui que não existe em nossa lista. Vamos rodar o teste:

```sh
mix test test/students_test.exs
..

  1) test List of students approved was student approved? (StudentsTest)
     test/students_test.exs:27
     Assertion with == failed
     code:  assert Students.was_approved?(current_list, "Rui") == false
     left:  true
     right: false
     stacktrace:
       test/students_test.exs:31: (test)

.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
4 tests, 1 failure
```

A resposta para se Rui passou nos exames é true, mas na verdade esperavamos por false. Isso devido ao retorno de nossa função ser estático. Precisamos de algo mais dinâmico para nos salvar agora.

Temos um operador em elixir chamado `in/2`. Ele verificar se existe um elemento em nossa lista. A leitura desse operador fica muito interessante: `1 in [1,2,3]`. Vamos aplica-lo em nosso código:

<pre class="language-elixir" data-title="" data-line-numbers><code class="lang-elixir">defmodule Students do
  # ...
  def was_approved?(approved_list, student) do
<strong>    student in approved_list
</strong>  end
end

</code></pre>

Adicionamos o operador in na linha 4. Também removemos o underline (`_`) dos dois parâmetros da função, para podermos utiliza-las. vamos rodar o teste:

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)
....
Finished in 0.02 seconds (0.00s async, 0.02s sync)
4 tests, 0 failures
```

Perfeito! As funções de lista para estudantes estão tomando forma e ficando claras em seus objetivos. Precisamos so de mais duas funcionalidades. Me ligaram ainda pouco pedindo que adicionacemos a possibilidade de saber o primeiro e ultimo estudante de nossa lista. Falei que não seria um problema, que você já estava dominando a listagem. Vamos escopar isso

Desafio final

1. Obter primeiro estudante da lista
2. Obter último estudante da lista

Vamos começar do primeiro. Criaremos seu teste:

{% code title="test/students_test.exs" lineNumbers="true" %}
```elixir
defmodule StudentsTest do
  use ExUnit.Case

  describe "List of students approved" do
    # ...
    test "first the list" do
      current_list = ["Iago", "Paulo", "João"]

      assert Students.first(current_list) == "Iago"
    end
  end
end

```
{% endcode %}

```sh
mix test test/students_test.exs
warning: Students.first/1 is undefined or private
  test/students_test.exs:37: StudentsTest."test List of students approved first the list"/1

.

  1) test List of students approved first the list (StudentsTest)
     test/students_test.exs:34
     ** (UndefinedFunctionError) function Students.first/1 is undefined or private
     code: assert Students.first(current_list) == "Iago"
     stacktrace:
       (hello_world 0.1.0) Students.first(["Iago", "Paulo", "João"])
       test/students_test.exs:37: (test)

...
Finished in 0.03 seconds (0.00s async, 0.03s sync)
5 tests, 1 failure
```

Vamos implementar a função `first/1`, onde recebe a lista que queremos obter o primeiro. Elixir tem um módulo de List que nos ajuda a listar com algumas coisas relacionadas a lista. Para resolver nosso problema aqui, temos a função `List.first/1`, onde o argumento passado é a lista e a resposta é o elemento da lista, exatamente o que precisamos.

{% code title="lib/students.ex" lineNumbers="true" %}
```elixir
defmodule Students do
  # ...
  def first(current_list) do
    List.first(current_list)
  end
end

```
{% endcode %}

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)
.....
Finished in 0.02 seconds (0.00s async, 0.02s sync)
5 tests, 0 failures
```

Simples não? Agora precisamos lidar com o útilmo da lista. Te desafio a pensar qual função usaremos para resolver isso. Enquanto pensa, vamos para o teste:

{% code title="test/students_test.exs" lineNumbers="true" %}
```elixir
defmodule StudentsTest do
  use ExUnit.Case

  describe "List of students approved" do
    # ...
    test "last in the list" do
      current_list = ["Iago", "Paulo", "João"]

      assert Students.last(current_list) == "João"
    end
  end
end

```
{% endcode %}

```sh
mix test test/students_test.exs
warning: Students.last/1 is undefined or private
  test/students_test.exs:43: StudentsTest."test List of students approved last in the list"/1

...

  1) test List of students approved last in the list (StudentsTest)
     test/students_test.exs:40
     ** (UndefinedFunctionError) function Students.last/1 is undefined or private
     code: assert Students.last(current_list) == "João"
     stacktrace:
       (hello_world 0.1.0) Students.last(["Iago", "Paulo", "João"])
       test/students_test.exs:43: (test)

..
Finished in 0.03 seconds (0.00s async, 0.03s sync)
6 tests, 1 failure
```

Vamos implementar a função que não existe. Pensou na função que usariamos para ser oposto a `List.first/1`? É isso mesmo, `List.last/1`. Vamos implementar nosso código.

{% code title="lib/students.ex" lineNumbers="true" %}
```elixir
defmodule Students do
  # ...
  def last(current_list) do
    List.last(current_list)
  end
end

```
{% endcode %}

```sh
mix test test/students_test.exs
Compiling 1 file (.ex)
......
Finished in 0.02 seconds (0.00s async, 0.02s sync)
6 tests, 0 failures
```

### Conclusão

Ótimo! Agora temos muitas ferramentas para lidar com listas. Listas são simples, mas podemos tirar grandes vantagens dela ao dia-a-dia de trabalho. Ela sempre estará presente e muitas vezes podem se tornar bem complexa. O domínio de operações e funções de auxílio ira te ajudar na jornada.

Você deve ter ficado com a impressão de faltar algo. Sim, temos um buraco aqui. Que tipo de aprendizado de lista não ensina como a percorremos. Por isso o próximo capítulo falará sobre estruturas de repetição.&#x20;





