# With

Eu realmente acho interessante a declaração `with`. Ele deixa as coisas mais claras e podemos também controlar o fluxo do código. Ela é baseada na utilização de funções encadeadas que podemos controlar em caso de sucesso e em caso de algo der errado.&#x20;

Diferente dos [pipes](../em-breve/pipes.md), com `with` podemos ter um maior controle na mesma camada de onde ele está sendo implementado. Podemos definir o que esperamos de cada função por meio de pattern matching e como iremos usar a resposta nas funções subsequentes. Também podemos definir qual o fluxo ele vai tomar em caso de um erro especifico ou genérico.

A estrutura é simples:

* Declaração `with`

```elixir
with
```

* Execução de funções separadas por `,` (virgula). Prestando atenção que temos os valores retornados e isso é de extrema importância. Aqui podemos criar um controle de como será executado, a ordem de execução e critérios de aceite.

```elixir
with valor_1 <- funcao_1(),
     valor_2 <- funcao_2()
```

* Declaração `do`, onde abrimos escopo para quando tudo der certo, ele fará. Muito usado para definir a resposta de uma função onde o `with` esta sendo composto.

```elixir
with valor_1 <- funcao_1(),
     valor_2 <- funcao_2() do
  # caso tudo funciona
end
```

* Declaração `else`, para quando algo sair errado. Aqui podemos usar a regra do `pattern matching` para conseguir a resposta do erro.

```elixir
with valor_1 <- funcao_1(),
     valor_2 <- funcao_2() do
  # caso tudo funciona
else
  # caso algo de errado
  error -> IO.inspect(error)
end
```

A `funcao_1` e `funcao_2` são funções que estao compondo uma funcionalidade. Podemos utilizar várias funções para compor uma funcionalidade, mas lembrando que nem sempre ter muitas facilita o entendimento.

{% hint style="warning" %}
**Composição**\
Quando falamos de composição aqui, estamos falando sobre juntar várias funções com o intuito de fazer uma funcionalidade completa. Por exemplo, salvar usuário ou renderizar arquivos, onde precisamos passar por validações e outras operações até chegar ao resultado estimado.&#x20;
{% endhint %}

### Exemplo

Nada como um exemplo real para entender coisas complexas. Vamos imaginar que precisamos criar um usuário. Para essa funcionalidade precisamos:

* Validar se os dados estão corretos;
* Salvar no banco de dados
* Atualizar usuário para ativo
* Responder que tudo deu certo

São vários passos. Você pode perceber que temos bem definido uma sequencia de operações até finalizar a funcionalidade. Vamos começar escrevendo o nosso teste.

{% code title="test/users_test.exs" lineNumbers="true" %}
```elixir
defmodule WithTest do
  use ExUnit.Case

  describe "create/1" do
    test "create an user and active him when params are valid" do
       params = %{name: "iago"}

       {:ok, result} = Users.create(params)

       assert result.name == params.name
    end
  end
end
```
{% endcode %}

```shell
mix test test/users_test.exs
warning: Users.create/1 is undefined (module Users is not available or is yet to be defined)
  test/users_test.exs:8: WithTest."test create/1 create an user and active him when params are valid"/1



  1) test create/1 create an user and active him when params are valid (WithTest)
     test/users_test.exs:5
     ** (UndefinedFunctionError) function Users.create/1 is undefined (module Users is not available)
     code: result = Users.create(params)
     stacktrace:
       Users.create(%{name: "iago"})
       test/users_test.exs:8: (test)


Finished in 0.03 seconds (0.00s async, 0.03s sync)
1 test, 1 failure
```

Precisamos criar o modulo e a função. Começamos simples:

{% code title="lib/users.ex" lineNumbers="true" %}
```elixir
defmodule Users do
  def create(params) do
    data = %{
      name: params.name,
    }
    
    {:ok, data}
  end
end
```
{% endcode %}

```shell
mix test test/users_test.exs
Compiling 1 file (.ex)
Generated hello_world app
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Primeira iteração passando, vamos lidar a primeira etapa, com a validação. Ela será simples, precisamos ter somente o elemento name dentro de nosso map. Criaremos um teste para isso

{% code title="test/users_test.exs" lineNumbers="true" %}
```elixir
defmodule WithTest do
  use ExUnit.Case

  describe "create/1" do
    # ...

    test "show an error when params are invalid" do
      invalid_params = %{name: "iago"}

      {:error, reason} = Users.create(invalid_params)

      assertreason == "The field name is required"
    end
  end
