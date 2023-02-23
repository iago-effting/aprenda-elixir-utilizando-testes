# Enumeráveis

Os `Enumerables` são módulos em Elixir que fornecem uma série de funções para manipular [coleções](colecoes.md). As funções do módulo `Enumerables` permitem que você faça coisas como `filtrar`, `mapear`, `reduzir` e `classificar` [coleções](colecoes.md) de maneira eficiente. Essas funções são projetadas para trabalhar com qualquer tipo de coleção que implemente o protocolo `Enumerable`, que é implementado por muitas coleções em Elixir.

Ao usar as funções do módulo `Enumerables`, você pode escrever código mais conciso e legível do que ao manipular coleções manualmente usando loops. Por exemplo, em vez de escrever um loop para percorrer uma lista e executar uma determinada operação em cada elemento, você pode usar a função `Enum.map` para aplicar uma função a cada elemento da lista.

Alguns exemplos de funções do módulo `Enumerables` incluem:

* `map`: aplica uma função a cada elemento de uma coleção e retorna uma nova coleção com os resultados.
* `filter`: retorna uma nova coleção contendo apenas os elementos que atendem a uma determinada condição.
* `reduce`: aplica uma função a cada elemento de uma coleção para acumular um resultado único.
* `sort`: classifica uma coleção com base em uma determinada ordem.

Essas são apenas algumas das muitas funções disponíveis no módulo `Enumerables` em Elixir. Usando essas funções, você pode manipular coleções de maneira fácil e expressiva em seus programas Elixir.

Alguns dos módulos mais comuns que utilizam o protocolo Enumerable:

* [Enum](../basico/listas/enum.md)
* [Stream](../basico/listas/stream.md)
* [List](../basico/list.md)
* Range
* MapSet

Ao aprender a trabalhar com o protocolo Enumerable em Elixir, você poderá trabalhar com esses e outros módulos de forma mais eficiente e com menos código.

### Conclusão

Em resumo, o protocolo Enumerable é um recurso fundamental em Elixir que permite que você trabalhe com coleções de dados de maneira eficiente, fácil e consistente. Ao dominar o protocolo Enumerable, você pode escrever código mais robusto e conciso, tornando-se um programador mais eficaz em Elixir.
