---
description: >-
  Precisamos de um lugar para nossas funções morarem, senão, elas podem se
  perder.
---

# Módulos

Módulos são onde nossas funções vivem. Elas não podem estar por ai sem um lugar para ficar, pois poderiam se perder e nunca mais ser utilizadas. Elixir obriga todas as funções estarem em módulos.

Para definir um module, utilizamos a palavra chave [`defmodule`](https://elixir-lang.org/getting-started/modules-and-functions.html) seguido do nome e da abertura de seu contexto usando `do`. No exemplo criado na seção Meu "Hello, world" podemos vê-lo no arquivo `hello_world.ex`

{% code title="lib/hello_world.ex" lineNumbers="true" %}
```elixir
defmodule HelloWorld do # aqui está o nome do nosso módulo
  def hello do
    :world
  end
end
```
{% endcode %}

Isso serve para quando chamarmos nossas funções, sabermos de onde elas estão vindo. Ao invés de somente `hello`, temos `HelloWorld.hello` e facilmente sabemos quem ela é.

No arquivo de teste podemos ver como o código é executado utilizando a chamada do modulo e depois a função.

<pre class="language-elixir" data-title="test/hello_world_test.exs" data-line-numbers><code class="lang-elixir">defmodule HelloWorldTest do
  use ExUnit.Case
  doctest HelloWorld

  test "greets the world" do
<strong>    assert HelloWorld.hello() == :new_world # Chamada do modulo e função
</strong>  end
end</code></pre>

Podemos definir infinitas funções dentro de um modulo (mas não façam isso, precisamos de organização certo? hehe). Alguns bons exemplos de módulos:

* `Authentication.login(user, password)`
* `Authentication.logout(user)`
* `Article.save(content)`
* `Article.delete(id)`
* `Article.update(article, new_content)`

Percebem como as funções são organizadas dentro de módulos? Isso facilita a leitura e organização do código e deixa as coisas mais intuitivas.

Temos por regra:

* Todo módulo deve começar com letra maiúscula, no estilo [CamelCase](https://pt.wikipedia.org/wiki/CamelCase);
* O modulo deve conter apenas o que é necessário para sua execução;

Nome de módulos podem ter mais de uma nível de profundidade, exemplo `Project.Authentication.Meta.Token` e geralmente seguem a estrutura de pastas

### Atributos de módulo

Podemos definir atributos no módulo. São um tipo de variáveis que vivem apenas dentro do módulo e nunca veem a luz do dia. Útil para utilização interna. Para declarar ela, utilizamos o arroba(@) seguido do nome do atributo e logo após o valor (sem sinal de igual). Exemplo `@attribute_name 10`

Vamos alterar nosso programa para concatenar o atributo de módulo em nossa resposta. Primeiro nosso teste:

<pre class="language-elixir" data-title="test/hello_world_test.exs" data-line-numbers><code class="lang-elixir">defmodule HelloWorldTest do
  use ExUnit.Case
  doctest HelloWorld

  test "greets the world" do
<strong>    assert HelloWorld.hello() == "Hello, new world"
</strong>  end
end</code></pre>

Rode o programa e voce terá o relatório de erro

```shell
mix test
1) test greets the world (HelloWorldTest)
     test/hello_world_test.exs:5
     Assertion with == failed
     code:  assert HelloWorld.hello() == "Hello, new world"
     left:  :world
     right: "Hello, new world"
     stacktrace:
       test/hello_world_test.exs:6: (test)
```

Precisamos alterar nosso programa para atender a nova resposta e para isso, utilizaremos atributos de módulo, apenas para exemplificar

<pre class="language-elixir" data-title="lib/hello_world.ex" data-line-numbers><code class="lang-elixir">defmodule HelloWorld do
<strong>  @message "Hello, new world" # definição do atributo de módulo
</strong>  
  def hello do
<strong>    @message # Utilização do atributo de módulo
</strong>  end
end
</code></pre>

Basta rodar novamente o teste e tudo estará funcionando.

```shell
mix test
Compiling 1 file (.ex)
.
Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

#### Para se manter atento

* Atributo de módulo não pode ser acessado do lado de fora do módulo.
* Suporta apenas tipos primitivos, então não poderá executar uma função para obter uma resposta diretamente nele.

### Conclusão

Módulos são formas de agregar funções a fim de deixa-las próximas por contexto, facilitando a utilização e criação da mesma.