end
```
{% endcode %}

```shell
mix test test/users_test.exs


  1) test create/1 show an error when params are invalid (WithTest)
     test/users_test.exs:14
     ** (MatchError) no match of right hand side value: {:ok, %{is_actived: true, name: "iago"}}
     code: {:error, reason} = Users.create(invalid_params)
     stacktrace:
       test/users_test.exs:17: (test)

.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 1 failure
```

Ao rodar o teste, vemos que nosso pattern matching não funcionou. Nosso teste esta esperando um `{:error, reason}` e recebemos um `{:ok, data}`. Claramente por que estamos passando um dado estático. Vamos arrumar isso.

Criamos uma função dentro de `users.ex` para validar se os dados estão corretos.&#x20;

{% hint style="info" %}
Temos apenas uma função, ainda não temos a necessidade de utilizar a declaração `with`, não ganhamos nada com isso.
{% endhint %}

Vamos atualizar o arquivo `lib/users.ex`

Aqui precisamos de dois tipos de resposta para nosso `create/1`. Quando temos sucesso e voltamos um `{:ok, data}` e quando de erro um `{:error, reason`} para assim podemos fazer o teste passar.

Temos algumas declarações condicionais boas para isso. Iremos utilizar `case` para facilitar o entendimento.

{% code title="lib/users.ex" lineNumbers="true" %}
```elixir
defmodule Users do
  def create(params) do
    case validate(params) do
      {:ok, validated_user} ->
        {:ok,
         %{
           name: validated_user.name,
         }}

      error ->
        error
    end
  end

  defp validate(user_params) when not is_nil(user_params.name), do: {:ok, user_params}
  defp validate(_user_params), do: {:error, "The field name is required"}
end
```
{% endcode %}

Se rodar isso, teremos um bom resultado.

```shell
mix test test/users_test.exs
Compiling 1 file (.ex)
..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 0 failures
```

Aqui precisamos de uma pausa para uma reflexão. Se você analisar o código, está  bagunçado. Temos indentações demais, que dificultam a leitura. Vamos refatorar.

{% hint style="info" %}
**Refatoração**

Processo de modificar um sistema de software para melhorar a estrutura interna do código sem alterar seu comportamento externo.

[Mais sobre](https://pt.wikipedia.org/wiki/Refatora%C3%A7%C3%A3o)
{% endhint %}

Uma boa regra da refatoração é nunca mudar o comportamento do que se refatora. Isso faz com que nossos testes criados até então, sirvam de checkpoint para saber se tudo esta funcionando como deveria após a alteração.

**Vamos fazer o seguinte:**

Onde adicionamos o dado `name` vamos por para dentro de uma função chamada `build/1` onde receberá o usuário que queremos construir. Ele Irá montar o campo e retornar o novo valor do `map.`

Vamos lá

{% code title="lib/users.ex" lineNumbers="true" %}
```elixir
defmodule Users do
  def create(params) do
    case validate(params) do
      {:ok, validated_user} ->
        {:ok, build(validated_user)}

      error ->
        error
    end
  end

  defp build(user) do
     %{
       name: user.name,
     }
  end

  defp validate(user_params) when not is_nil(user_params.name), do: {:ok, user_params}
  defp validate(_user_params), do: {:error, "The field name is required"}
end

```
{% endcode %}

```shell
mix test test/users_test.exs
Compiling 1 file (.ex)
..
Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 0 failures
```

Ficou melhor de ler. Ainda temos alguns problemas ali, mas vamos seguir e esperar doer mais um pouco para entender o que esta acontecendo.

Em nossa lista o próximo seria salvar o usuário no banco.

* ~~Validar se os dados estão corretos;~~
* Salvar no banco de dados
* Atualizar usuário para ativo
* Responder que tudo deu certo

Não utilizaremos banco de dados aqui, mas iremos criar uma função para simular. Para isso vamos atualizar nosso primeiro teste, aquele que esperamos sucesso e agora adicionaremos uma nova afirmação, onde um novo campo chamada `is_inserted` irá ser adicionado ao nosso `map`, apenas para exemplificar que foi inserido em nosso banco de dados que não existe. Vamos ao teste:

<pre class="language-elixir" data-title="test/users_test.exs" data-line-numbers><code class="lang-elixir">defmodule WithTest do
  use ExUnit.Case

  describe "create/1" do
    test "create an user and active him when params are valid" do
       params = %{name: "iago"}

       {:ok, result} = Users.create(params)

       assert result.name == params.name
<strong>       assert result.is_inserted == true
</strong>    end
  end
end
</code></pre>

Se rodarmos esse teste, teremos um erro

```shell
mix test test/users_test.exs


  1) test create/1 create an user and active him when params are valid (WithTest)
     test/users_test.exs:5
     ** (KeyError) key :is_inserted not found in: %{is_actived: true, name: "iago"}
     code: assert result.is_inserted == true
     stacktrace:
       test/users_test.exs:12: (test)

