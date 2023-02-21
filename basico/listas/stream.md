# Stream

Falamos sobre enumeráveis (ou enumerables), onde é uma característica do que é enumerável ou qualidade daquilo que se consegue enumerar. Em nosso contexto, algo que podemos iterar, passando um por um. Fizemos exemplos de iteração no capítulo sobre [Enum](enum.md).&#x20;

Stream está nessa mesma categoria, com uma diferença importante. Streams são composições de enumeração preguiçosa.&#x20;

Isso quer dizer, diferente de listas que ao criamos são executadas, `Streams` não são. Eles contém tudo que precisamos para executar, mas só irão ser executados quando for necessário. Logo, preguiçoso.&#x20;

Vamos a um exemplo simples:

Precisamos agendar 5 eventos, porém, não queremos ter o resultado agora, porque só será processado semana que vem.&#x20;

Vamos ao teste:

<pre class="language-elixir" data-title="test/schedule_test.exs" data-line-numbers><code class="lang-elixir">defmodule EventTest do
  use ExUnit.Case

  describe "schedule/1" do
    test "Create a lazy list and execute later" do
      range = 1..5

<strong>      stream = Stream.map(range, &#x26;Event.schedule/1)
</strong><strong>      assert %Stream{enum: 1..5, funs: [_function_1]} = stream
</strong>    end
  end
end

</code></pre>

O teste parece complexo por nao ser tão comum utilizarmos Stream desa forma, mas, vamos tentar entende-lo.&#x20;

Na linha 8 utilizamos o `Stream.map/2` passando no primeiro argumento um enumerable (no nosso caso foi um range, mas poderia ser uma lista também). No segundo paramentro, passamos a função que queremos que execute para cada elemento de nosso enumerable. Isso quer dizer que esse função será executada para cada item dentro do campo enum.&#x20;

Na linha 9 garantimos que criamos nosso `enum` com o valor de range (1 até 5) e em `funs`, que temos uma função para ser executada para cada interação. No teste de mesa seria:

```elixir
Event.schedule(1)
Event.schedule(2)
Event.schedule(3)
Event.schedule(4)
Event.schedule(5)
```

Porém, estamos usando `Stream` e esse código ainda não foi executado. Vamos rodar o teste para ver o que temos:

```sh
mix test test/event_test.exs
warning: Event.schedule/1 is undefined (module Event is not available or is yet to be defined)
  test/event_test.exs:8: EventTest."test schedule/1Create a lazy list and execute later"/1

.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
1 test, 0 failures
```

Estranho não? O teste passou, mesmo acusando em um `warning` que não possuimos `Event.schedule/1`. Isso quer dizer que a função não foi executada, como já esperavamos. Lembrando que `Stream` é um carregamento preguiçoso, ele só será executado quando realmente precisar. Vamos seguir até falhar. Precisamos agora fazer ele executar. Vamos atualizar nosso teste.

<pre class="language-elixir" data-title="test/event_test.exs" data-line-numbers><code class="lang-elixir">defmodule EventTest do
  use ExUnit.Case

  describe "schedule/1" do
    test "Create a lazy list and execute later" do
      range = 1..5

      stream = Stream.map(range, &#x26;Event.schedule/1)
      assert %Stream{enum: 1..5, funs: [_function_1]} = stream

<strong>      Stream.run(stream)
</strong>    end
  end
end

</code></pre>

A linha 12 roda o streaming para mim e nessa hora ele tenta executar a função passada. Vamos rodar o teste:

```sh
mix test test/event_test.exs
warning: variable "response" is unused (if the variable is not meant to be used, prefix it with an underscore)
  test/event_test.exs:11: EventTest."test schedule/1 Create a lazy list and execute later"/1

warning: Event.schedule/1 is undefined (module Event is not available or is yet to be defined)
  test/event_test.exs:8: EventTest."test schedule/1 Create a lazy list and execute later"/1



  1) test schedule/1 Create a lazy list and execute later (EventTest)
     test/event_test.exs:5
     ** (UndefinedFunctionError) function Event.schedule/1 is undefined (module Event is not available)
     code: response = Stream.run(stream)
     stacktrace:
       Event.schedule(1)
       (elixir 1.14.3) lib/stream.ex:612: anonymous fn/4 in Stream.map/2
       (elixir 1.14.3) lib/range.ex:392: Enumerable.Range.reduce/5
       (elixir 1.14.3) lib/stream.ex:1811: Enumerable.Stream.do_each/4
       (elixir 1.14.3) lib/stream.ex:689: Stream.run/1
       test/event_test.exs:11: (test)


Finished in 0.04 seconds (0.00s async, 0.04s sync)
1 test, 1 failure
```

Agora temos nosso primeiro erro. Ele esta avisando que não possuímos a função `Event.schedule/1`. O mais interessante é que agora ele falou que na linha 8 também não foi encontrado a função. Esse é um dos diferenciais de utilizar `Stream`. Ele deixa tudo pronto para ser executado e quando realmente precisar, ele vai realizar a computação e dar o resultado.

Imagina um cenário onde temos milhoes de dados. Esses dados precisam ser computados na hora, podemos fazer isso aos poucos e com `Stream` podemos criar uma lógica onde isso seja possível. Deixando o processamento mais rápido e não gastando recursos de máquina a toa.

Vamos resolver o problema do teste e criar nossa implementação. Você vai perceber que agora se tornou uma função comum, como fizemos no [capítulo sobre Enum](enum.md). Vamos criar a função apenas para passar os testes até então:

{% code title="lib/event.ex" lineNumbers="true" %}
```elixir
defmodule Event do
  def schedule(_x), do: nil
end

```
{% endcode %}

```sh
mix test test/event_test.exs
.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 0 failures
```

Perfeito, estamos conseguindo executar `Event.schedule/1`, porém, ele não faz nada. Vamos alterar nosso teste para que ele diga que o evento x foi agendado.&#x20;

{% code title="test/event_test.exs" lineNumbers="true" %}
```elixir
defmodule EventTest do
  use ExUnit.Case

  describe "schedule/1" do
    test "Create a lazy list and execute later" do
      range = 1..5

      stream = Stream.map(range, &Event.schedule/1)
      assert %Stream{enum: 1..5, funs: [_function_1]} = stream

      response = Stream.run(stream)
      
      assert response == [
        "event 1 was scheduled",
        "event 2 was scheduled",
        "event 3 was scheduled",
        "event 4 was scheduled",
        "event 5 was scheduled"
      ]
    end
  end
end

```
{% endcode %}

Vamos rodar esse teste:

```sh
mix test test/event_test.exs


  1) test schedule/1 Create a lazy list and execute later (EventTest)
     test/event_test.exs:5
     Assertion with == failed
     code:  assert response == [
              "event 1 was scheduled",
              "event 2 was scheduled",
              "event 3 was scheduled",
              "event 4 was scheduled",
              "event 5 was scheduled"
            ]
     left:  :ok
     right: ["event 1 was scheduled", "event 2 was scheduled", "event 3 was scheduled", "event 4 was scheduled", "event 5 was scheduled"]
     stacktrace:
       test/event_test.exs:13: (test)


Finished in 0.03 seconds (0.00s async, 0.03s sync)
1 test, 1 failure
```

Algo estranho aconteceu. A resposta para [`Stream.run/1`](https://hexdocs.pm/elixir/1.12/Stream.html#run/1) foi `:ok`. Isso causou o problema que não estavamos esperando. Queriamos a lista de eventos agendados. Vamos entender isso.

A função [`Stream.run/1`](https://hexdocs.pm/elixir/1.12/Stream.html#run/1) mesmo executando nosso `Stream` não retorna o valor processado. Ele é normalmente utilizado para iniciar um processo que não precisamos de uma resposta. Logo, essa função não serve para nós obtermos uma resposta do processamento. Para isso precisamos usar uma outra. &#x20;

Para resolver isso, utilizaremos uma função simples do módulo `Enum` chamada `to_list`. Quando utilizamos ela, a própria função entende quando estamos utilizando `Stream` e faz a conversão, executando o `Stream` e pegando a resposta, convertendo para uma lista e devolvendo para nos como uma lista simples. Vamos a implementação:

<pre class="language-elixir" data-title="lib/event.ex" data-line-numbers><code class="lang-elixir">defmodule EventTest do
  use ExUnit.Case

  describe "schedule/1" do
    test "Create a lazy list and execute later" do
      range = 1..5

      stream = Stream.map(range, &#x26;Event.schedule/1)
      assert %Stream{enum: 1..5, funs: [_function_1]} = stream

<strong>      response = Enum.to_list(stream) # apenas alteramos aqui
</strong>
      assert response == [
        "event 1 was scheduled",
        "event 2 was scheduled",
        "event 3 was scheduled",
        "event 4 was scheduled",
        "event 5 was scheduled"
      ]
    end
  end
end

</code></pre>

Apenas alteramos a linha 11 para faze a conversão do `Stream` para uma `lista`. Isso faz todo o processamento e nos devolve a lista que queremos. vamos rodar o teste e ver o que acontece:

```sh
mix test test/event_test.exs
.
Finished in 0.03 seconds (0.00s async, 0.03s sync)
1 test, 0 failures
```

Perfeito! Fizemos nosso primeiro processamento de `Stream` e tudo ocorreu bem.&#x20;

### Conclusão

Stream é uma lista de carregamento preguiçoso que só será executado quando realmente precisar. ele serve para evitar processamente sem necessidades, como quando não precisamos executar todos os dados de uma vez em uma lista de milhares de dados. Isso nós ajuda a salvar processamento.

Esse foi um exemlpo simples para entendermos o conceito de Stream. Fiz um vídeo mais avançado sobre processamento de grande quantidade de dados. Você pode dar uma olhada nele [nesse link](https://www.youtube.com/watch?v=w\_Z6BL9\_59w).

