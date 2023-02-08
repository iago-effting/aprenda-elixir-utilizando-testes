# Funções anônimas

Funções anônimas funcionam parecidos com as [funções nomeadas](nomeadas.md). Enquanto a função nomeada possui um nome próprio, a função anonima possui seu comportamento, podendo ser atribuido a qualquer variável, chamamos isso de "First class citizen". que significa que tratamos funções como valores.

Vamos criar um teste simples, um usando uma função nomeada, criado no capítulo [Funções nomeadas](nomeadas.md). Depois criaremos um segundo que faz a mesma coisa, porém, como uma função anônima e por ser first class citizen, colocaremos ela em uma variável pois variáveis recebem valores.

<pre class="language-elixir" data-title="" data-line-numbers><code class="lang-elixir">defmodule FunctionTest do
  use ExUnit.Case

  test "named function" do
<strong>    assert Math.sum(1, 5) == 6
</strong>  end

  test "success" do
    sum = fn x, y -> x + y end

<strong>    assert sum.(10) == 100
</strong>  end
end
</code></pre>

O que vale a penas ver aqui é a execução e definição. Enquanto a função nomeada chama um module + a função (`Math.sum/2`), a função anonima so tem o comportamento da função.&#x20;

Também podemos notar que a definição de uma função anonima começa com a palavra chave `fn` seguido dos parametros e um seta `->` para a direita, fechando entao com `end`, ficando assim:

```elixir
fn param_1 -> IO.inspect param_1 end
```

* Entre o `fn` e a seta `->` temos os parametros que funcionam igual o parametro de funções nomeadas.&#x20;
* Entre a seta `->` e o `end` temos o corpo da função, onde nossa logica é criada.

Se atribuirmos esse comportamente a uma variável, por exemplo x, para conseguir rodar ela, utilizando o código `x.()`. O ponto é uma peça fundamental para rodar funções anonimas. Caso você tire ele, um relatório de erro aparecerá. Vamos fazer esse teste

{% code title="" lineNumbers="true" %}
```elixir
defmodule FunctionTest do
  use ExUnit.Case

  # ...
  
  test "success" do
    sum = fn x, y -> x + y end

    assert sum(10) == 100
  end
end
```
{% endcode %}

```sh
mix test test/function_test.exs
warning: variable "sum" is unused (if the variable is not meant to be used, prefix it with an underscore)
  test/function_test.exs:9: FunctionTest."test success"/1


== Compilation error in file test/function_test.exs ==
** (CompileError) test/function_test.exs:11: undefined function sum/1 (expected FunctionTest to define such a function or for it to be imported, but none are available)
```

Ele avisa que a função `sum/1` não foi definida e ele está certo, o que temos é uma função anonima.

Existem diversos casos que a função anonima é mais apropriada. Como quando quisermos passar para dentro de uma função que espera como argumento uma outra função, mas não queremos nomear ela, pois já esta claro o suficiente sua intenção. Temos isso acontecendo em várias funções nativas do elixir. Um exemplo é a utilização de `Enum.map/2`.

{% hint style="info" %}
Não se importe agora com o que seja o Enum.map/2, veremos isso depois.
{% endhint %}

Aqui podemos ver novamente o **First Class Citizen** agindo. O segundo parâmetro de `Enum.map/2` espera um valor do tipo função. Podemos então colocar a função anonima diretamente, sem necessidade de passar por uma variável antes.

{% code lineNumbers="true" %}
```elixir
Enum.map([1, 2, 3], fn x -> x * 2 end
```
{% endcode %}

### Conclusão

Funções anônimas podem parecer estranhas em primeiro contato. Mas uma vez que você esteja lidando com problemas reais em seu programa, verá que ele se torna útil em diversas ocasiões.
