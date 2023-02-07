# case/2

O `case/2` nos ajuda a **condicionar um valor de acordo com diferentes valores de entrada**. Ele cairá no caso especificado pelo valor esperado. Normalmente utilizando [pattern matching](../pattern-matching.md). \
\
Um exemplo real:

<pre class="language-elixir" data-line-numbers><code class="lang-elixir"><strong>case HTTPoison.get("http://.../user/1") do
</strong>  {:ok, %HTTPoison.Response{body: body, status: 200}} -> Jason.decode(body)
  {:ok, %HTTPoison.Response{body: body, status: 404}} -> {:error, :not_found}
  {:ok, %HTTPoison.Response{body: body, status: 401}} -> {:error, :unauthorized}
  {:error, reason} -> {:error, reason}
end
</code></pre>

{% hint style="warning" %}
```
HTTPoison.get/1

HTTPoison é um biblioteca escrita em elixir para realizar requisições HTTP externas.
Caso queira, você pode ver mais sobre ela na página de biblioteca

https://github.com/edgurgel/httpoison
```
{% endhint %}

Temos no exemplo uma chamada a um endpoint para resgatar um usuário com o id 1. No mundo real temos vários casos de resposta utilizando [pattern matching](../pattern-matching.md) para cada caso.

* **status: 200** -> Deu tudo certo e o usuário foi retornado no boddy
* **status: 404** -> O usuário com id 1 não foi encontrado
* **status: 401** -> Você não possui autorização para acessar esse endpoint
* **:error** -> Algo deu errado na chamada do endpoint mas não possuimos tratamento para isso, logo, vou apenas retornar a razão do erro junto com a [tupla de error](../../conceitos/convencoes.md#tupla-ok-result-e-error-reason).\


Case se torna um bom recurso para conseguirmos lidar com todos os casos dessa requisição e facilmente trata-las a fim de avisar o usuário ou tentar uma ação corretiva. No nosso caso, apenas alertamos que algo não saiu como o esperado.

### Criando nosso código

Vamos a um código mais simples para aplicarmos o conceito.

Utilizaremos o código de autorização feita no estudo de [cond/1](cond-1.md) . Precisamos obter os dados de um recurso, que vamos chamar aqui de `articles.` Pra isso o usuário deve estar autorizado a receber a informação. Qualquer nível de funcionário conseguirá realizar essa operação.

{% code title="test/article_test.exs" lineNumbers="true" %}
```elixir
defmodule ArticlesTest do
  use ExUnit

  test "success: user has the authorization to get articles" do
    user = %User{id: 156, name: "Iago", level: 1}

    assert {:ok, _articles} = Articles.all()
  end
end
```
{% endcode %}

```
mix test test/articles_test.exs
warning: Articles.all/1 is undefined (module Articles is not available or is yet to be defined)
  test/articles_test.exs:7: ArticlesTest."test success: user has authorization to get articles"/1



  1) test success: user has the authorization to get articles (ArticlesTest)
     test/articles_test.exs:4
     ** (UndefinedFunctionError) function Articles.all/1 is undefined (module Articles is not available)
     code: assert {:ok, _articles} = Articles.all(user)
     stacktrace:
       Articles.all(%User{id: 156, name: "Iago", level: 2})
       test/articles_test.exs:7: (test)


Finished in 0.03 seconds (0.00s async, 0.03s sync)
1 test, 1 failure
```

Teste falhou por que não temos ainda o módulo `Articles.` Vamos cria-lo e já adicionar a logica utilizando o `Authorizatin.access/1`. Vamos fazer esse teste passar o mais simples possível.

<pre class="language-elixir" data-title="lib/articles.ex" data-line-numbers><code class="lang-elixir">defmodule Articles do
  def all(_user) do
    articles = [%{title: "This is an example"}]

<strong>    {:ok, articles}
</strong>  end
end
</code></pre>

```sh
mix test test/articles_test.exs
Compiling 1 file (.ex)

.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Primeira etapa finalizada. Vamos para o segundo problema. Caso você não tenha um nível de acesso acima de 1, você não pode acessar os dados.

{% code title="test/articles_test.exs" lineNumbers="true" %}
```elixir
defmodule ArticlesTest do
  use ExUnit.Case

  # ...

  test "fail: user has not authorization to get articles" do
    user = %User{id: 156, name: "Iago", level: 1}

    assert {:error, _reason} = Articles.all(user)
  end
end
```
{% endcode %}

Rodando o teste, teremos um relatório de erro.

```elixir
mix test test/articles_test.exs
Compiling 1 file (.ex)


  1) test fail: user has not authorization to get articles (ArticlesTest)
     test/articles_test.exs:10
     match (=) failed
     code:  assert {:error, _reason} = Articles.all(user)
     left:  {:error, _reason}
     right: {:ok, [%{title: "This is an example"}]}
     stacktrace:
       test/articles_test.exs:13: (test)

.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 1 failure
```

Temos agora um cenário novo. Quando o seu nível de acesso é baixo, ele não deveria deixar passar. Mas como esta agora voce consegue acessar os artigos normalmente.&#x20;

Vamos adicionar a checagem de autorização. A autorização responde de duas formas:

* {:ok, data} => quando algo tem sucesso
* {:error, reason}  => quando algo está errado.

Já que possuimos esses dois cenários e ambos podem ser pegos por pattern matching podemos utilizar case/2 para resolver o problema.

1. Utilizaremos case/2 com o primeiro argumento a chamada a função Authorization.access/1
2. Utilizaremos o bloco do para realizar os matchs dos cenários
3. Retornaremos o valor dependendo do caso.

{% code title="lib/articles.ex" lineNumbers="true" %}
```elixir
defmodule Articles do
  def all(user) do
    articles = [%{title: "This is an example"}]

    case Authorization.access(user)
      {:ok, articles} -> {:ok, articles}
      {:error, reason} -> {:error, reason}
    end
  end
end
```
{% endcode %}

Dentro do segundo argumento do `case/2` temos a chamada do escopo  `do` onde isola o contexto de `case`. Cada linha dentro do escopo `do` é um possível cenário a ser coberto. Sendo o lado esquerdo a resposta de `Authorization.access` (o que foi chamado no primeiro argumento do `case/2`). Para acessar um caso especifico o pattern matching deve ser válido. Caso não seja ele passa para o proximo match. Caso não encontra nenhum que seja valido ele dispara uma exceção.

Vamos rodar o teste novamente

```sh
mix test test/articles_test.exs
..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 0 failures
```

Tudo funcionando novamente. Com isso você pode criar diversos casos dentro desse case em articles. Como por exemplo:

* Somente usuários com permissões de level 2 conseguem pegar determinados artigos
* Quando tivermos level 0 pode ser apenas dito que nada foi encontrado.

Assim você consegue expandir os casos de uma funcionalidade.



### Conclusão

Case é uma poderosa ferramenta para ter em sua caixa de ferramentas. Várias bibliotecas a utilizam. Também conseguimos tirar maior proveitos da [conveção de tuplas](../../conceitos/convencoes.md#tupla-ok-result-e-error-reason), sendo assim, mais fácil a sua utilização.