.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 1 failure
```

Precisamos criar um novo comportamento onde simulará salvar no banco de dados adicionando ao map a chave `inserted_at: true`.&#x20;

Porém, temos um problema. Onde iremos colocar esse código? Teremos que tratar caso algum erro aconteça. Poderíamos utilizar o `case` e encadear mais `case`. Ficando algo assim:

```elixir
defmodule Users do
  def create(params) do
    case validate(params) do
      {:ok, validated_user} ->
        case save(validated_user) do
          {:ok, user_saved} -> {:ok, user_saved}
          {:error, reason} -> {:error, reason}
        end

      error ->
        error
    end
  end

  defp validate(user_params) when not is_nil(user_params.name), do: {:ok, user_params}
  defp validate(_user_params), do: {:error, "The field name is required"}
end

```

Isso até pode funcionar, mas temos grandes perdas como ilegibilidade e este arquivo se tornará gigante até finalizarmos todas as etapas que queremos, logo, a manutenção será penosa. Podemos também usar pipes, estudamos sobre eles, porém, temos os problemas com os errors. Teríamos que tratar dentro de cada função o erro anterior e isso traria muitas indireções.

Para a nossa felicidade, temos uma declaração chamada with, que trabalha como um pipe, porem, cuidando de algumas coisas que ele não consegue fazer. Vamos usar ele e aprender como ele funciona.

Primeiro, um refactoring do que temos até agora.

{% code title="lib/users.ex" lineNumbers="true" %}
```elixir
defmodule Users do
  def create(params) do
    with {:ok, validated_user} <- validate(params) do
      {:ok, validated_user}
    else
      error ->
        error
    end
  end

  defp validate(user_params) when not is_nil(user_params.name), do: {:ok, user_params}
  defp validate(_user_params), do: {:error, "The field name is required"}
end
```
{% endcode %}

A resposta que esperamos da primeira função é `{:ok, validated_user}` caso não seja atendida a execução cairá no else e lá temos um tratamento simples de so passar o erro para frente.

Caso o erro de validação ocorra, ele cairá no else e no error tera o valor de `{:error, "The field name is required"}`. O legal da utilização do with é que qualquer coisa que não seja esperada como resultado nas função de composição, sera direcionadas para o else, onde podemos fazer tratamento ou so retornar seu erro de forma genérica. Logo isso ficará mais claro.

Continuando. Precisamos agora salvar em nosso banco de dados falso e retornar o dado com a chave `is_saved: true`. As coisas se tornam mais simples daqui para frente. Precisamos criar uma função para salvar, chamarei de save/1 onde recebe o usuário que precisamos salvar e adicionaremos na composição de funções do `with`.

<pre class="language-elixir" data-title="lib/user.ex" data-line-numbers><code class="lang-elixir">defmodule Users do
  def create(params) do
    with {:ok, validated_user} &#x3C;- validate(params),
<strong>         {:ok, saved_user} &#x3C;- save(validated_user) do
</strong><strong>      {:ok, saved_user}
</strong>    else
      error ->
        error
    end
  end

  # ...

<strong>  defp save(user) do
</strong><strong>    data = %{
</strong><strong>      name: user.name,
</strong><strong>      is_saved: true
</strong><strong>    }
</strong><strong>
</strong><strong>    {:ok, data}
</strong><strong>  end
</strong>end
</code></pre>

Na função `save/1` retorno apenas como sucesso, mas se você realmente quiser por isso em um banco, e queira retornar um `{:error, reason}` também irá funcionar, o with cuidará do retorno do error.

Rodando isso, temos sucesso.

```shell
mix test test/users_test.exs
..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 0 failures
```

Mais uma etapa concluída, vamos agora ativar o usuário:

* ~~Validar se os dados estão corretos;~~
* ~~Salvar no banco de dados~~
* Atualizar usuário para ativo
* Responder que tudo deu certo

Começamos pelo teste, adicionado a afirmação em nosso teste de sucesso que esperamos agora um campo `is_activated: true`.

<pre class="language-elixir" data-title="test/users_test.exs" data-line-numbers><code class="lang-elixir">defmodule WithTest do
  use ExUnit.Case

  describe "create/1" do
    test "create an user and active him when params are valid" do
       params = %{name: "iago"}

       {:ok, result} = Users.create(params)

       assert result.name == params.name
       assert result.is_saved == true
<strong>       assert result.is_activated == true
</strong>    end

    # ...
  end
end
</code></pre>

```shell
mix test test/users_test.exs


  1) test create/1 create an user and active him when params are valid (WithTest)
     test/users_test.exs:5
     ** (KeyError) key :is_activated not found in: %{is_saved: true, name: "iago"}. Did you mean:
     
           * :is_saved
     
     code: assert result.is_activated == true
     stacktrace:
       test/users_test.exs:12: (test)

