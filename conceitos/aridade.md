# Aridade

Normalmente você verá as funções com essa representação `map/2` ou `Enum.map/2`. Esse número após a barra se chama aridade. Ela vem da matemática onde significa o número de argumentos ou de operandos que uma função ou operação requer.

Ele tem o mesmo significado no Elixir. Ele indica quantos argumentos essa função necessita para ser executada. No exemplo acima temos o `Enum.map/2`. Leia-se que a função `map` do módulo `Enum` precisa de dois argumentos para poder ser utilizado.

Isso serve para as funções que você cria também. Vamos a um exemplo. Teremos uma função simples chamada `multiple` do módulo `Math` que multiplica 2 números `x` e `y`.&#x20;

Podemos representar isso dessa forma `Math.multiple/2`. Uma vez que você passe isso para alguém que entenda de aridade, ele entenderá o que é necessário para essa função.

Vamos ao teste

{% code title="test/math_test.exs" lineNumbers="true" %}
```elixir
defmodule MathTest do
  use ExUnit.Case
  
  describe "multiple/2" do
    test "success: return the result" do
      assert Math.multiple(3, 5) == 15
    end
  end
end
```
{% endcode %}

Rodando isso teremos um relatório de erro pois não possuímos a função.

```sh
mix test test/math_test.exs    
warning: Math.multiple/2 is undefined or private
  test/math_test.exs:6: MathTest."test multiple/2 success: return the result"/1



  1) test multiple/2 success: return the result (MathTest)
     test/math_test.exs:5
     ** (UndefinedFunctionError) function Math.multiple/2 is undefined or private
     code: assert Math.multiple(3, 5) == 15
     stacktrace:
       (hello_world 0.1.0) Math.multiple(3, 5)
       test/math_test.exs:6: (test)


Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

Agora que possuímos a representação e o teste de nossa função, vamos criar a implementação dela.

<pre class="language-elixir" data-title="lib/math.ex" data-line-numbers><code class="lang-elixir">defmodule Math do
<strong>  def multiple(x, y) do
</strong>    x * y
  end
end
</code></pre>

```elixir
mix test test/math_test.exs
Compiling 1 file (.ex)
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

Tudo funcionando. Mas, sabendo que nossa função é dois de aridade, o que acontece quando mandamos apenas um argumento? Vamos criar um teste para isso

{% code title="test/math_test.exs" lineNumbers="true" %}
```elixir
defmodule MathTest do
  use ExUnit.Case
  
  describe "multiple/2" do
    # ...
    
    test "fail: Insufficient Arguments" do
      Math.multiple(3)
    end
  end
end
```
{% endcode %}

<pre class="language-sh"><code class="lang-sh">test test/math_test.exs
<strong>warning: Math.multiple/1 is undefined or private. Did you mean:
</strong>
<strong>      * multiple/2
</strong>
  test/math_test.exs:10: MathTest."test multiple/2 fail: Insufficient Arguments"/1



  1) test multiple/2 fail: Insufficient Arguments (MathTest)
     test/math_test.exs:9
<strong>     ** (UndefinedFunctionError) function Math.multiple/1 is undefined or private. Did you mean:
</strong>     
           * multiple/2
     
     code: assert Math.multiple(3) == nil
     stacktrace:
       (hello_world 0.1.0) Math.multiple(3)
       test/math_test.exs:10: (test)

.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 1 failure
</code></pre>

Quando não encontra uma função com a especificação enviada, recebemos uma exceção dizendo que função não foi definida. Também recebemos uma sugestão de que talvez o que queremos seja a função `multiple/2` e não `multiple/1`.

Podemos fazer esse teste passar dizendo que esperamos que falhe quando isso aconteça. Vamos alterar nosso teste para esperar por iso.

<pre class="language-elixir" data-title="test/math_test.exs" data-line-numbers><code class="lang-elixir">defmodule MathTest do
  use ExUnit.Case
  
  describe "multiple/2" do
    # ...
    
    test "fail: Insufficient Arguments" do
<strong>      expected = "function Math.multiple/1 is undefined or private"
</strong>      assert_raise UndefinedFunctionError, expected, fn ->
        Math.multiple(3)
      end
    end
  end
end
</code></pre>

{% hint style="warning" %}
****[**assert\_raise**](https://hexdocs.pm/ex\_unit/ExUnit.Assertions.html#assert\_raise/2)&#x20;

Ele é uma função auxiliadora do `ExUnit` que captura uma exceção, assim podemos criar testes que esperamos que uma implementação falhe quando algo sai do esperado. Algumas vezes esse caso é necessário.
{% endhint %}

Executando isso, teremos sucesso nos testes

```sh
mix test test/math_test.exs
..
Finished in 0.03 seconds (0.00s async, 0.03s sync)
2 tests, 0 failures
```



