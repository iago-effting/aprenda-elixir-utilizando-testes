# Integer

Em Elixir, `integer` é um tipo de dado que representa um número inteiro, ou seja, um número sem casas decimais. Em termos de programação, é um tipo de dado que pode ser usado para realizar operações matemáticas que não envolvem números fracionários.

Vamos criar um teste para entender melhor isso:

{% code title="test/integer_test.exs" lineNumbers="true" %}
```elixir
defmodule IntegerTest do
  use ExUnit.Case

  test "defining an integer" do
    my_integer = 1652468751356775

    assert is_integer(my_integer) == true
  end
end

```
{% endcode %}

```sh
mix test test/integer_test.exs
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Podemos também, realizar operações matemáticas:

{% code title="test/integer_test.exs" lineNumbers="true" %}
```elixir
defmodule IntegerTest do
  use ExUnit.Case

  # ...
  
  test "math" do
    assert 1 - 1 == 0
    assert 1 + 3 == 4
    assert 1 * 5 == 5
  end
end

```
{% endcode %}

Dois valores inteiros quando somados, diminuidos ou multiplicados, tem como resposta um inteiro. Mas uma vez que dividimos esse valor e o valor venha com casas decimais, como por exemplo `5 / 2 = 2.5` temos um tipo de retorno de dado diferente.&#x20;

<pre class="language-elixir" data-title="test/integer_test.exs" data-line-numbers><code class="lang-elixir">defmodule IntegerTest do
  use ExUnit.Case

  # ...
  
  test "division" do
    x = 5
    y = 2
    
    assert is_integer(x) == true
    assert is_integer(y) == true
    
    result = x / y
    
<strong>    assert is_float(result) == true
</strong>  end
end

</code></pre>

O dada resultante da operação veio com casas decimais, sendo agora do tipo float.

### Conclusão

O tipo `integer` é uma representação flexível de inteiros, que pode ser usada para armazenar inteiros de qualquer tamanho e sinal. Como é implementado usando a biblioteca `erlang`, os inteiros são de tamanho variável, o que significa que o tamanho do inteiro é determinado automaticamente pelo valor que ele contém.

