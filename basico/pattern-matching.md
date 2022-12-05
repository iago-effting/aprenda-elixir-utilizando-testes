# Pattern Matching

### Pattern matching em variáveis

Toda linguagem de programação tem a capacidade de criar variáveis. Você define um nome (talvez um tipo, dependendo da linguagem) e assina um valor a ela.

Elixir é um pouco diferente, mesmo parecendo igual a primeira vista, seu conceito muda.

Vamos a um exemplo simples onde criaremos uma `map` chamado pessoa e dentro dele teremos o campo `name` e `genre`. Popularemos os valores do `map` com `My Name` e `:no_binary`.&#x20;

Vamos ao teste.

<pre class="language-elixir" data-title="test/person_test.exs" data-line-numbers><code class="lang-elixir">defmodule SimpleTest do
  use ExUnit.Case

  test "simple tests about pattern matching" do
<strong>    person = Person.create("My Name", :no_binary)
</strong>
    assert person.name == "My Name"
    assert person.genre == :no_binary
  end
end
</code></pre>

Rodando esse teste, obtemos o relatório de erro:

```shell
$ mix test
warning: Person.create/2 is undefined (module Person is not available or is yet to be defined)
  test/phrases_test.exs:6: SimpleTest."test simple tests about pattern matching"/1

.

  1) test simple tests about pattern matching (SimpleTest)
     test/phrases_test.exs:4
     ** (UndefinedFunctionError) function Person.create/2 is undefined (module Person is not available)
     code: person = Person.create("My Name", :no_binary)defmodule Document do
  def render(_id, _type) do
    "Rendering txt"
  end
end

     stacktrace:
       Person.create("My Name", :no_binary)
       test/phrases_test.exs:6: (test)

.
Finished in 0.04 seconds (0.00s async, 0.04s sync)
3 tests, 1 failure
```

Vamos criar nosso módulo para passar o teste

{% code title="lib/person.ex" lineNumbers="true" %}
```elixir
defmodule Person do
  def create(name, genre) do
    %{
      name: name,
      genre: genre
    }
  end
end
```
{% endcode %}

Essa função apenas retorna um map com os valores que enviamos.

Se rodarmos novamente, os testes terão passados.

```shell
mix test
...
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 0 failures
```

Analisando nosso teste, a definição de `person` parece uma assinatura comum de variável, certo? `chave = valor`. Mas por traz dos panos, elixir utiliza pattern matching para fazer isso. O valor a direita, apenas será adicionado a esquerda, caso de o match.&#x20;

Em nosso exemplo, o requisito para matching é: se possuir algo ao lado direito, ele conseguira ser atribuído a variável person. Ele é bem aberto e pode receber qualquer tipo de dado.

Sei que parece confuso. Mas vamos continuar, logo tudo fará sentido.

Imagine agora que voce precisa pegar apenas `name` e `genre` que estão dentro do map retornado da função e coloca-los em uma variável cada. Para poder fazer as confiramações isoladas.

Algo assim:

<pre class="language-elixir" data-line-numbers><code class="lang-elixir">  use ExUnit.Case

  test "simple tests about pattern matching" do
    # ...

<strong>    assert new_name == "My Name"
</strong><strong>    assert new_genre == :no_binary
</strong>  end
end
</code></pre>

Sem pattern matching, voce precisaria continuar com o valor `person.name` ou atribuir ele a uma nova variável `new_name = person.name`. O que por fim, daria na utilização do mesmo.

Com pattern matching é diferente, você pode obter diretamente o valor que quer, sem precisar de uma etapa intermediária.

<pre class="language-elixir" data-title="test/person_test.exs" data-line-numbers><code class="lang-elixir">defmodule PersonTest do
  use ExUnit.Case

  test "simple tests about pattern matching" do
<strong>    %{
</strong><strong>      name: new_name,
</strong><strong>      genre: new_genre
</strong><strong>    } = Person.create("My Name", :no_binary)
</strong>
<strong>    assert new_name == "My Name"
</strong><strong>    assert new_genre == :no_binary
</strong>  end
end
</code></pre>

