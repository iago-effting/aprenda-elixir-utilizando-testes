# String

Em Elixir, uma string é uma sequência de caracteres delimitada por aspas duplas (" ") ou aspas simples (' ').&#x20;

Vamos a um exemplo.

<pre class="language-elixir" data-title="test/my_name_test.exs" data-line-numbers><code class="lang-elixir">defmodule MyNameTest do
  use ExUnit.Case
  
  test "Write my name with the double quote" do
<strong>    my_name = "Iago"
</strong>    
    assert my_name == "Iago"
  end
end
</code></pre>

Criamos uma `string` acima utilizando aspas duplas e esperamos que ela seja igual ao texto `"Iago"` também com aspas duplas. Vamos rodar esse teste:

```sh
mix test test/my_name_test.exs
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Tudo funcionando. Comentei mais acima que também podemos criar strings utilizando aspas simples (' '). Vamos dar uma olhada nisso.

<pre class="language-elixir" data-title="test/my_name_test.exs" data-line-numbers><code class="lang-elixir">defmodule MyNameTest do
  use ExUnit.Case
  
  test "Write my name with the single quote" do
<strong>    my_name = 'Iago'
</strong>    
    assert my_name == 'Iago'
  end
end
</code></pre>

Se rodarmos o teste, ele passará.

```sh
mix test test/my_name_test.exs
..
Finished in 0.01 seconds (0.00s async, 0.01s sync)
2 tests, 0 failures
```

Tudo perfeito até então. Vamos agora comparar um texto com aspas simples com um texto de aspas duplas e ver o que acontece.

<pre class="language-elixir" data-title="test/my_name_test.exs" data-line-numbers><code class="lang-elixir">defmodule MyNameTest do
  use ExUnit.Case

  # ...

  test "Write my name with the single quote and compare with double quotes" do
<strong>    my_name = 'Iago'
</strong>
<strong>    assert my_name == "Iago"
</strong>  end
end

</code></pre>

```sh
mix test test/my_name_test.exs


  1) test Write my name with the single quote and compare with double quotes (MyNameTest)
     test/my_name_test.exs:16
     Assertion with == failed
     code:  assert my_name == "Iago"
     left:  'Iago'
     right: "Iago"
     stacktrace:
       test/my_name_test.exs:19: (test)

..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 1 failure
```

A há! Te pegamos. Mesmo os dois sendo considerados textos, os tipos deles são diferentes.&#x20;

* Quando utilizamos aspas duplas (" ") o tipo de dado retornado é um `binário`.&#x20;
* Enquanto que quando utilizado aspas simples (' ') temos um `charlist`

Vamos alterar o teste para dizer que string com aspas simples é diferente de aspas duplas:

<pre class="language-elixir" data-title="test/my_name_test.exs" data-line-numbers><code class="lang-elixir">defmodule MyNameTest do
  use ExUnit.Case

  # ...

  test "Write my name with the single quote and compare with double quotes" do
    my_name = 'Iago'

<strong>    assert my_name != "Iago"
</strong>  end
end

</code></pre>

Apenas alteramos a linha 9. Se rodarmos os testes, teremos mensagem de sucesso.

```sh
mix test test/my_name_test.exs
...
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 0 failures
```

Agora vamor provar que os tipos são diferentes, começando com aspas duplas. Vamos alterar o primeiro teste:

<pre class="language-elixir" data-title="test/my_name_test.exs" data-line-numbers><code class="lang-elixir">defmodule MyNameTest do
  use ExUnit.Case
  
  test "Write my name with the double quote" do
    my_name = "Iago"
    
    assert my_name == "Iago"
    
<strong>    assert is_binary(my_name) == true
</strong><strong>    assert is_list(my_name) == false
</strong>  end
end
</code></pre>

Adicionamos as validações `is_binary/1` onde esperamos que seja verdadeira e `is_list/1` onde esperamos que seja falso. Vamos rodar esse teste:

```sh
mix test test/my_name_test.exs
...
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 0 failures
```

Agora validaremos a string com aspas simples onde volta um charlist.

<pre class="language-elixir" data-title="" data-line-numbers><code class="lang-elixir">defmodule MyNameTest do
  use ExUnit.Case

  # ...

  test "Write my name with the single quote" do
    my_name = 'Iago'

    assert my_name == 'Iago'
    
<strong>    assert is_binary(my_name) == false
</strong><strong>    assert is_list(my_name) == true
</strong>  end

  # ...
end

</code></pre>

```sh
mix test test/my_name_test.exs
...
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 0 failures
```

Isso se comprova verdadeiro.

* Aspas duplas -> Retorna o tipo de dado binário
* Aspas simples -> Retorna o tipo de dados lista

Isso acontece porque as aspas simples (' ') são usadas para representar uma lista de caracteres individuais. Cada elemento da lista é um caractere Unicode de 1 byte, portanto, uma lista de caracteres pode ser vista como uma sequência de inteiros, onde cada inteiro representa um caractere.

Por outro lado, as aspas duplas (" ") são usadas para criar uma `string`. Em Elixir, uma `string` é uma sequência de `bytes`, e cada caractere Unicode pode ser representado por vários bytes. Logo, aspas duplas são usados para a criação de textos.

### Escolhendo um tipo

A escolha entre o uso de `charlists` ou `strings` em Elixir dependerá do contexto em que estamos trabalhando e das necessidades específicas do problema em questão. Aqui estão algumas diretrizes gerais que podem ajudar na escolha:

**Use `strings` quando:**

* A eficiência na manipulação de strings é importante e a concatenação não é um problema (por exemplo, quando as strings são pequenas e/ou há poucas concatenações);
* Você está trabalhando com operações que requerem o uso de funções de string específicas em Elixir, como `String.length/1`, `String.split/2`, `String.downcase/1`, entre outras;
* A interoperabilidade com outras linguagens não é um problema e o código está sendo escrito exclusivamente em Elixir.

**Use `charlists` quando:**

* A eficiência na concatenação de strings é importante e há muitas concatenações envolvidas;
* Você está trabalhando com código que usa `charlists` (por exemplo, se estiver interagindo com código escrito em Erlang);
* Você está trabalhando com caracteres Unicode complexos que requerem mais de um byte para serem codificados;
* Você precisa manipular listas de caracteres usando funções de lista em Elixir.

### Conclusão

Em resumo, `charlists` são mais adequadas para situações em que a eficiência na concatenação é importante e para lidar com caracteres Unicode complexos, enquanto `strings` são mais adequadas para operações gerais de manipulação de strings e interoperabilidade com outras linguagens.
