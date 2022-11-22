---
description: As constantes do elixir
---

# Atoms

Atoms são constantes onde seu valor é o próprio nome. Sua definição vem com um prefixo de dois pontos `:` seguido de um nome. Exemplo `:apple`.

Um ato é igual a outro atom com mesmo nome.

{% code title="test/atom_test.exs" lineNumbers="true" %}
```elixir
defmodule AtomTest do
  use ExUnit.Case

  test "simple tests about atom" do
    apple = :apple

    assert apple == :apple
    assert :new_atom == :new_atom
  end
end
```
{% endcode %}

Rodando esse código teremos um relatório de sucesso.

```shell
mix test 
....
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 tests, 0 failures
```

Atoms podem ser definidos dinamicamente, para isso poder fazer de duas formas:

1. Utilizando interpolação
2. Utilizando conversão

#### 1. Interpolação

<pre class="language-elixir" data-title="test/atom_test.exs" data-line-numbers><code class="lang-elixir">defmodule AtomTest do
  use ExUnit.Case
  
  # ...

  test "interpolation" do
    value = "noise"
<strong>    new_atom = :"#{noise}"
</strong>
    assert new_atom == :noise
  end
end</code></pre>

```shell
mix test 
.....
Finished in 0.02 seconds (0.00s async, 0.02s sync)
2 tests, 0 failures
```

#### 2. Conversão

<pre class="language-elixir" data-title="test/atom_test.exs" data-line-numbers><code class="lang-elixir">defmodule AtomTest do
  use ExUnit.Case
  
  # ...

  test "conversion" do
    value = "noise"
<strong>    new_atom = String.to_atom(value)
</strong>
    assert new_atom == :noise
  end
end</code></pre>

```shell
mix test
......
Finished in 0.02 seconds (0.00s async, 0.02s sync)
3 tests, 0 failures
```

{% hint style="warning" %}
#### Atenção

Devemos tomar cuidado na criação de atoms dinamicamente em nossas aplicações. Quando atoms são criados eles se mantem na memória até que a aplicação reinicie.\
\
Isso quer dizer que, se tivermos uma forma de criar atoms dinamicamente exposta para o usuário, corremos o risco de usuários mal intencionados criarem N atoms, até que nossa aplicação não responda corretamente.
{% endhint %}

Temos algumas formas de evitar esse tipo de problema. Uma forma é a utilização do [`String.to_existing_atom/1`](https://hexdocs.pm/elixir/1.12/String.html#to\_existing\_atom/1) onde, um atom so será gerado, caso já tenha sido criado antes. Com isso podemos limitar o que pode e o que não pode ser criado.&#x20;

<pre class="language-elixir" data-title="lib/atom_test.exs" data-line-numbers><code class="lang-elixir">defmodule AtomTest do
  use ExUnit.Case

  test "to_existing_atom/1" do
    value = "atom_that_does_not_exist"
<strong>    new_atom = String.to_existing_atom(value)
</strong>
    assert new_atom == :atom_that_does_not_exist
  end
end
</code></pre>

```shell
mix test
1) test to_existing_atom/1 (AtomTest)
     test/atom_test.exs:25
     ** (ArgumentError) errors were found at the given arguments:
     
       * 1st argument: not an already existing atom
     
     code: new_atom = String.to_existing_atom("atom_that_does_not_exist")
     stacktrace:
       :erlang.binary_to_existing_atom("atom_that_does_not_exist", :utf8)
       test/atom_test.exs:27: (test)
```

E caso ele já tenha sido criado, não teremos problemas

<pre class="language-elixir" data-title="lib/atom_test.exs" data-line-numbers><code class="lang-elixir">defmodule AtomTest do
  use ExUnit.Case
  
  # ...

  test "atom that does exist" do
    _atom = :atom_that_does_exist
    
    value = "atom_that_does_exist"
<strong>    new_atom = String.to_existing_atom(value)
</strong>
    assert new_atom == :atom_that_does_exist
  end
end</code></pre>

```shell
mix test
........
Finished in 0.02 seconds (0.00s async, 0.02s sync)
5 tests, 0 failures
```

### Conclusão

Atoms são muito utilizados no mundo elixir, são práticos e fáceis de usar. Vimos que precisamos tomar cuidado com a geração dinâmica e que podemos controlar o que pode ser criado. Mais a frente utilizaremos cada vez mais atoms para simplificar nosso código e estrutura-lo.