O `new_name` e `new_genre` estão na posição onde está onde deveriam estar os valores certo? Ele é um espelho do que tem dentro da função, porem, no lugar dos valores, temos a definição de uma nova variável e é ai que o matching acontece. No nosso caso, estamos so dizendo que aceitamos qualquer valor que siga a estrutura de dentro do map, tendo `name` sendo chamado agora de `new_name` e a mesma coisa acontece com `new_genre`.&#x20;

Sendo assim, podemos utilizar diretamente a variável, porque após o matching, ela existe.

Se rodar o teste a cima, ele funcionará corretamente.

```shell
$ mix test
...
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 0 failures
```

Outros exemplo:

{% code lineNumbers="true" %}
```elixir
iex> 10 = 10
iex> x = 1
iex> %{y: value} = %{y: 100}
iex> %{person: %{name: person_name}} = %{person: %{name": "iago"}}
iex> IO.inspet(x) 
1
iex> IO.inspet(value) 
100
iex> IO.inspet(person_name) #
iago
```
{% endcode %}

{% hint style="info" %}
**IO.inspect**

\
Função do módulo IO para espionar o que tem dentro de algum elemento.\
\
[Mais sobre IO.inspect](https://hexdocs.pm/elixir/IO.html#inspect/2)
{% endhint %}

Quando um matching não é sucedido ele dispara uma exceção.

```shell
iex> {a, b, c} = {:hello, "world"}
** (MatchError) no match of right hand side value: {:hello, "world"}
```

### Pattern matching em funções

Na função funciona do mesmo jeito, porém, utilizado nos parâmetros. Vamos a um exemplo. Precisamos de uma função que exiba um tipo de documento dependendo do tipo pedido. Vamos chamar essa função de `render` e o módulo de `Document`. Nossa função precisará de um ID para identificar o documento e um atom pedindo o tipo apropriado, nesse exemplo `:txt`, `:pdf`. Vamos cuidar do `txt` primeiro:

{% code title="test/document_test.exs" lineNumbers="true" %}
```elixir
defmodule DocumentTest do
  use ExUnit.Case

  describe "show/2" do
    test "render txt" do
      id = 12353
      type = :txt

      result = Document.render(id, type)

      assert result == "Rendering txt"
    end
  end
end
```
{% endcode %}

Rodando esse teste obteremos um relatório comum de erro.

```shell
mix test test/document_test.exs
warning: Document.render/2 is undefined (module Document is not available or is yet to be defined)
  test/document_test.exs:9: DocumentTest."test show/2 render txt"/1



  1) test show/2 render txt (DocumentTest)
     test/document_test.exs:5
     ** (UndefinedFunctionError) function Document.render/2 is undefined (module Document is not available)
     code: result = Document.render(id, type)
     stacktrace:
       Document.render(12353, :txt)
       test/document_test.exs:9: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Nosso módulo ainda não existe. Vamos cria-lo:

{% code title="lib/document.ex" lineNumbers="true" %}
```elixir
defmodule Document do
  def render(_id, _type) do
    "Rendering txt"
  end
end
```
{% endcode %}

Rodando o teste agora, tudo vai estar funcionando.

```shell
mix test test/document_test.exs
Compiling 1 file (.ex)
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Vamos lidar agora com o PDF.

{% code title="test/document_test.exs" lineNumbers="true" %}
```elixir
defmodule DocumentTest do
  use ExUnit.Case

  describe "show/2" do
    # ...
    
    test "render pdf" do
      id = 12353
      type = :pdf

      result = Document.render(id, type)

      assert result == "Rendering pdf"
    end
  end
end
```
{% endcode %}

Se rodarmos novamente, teremos um relatório de erro.

```shell
mix test test/document_test.exs
.

  1) test show/2 render pdf (DocumentTest)
     test/document_test.exs:14
     Assertion with == failed
     code:  assert result == "Rendering pdf"
     left:  "Rendering txt"
     right: "Rendering pdf"
     stacktrace:
       test/document_test.exs:20: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 1 failure
```

Aqui as coisas começam a ficar interessantes. Estamos chamando a mesma função, mas com parâmetros diferente. Uma solução fácil para isso, é por um if dentro da função e retornar o valor que queremos. Algo assim:

{% code lineNumbers="true" %}
```elixir
defmodule Document do
  def render(_id, _type) do
    if type == :txt do
      "Rendering txt"
    else
      "Rendering pdf"
    end
  end
end
```
{% endcode %}

Isso funcionária, mas se tornaria uma bagunça se precisarmos colocar mais tipos de arquivos. Com pattern matching, podemos controlar o fluxo das chamadas de funções, colocando nos parâmetros a formula do padrão para executar uma função.&#x20;

<pre class="language-elixir" data-title="lib/document.ex" data-line-numbers><code class="lang-elixir">defmodule Document do
<strong>  def render(_id, :txt) do
</strong>    "Rendering txt"
  end

<strong>  def render(_id, :pdf) do
</strong>    "Rendering txt"
  end
end

</code></pre>

Na definição da função, no segundo parâmetro, ao invés de colocar uma variável, setamos diretamente o valor. Por regra do Pattern matching, o valor deve ser igual para ele ser realizado com sucesso e ai executar a função. A primeira função `render` so irá ser executada, quando o segundo parâmetro for `:txt` e a segunda função apenas quando for `:pdf`.

Se rodar o código, teremos um relatório positivo.

```shell
mix test test/document_test.exs
Compiling 1 file (.ex)
..
Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 0 failures
```

Caso nenhuma dessas condições seja cumprida, uma exceção é lançada. Para evitar isso, podemos criar uma função para avisar que o tipo não é suportado:

<pre class="language-elixir" data-title="test/document_test.exs" data-line-numbers><code class="lang-elixir">defmodule DocumentTest do
  use ExUnit.Case

  describe "render/2" do
    # ...
    
    test "unsupported type" do
      id = 12353
<strong>      type = :unsupported
</strong>
      result = Document.render(id, type)

      assert result == "This type #{type} is not supported"
    end
  end
end

</code></pre>

executando o teste, temos o seguinte relatório:

```shell
mix test test/document_test.exs


  1) test show/2 unsupported type (DocumentTest)
     test/document_test.exs:23
     ** (FunctionClauseError) no function clause matching in Document.render/2

     The following arguments were given to Document.render/2:
     
         # 1
         12353
     
         # 2
         :unsupported
     
     Attempted function clauses (showing 2 out of 2):
     
         def render(_id, :txt)
         def render(_id, :pdf)
     
     code: result = Document.render(id, type)
     stacktrace:
       (hello_world 0.1.0) lib/document.ex:2: Document.render/2
       test/document_test.exs:27: (test)

..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 1 failure
```

O próprio relatório nos da as opções válidas com `:txt` e `:pdf.` Vamos corrigir isso.

<pre class="language-elixir" data-title="lib/document.ex" data-line-numbers><code class="lang-elixir">defmodule Document do
  def render(_id, :txt) do
    "Rendering txt"
  end

  def render(_id, :pdf) do
    "Rendering pdf"
  end

<strong>  def render(_id, unsupported) do
</strong><strong>    "This type #{unsupported} is not supported"
</strong><strong>  end
</strong>end
</code></pre>

Na ultima função, troquei novamente o segundo parâmetro para variável, assim ele não espera um valor especifico e consegue executar a função. Tendo isso em mãos, criamos uma frase para avisar que o tipo informado não é válido.

Podemos rodar esse código e temos o relatório de sucesso:

```shell
mix test test/document_test.exs
...
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 0 failures
```

{% hint style="info" %}
**Ordem de execução**

A tentativa de execução de funções começa de cima para baixo. Quando uma função é atendida pelo pattern matching ela não vai para a próxima.\


`def render(_id, :txt), do: # ...`

`def render(_id, type), do: # Ela irá parar aqui`

`def render(_id, :pdf), do: # Nunca tentará fazer o pattern matching`
{% endhint %}

### Conclusão

Pattern matching é uma das principais funcionalidades do elixir. Versátil e fácil de usar, é uma excelente ferramenta para se aprender. Coisas complexas podem ser resolvidas de forma simples e elegante.

