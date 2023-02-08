# Funções nomeadas como anônima

Em alguns casos queremos utilizar uma função nomeada como parâmetro para alguma outra função. Por natureza não podemos fazer isso com funções nomeadas, somente com funções anônimas como foi exemplificado no capítulo de [funções anônimas](anonimas.md).

Mas quando precisamos fazer isso, o elixir nos deixa transformar uma função nomeada em função anônima. Para isso, podemos utilizar o operador de captura (&)

Sua estrutura começa com o operador de captura, seguido da chamada da função + a aridade da função.&#x20;

```elixir
&String.reverse/1
```

Com isso ela se torna uma função anônima, nos possibilitando coloca-la em uma variável ou passar por parâmetro.

Um exemplo de como podemos fazer isso

```elixir
# usando função anonima
Enum.map([1,2,3], fn x -> x * 2 end)
# [2, 4, 6]

# usando função nomeada
defmodule MyModule do
  def math(x), do: x * 2 
end

Enum.map([1,2,3], &MyModule.math/1)
# [2, 4, 6]
```

