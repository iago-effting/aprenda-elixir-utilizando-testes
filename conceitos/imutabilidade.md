# 🔥 Imutabilidade

{% hint style="warning" %}
**Aviso sobre essa categoria**

Abordarei apenas o básico para seguirmos nos estudos. Imutabilidade é um assunto complexo. Entender as vantagens e desvantagens pode consumir tempo e também confundir quem está começando em linguagens funcionais. Por hora, irei somente abordar o básico para entendimento da linguagem e para darmos continuidade nos estudos.
{% endhint %}

Como o próprio nome diz, imutabilidade significa imutável, algo constante, que não muda. Isso quer dizer, toda definição uma vez feita, nunca mudará. Para termos valores diferente, por exemplo, em uma variável, nós precisaremos sobrepor ela, jogando fora todo o valor antigo e adotando o novo valor.

Vamos a um exemplo simples:

* Criaremos um [`map`](../basico/map.md) de user onde terá o atributo `name`&#x20;
* Alteraremos o `name` para `Cafe com elixir`

Vamos criar nosso teste para isso

<pre class="language-elixir" data-title="test/imutability_test.exs" data-line-numbers><code class="lang-elixir">defmodule ImmutabilityTest do
  use ExUnit.Case

  test "trying immutability" do
    user = %{name: "iago"}
<strong>    updated_user = %{user | name: "iago updated"}
</strong>
    assert user.name == "iago"
    assert updated_user.name == "iago updated"
  end
end
</code></pre>

```shell
mix test test/imutability_test.exs
.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 0 failures
```

**Duas coisas para notar aqui:**

1. Mesmo atualizando `user` para o novo nome, o valor de `user` não se alterou;&#x20;
2. Após a atualização, é retornado o valor atualizado do map. Esse novo valor nós adicionamos dentro de `updated_user` e essa variável tem a atualização do `user`.

Caso você queira continuar com a mesma variável, porém, com o novo valor, você vai precisar sobrepor o valor da variável anterior.

{% code title="" lineNumbers="true" %}
```elixir
defmodule ImmutabilityTest do
  use ExUnit.Case

  # ...
  test "override variable" do
    user = %{name: "iago"}
    user = %{user | name: "iago updated"}

    assert user.name == "iago updated"
  end
end
```
{% endcode %}

```shell
mix test test/imutability_test.exs
..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 0 failures
```

Tudo no elixir é imutável, funcionando com essa mesma ideia.&#x20;

Mantenha isso em mente.



### Conclusão

Abordamos o básico de imutabilidade. É um ponto importante no estudo de linguagens funcionais. Ela nos ajuda a evitar diversos problemas na utilização da concorrência, nos dando uma boa vantagem em relações a linguagens mutaveis.

Lembrando, toda vez que realizarmos uma mudança de dados, o dado original nunca será alterado, você terá que cuidar dessa re-assinatura ou sobreposição de valores.