.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 1 failure
```

Vamos implementar esse comportamento. Precisamos de uma função semelhante ao save. Iremos criar a função `activate/1` passando o usuário que queremos ativar e então adicionaremos o campo e retornaremos o novo valor.

{% code title="lib/users.ex" lineNumbers="true" %}
```elixir
defmodule Users do
  def create(params) do
    with {:ok, validated_user} <- validate(params),
         {:ok, saved_user} <- save(validated_user),
         {:ok, activated_user} <- activate(saved_user) do
      {:ok, activated_user}
    else
      error ->
        error
    end
  end

  # ...

  defp activate(user) do
    data = %{
      name: user.name,
      is_saved: user.is_saved,
      is_activated: true
    }

    {:ok, data}
  end
end
```
{% endcode %}

```shell
mix test test/users_test.exs
Compiling 1 file (.ex)
..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 0 failures
```

Perceba como ficou simples adicionar novas funções a funcionalidade. É fácil de ler, fácil dar manutenção e fácil remover. Era isso que estávamos procurando.

E por fim, a última etapa. Responder que tudo deu certo.

* ~~Validar se os dados estão corretos;~~
* ~~Salvar no banco de dados~~
* ~~Atualizar usuário para ativo~~
* Responder que tudo deu certo

Na verdade, já fazemos isso. Quando utilizamos with, precisamos já ter o que responder. Podemos transformar dados ou chamar outras funções, isso depende de cada cenário. Mas sabemos que, ao entrar no bloco de execução do, todas as etapas anteriores foram feitas com sucesso e podemos ir sem medo para a próxima.

O código final de nossa implementação ficou assim:

{% code title="lib/users_test.exs" lineNumbers="true" %}
```elixir
defmodule Users do
  def create(params) do
    with {:ok, validated_user} <- validate(params),
         {:ok, saved_user} <- save(validated_user),
         {:ok, activated_user} <- activate(saved_user) do
      {:ok, activated_user}
    else
      error ->
        error
    end
  end

  defp validate(user_params) when not is_nil(user_params.name), do: {:ok, user_params}
  defp validate(_user_params), do: {:error, "The field name is required"}

  defp save(user) do
    data = %{
      name: user.name,
      is_saved: true
    }

    {:ok, data}
  end

  defp activate(user) do
    data = %{
      name: user.name,
      is_saved: user.is_saved,
      is_activated: true
    }

    {:ok, data}
  end
end
```
{% endcode %}

## Conclusão

A declaração `with` é poderosa, podemos usar para compor uma funcionalidade completa. Isso nos ajuda na legibilidade e manutenibilidade. Também nos força a pensar de forma funcional, uma vez que só podemos usar ela como função.

Existem mais sobre `with` que podemos aprender, mas acredito que esse capitulo ficou grande o suficiente.&#x20;

