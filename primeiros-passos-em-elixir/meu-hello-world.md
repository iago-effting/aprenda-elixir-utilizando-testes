---
description: Todo começo precisa de uma apresentação
---

# Meu "Hello, world"

Toda linguagem tem seu tutorial relacionado ao **Olá, mundo**, vamos fazer isso aqui também. Mas vamos focar também em como o teste funciona, mas primeiro precisamos criar um projeto usando o **mix:**

```shell
mix new hello_world

* creating README.md
* creating .formatter.exs
* creating .gitignore
* creating mix.exs
* creating lib
* creating lib/hello_world.ex
* creating test
* creating test/test_helper.exs
* creating test/hello_world_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd hello_world
    mix test

Run "mix help" for more commands.
```

Uma vez criado, você terá a seguinte estrutura:

```
hello_world/
├─ lib/
│  ├─ hello_world.ex
├─ test/
│  ├─ hello_world_test.exs
│  ├─ test_helper.exs
├─ mix.exs
├─ README.md
```

O **mix.exs** é nosso manifesto, é neste arquivo onde iremos definir tudo que será utilizado. Nome do projeto, versão, suas dependências e como será executado. A criação do projeto pelo mix nos facilita a etapa de configuração de projeto, saindo pronto para rodar, sem maiores complicações.

### Dependências

Todas as dependências vêm de um local chamado [hex](https://hex.pm/), o que facilita a inserção e atualização de  bibliotecas no projeto. Elas são definidas dentro do arquivo **mix.exs** na função **deps**. Se algo estiver lá, será instalado como dependência e adicionado ao projeto na pasta `deps` gerada na raiz do projeto, após rodar o comando para baixar as dependências. Para baixar as dependências basta rodar o comando `mix deps.get` .

```
mix deps.get

All dependencies are up to date
```

Nosso projeto ainda não possui nenhuma dependência, então a mensagem de "**All dependencies are up to date"** irá aparecer e podemos seguir.

### Testando

Nosso foco de validação vai ser na pasta `test` na raiz do projeto. Lá estarão todos os testes do projeto e helpers para nos ajudar nisso.

No arquivo `hello_world_test.exs` está nosso primeiro código:

{% code title="test/hello_world_test.exs" lineNumbers="true" %}
```elixir
defmodule HelloWorldTest do
  use ExUnit.Case

  test "greets the world" do
    assert HelloWorld.hello() == :world
  end
end
```
{% endcode %}

Por padrão, elixir traz o `ExUnit` como ferramenta de teste. Obtemos capacidades de teste quando adicionamos o código use `ExUnit.Case` em nosso arquivo, dando suporte a funções de `test`, **** `assert`, **** `describe` e outros.

O código é bem autoexplicativo, temos uma teste chamado _"greets the world"_ nele temos uma afirmação onde a resposta de `HelloWorld.hello()` deve ser igual a `:world` caso isso não ocorra, um erro será disparado.

Para rodar esse teste, podemos executar o comando onde executa todos os testes de uma vez ou somente um arquivo especifico. Vamos executar todos de uma vez:

```shell
mix test

Compiling 1 file (.ex)
Generated hello_world app
..
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 0 failures
```

Ao executar o comando teremos um relatório de resposta, O nosso nos diz que rodados 1 **doctest** e **1** **test** e não tivemos nenhuma falha.

Caso a resposta esteja diferente do esperado, você poderá ver o erro no relatório. Vamos alterar o teste para esperar `:new_world` e ver o que o relatório nos trás.

<pre class="language-elixir" data-title="test/hello_world_test.exs" data-line-numbers><code class="lang-elixir">defmodule HelloWorldTest do
  use ExUnit.Case

  test "greets the world" do
<strong>    assert HelloWorld.hello() == :new_world
</strong>  end
end</code></pre>

Basta agora rodar o teste e verificar o relatório:

```shell
mix test
.

  1) test greets the world (HelloWorldTest)
     test/hello_world_test.exs:5
     Assertion with == failed
     code:  assert HelloWorld.hello() == :new_world
     left:  :world
     right: :new_world
     stacktrace:
       test/hello_world_test.exs:6: (test)


Finished in 0.03 seconds (0.00s async, 0.03s sync)
1 test, 1 failure
```

No seu terminal terá cores melhores, em vermelho e verde, mas aqui já da de ter noção da resposta e também o que está incorreto. Vamos voltar agora o teste para tudo voltar a funcionar.

Os testes em elixir funcionam assim. São de extrema importância e simples de serem usados.

### Conclusão

Teste é uma ferramentas importante no desenvolvimento de software, nos ajudando a garantir o comportamento de nossos códigos. Nós próximos capítulos iremos conhecer mais da linguagem por meio do teste, para já termos uma base boa de como as coisas funcionam. Espero ver vocês por lá.
