# Float

Em Elixir, o tipo de dado `float` representa números de ponto flutuante (ou números reais) que são usados para representar números fracionários ou valores decimais.

Um exemplo de uso de `float` seria o seguinte:

```elixir
a = 1.5
b = 2.75
c = a + b

IO.puts "c é igual a #{c}"
# c é igual a 4.25
```

Aqui estão alguns exemplos de testes de unidade para verificar se os valores `float` estão funcionando corretamente:

{% code title="test/float_test.exs" lineNumbers="true" %}
```elixir
defmodule FloatTest do
  use ExUnit.Case

  test "adição de float" do
    assert 2.5 + 3.0 == 5.5
  end

  test "subtração de float" do
    assert 4.0 - 1.5 == 2.5
  end

  test "multiplicação de float" do
    assert 2.0 * 3.0 == 6.0
  end

  test "divisão de float" do
    assert 6.0 / 2.0 == 3.0
  end

  test "conversão de inteiro para float" do
    assert 5 + 2.5 == 7.5
  end
end
```
{% endcode %}

Em resumo, `float` é um tipo de dado usado para representar números de ponto flutuante (ou números reais) que são usados para representar números fracionários ou valores decimais. `float` é uma ferramenta importante para lidar com cálculos matemáticos precisos e é comumente usado em muitas linguagens de programação, incluindo Elixir.

Ao trabalhar com `float`, é importante ter em mente que os números de ponto flutuante são aproximados e não podem representar todos os números reais com precisão. Portanto, é importante estar ciente de problemas de arredondamento e imprecisão ao lidar com `float`.

### Problema de ponto flutuante

Os números de ponto flutuante podem apresentar problemas de imprecisão quando são usados em cálculos que envolvem números muito grandes ou muito pequenos. Isso ocorre porque os números de ponto flutuante têm uma precisão finita e não podem representar todos os números reais com exatidão.

Exemplo:

{% code lineNumbers="true" %}
```elixir
a = 1000000000.0
b = 0.000001
c = a + b

IO.puts "c é igual a #{c}"
# c é igual a 1000000000.0
```
{% endcode %}

O resultado é impreciso e não inclui o valor `0.000001`. Isso ocorre porque `b` é tão pequeno que sua representação em ponto flutuante não pode ser armazenada com precisão suficiente.

Para evitar problemas de imprecisão com números de ponto flutuante, é importante ter em mente suas limitações e usar técnicas para minimizar a perda de precisão, como o arredondamento ou o uso de bibliotecas de precisão arbitrária.

### Conclusão

Em conclusão, os números de ponto flutuante (ou `float`) são um tipo de dado importante em Elixir e em muitas outras linguagens de programação. Eles são usados para representar números reais e são necessários para lidar com cálculos matemáticos precisos.

Por fim, testes de unidade bem escritos são importantes para garantir que as operações matemáticas envolvendo números de ponto flutuante estejam funcionando corretamente em um programa em Elixir.
