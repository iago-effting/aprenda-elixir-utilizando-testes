# 🔥 Convenções

### Tupla `{:ok, result}` e `{:error, reason}`

Podemos facilmente entender que uma função não foi executada com sucesso, quando em seu retorno, temos essa estrutura `{:error, "Something went wrong"}`. Ou que tudo deu certo `{:ok, %{...}}`. Essa é uma convenção adotada pela comunidade elixir para facilitar a identificação de erros e sucessos, podendo tomar alguma ação com base nisso.

A definição de resposta funciona de duas formas.&#x20;

1. Em caso de sucesso retornamos uma [tupla](../basico/tuples.md) de `:ok`, junto com o `result`.&#x20;

```elixir
{:ok, result} = Authentication.login(email, password)
```

Podemos facilmente obter o resultado, garantindo que ele esteja funcionando devido ao pattern matching. Caso tenhamos uma resposta diferente de `{:ok, result}` o pattern matching falhará e saberemos que algo esta errado.

2\. Para definir um erro, seguimos na mesma ideia, porém, utilizando o atom de `:error` e a `reason`.

```elixir
{:error, reason} = Authentication.login(email, password)
```

Isso nos proporciona maior controle e também facilita a leitura do código.

O `result` e a `reason` são variáveis que criamos no pattern matching (para entender melhor isso, vá para a seção de [Pattern Matching ](../basico/pattern-matching.md)no livro.

Vou listar algumas bibliotecas famosas que utilizam dessa convenção:&#x20;

* [Ecto](https://hexdocs.pm/ecto/Ecto.Repo.html#c:insert/2-examples)
* [HTTPoison](https://hexdocs.pm/httpoison/HTTPoison.html#get/3)

Uma das grandes vantagens na utilização da tupla de `:ok/:error` é a utilização do [`with`](../basico/with.md) onde podemos controlar por meio de pattern matching os resultados esperados. De uma olhada na seção [with](../basico/with.md) do livro, para se aprofundar mais.

Um exemplo simples:

<pre class="language-elixir" data-line-numbers><code class="lang-elixir">def do_something() do
<strong>  with {:ok, function_result} &#x3C;- Module.function(), # tupla :ok
</strong><strong>       {:ok, another_result} &#x3C;-Module.another() do # tupla :ok
</strong>    IO.inspect("All good")
  else
<strong>    {:error, reason} -> IO.inspect("Something went worg: #{reason}") # tupla :error
</strong>  end
end
</code></pre>

### Função com bang (`!`)

Pode também ser notado que em alguns casos você pode adicionar um ponto de exclamação (`!`), chamada comumente de bang. Vimos isso nesse exemplo da lib HTTPoison `HTTPoison.get!/2`. Por convenção, essa invocação remove a tupla `:ok/:error` e retorna apenas o valor.&#x20;

Isso faz com que não tenhamos o poder de saber se algo deu certo por pattern matching. Dependendo o caso, é o melhor a se fazer.

**Um exemplo**

{% code lineNumbers="true" %}
```shell
iex> HTTPoison.get(url)
{:ok, %HTTPoison.Response{...}}

iex> HTTPoison.get!(url)
%HTTPoison.Response{...}
```
{% endcode %}

### Conclusão

Sempre que tiver criando funções, tenha em mente que ela pode falhar, e caso isso aconteça, precisamos saber que isso aconteceu.

Quando uma função segue de um ponto de exclamação `!`, a convenção nos diz que a resposta dessa função não trará a tupla com `:ok` ou `:error` e sim o resultado diretamente. Exemplo

