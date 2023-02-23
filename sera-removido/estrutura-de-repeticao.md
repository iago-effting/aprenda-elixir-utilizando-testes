# Estrutura de repetição

Devido a imutabilidade, isso é, não poder alterar valores de um tipo de dados, as estruturas de repetição são diferentes em linguagens funcionais.

Normalmente utilizamos, por exemplo, o `for` do javascript:

<pre class="language-javascript" data-line-numbers><code class="lang-javascript"><strong>for(i = 0; i &#x3C; array.length; i++) {
</strong>  array[i] = array[i] * 2;
}
</code></pre>

Na linha 1, possuímos `i = 0`. Esse i será atualizado a cada loop devido ao `i++`. Logo, o dado se altera para saber em que posição ele está. Nesse caso `i` é o estado dessa repetição que se muta. Também temos a alteração do array `array[i] = array[i] * 2` que muta o valor do dados que estamos iterando. Porém, elixir não permite alterarmos dados. Ele é imutável.

Em linguagem não funcionais, utilizamos estruturas de repetição como `foreach`, `while` ou `for`. Isso não funciona em elixir devido a [imutabilidade](../conceitos/imutabilidade.md). Uma vez que não podemos atualizar o atual estado de nosso loop, não temos como simplismente salvar o atual estado que ele se econtra (se na primeira interação ou em outra qualquer), precisamos então de outra solução.

Para resolver esse problemas, Elixir utiliza outra forma de looping chamado recursão. Veremos a ideia da recursão e também algumas funções nativas do elixir que fazem o looping usando a mesma.

* [Recursão](../basico/manipulacao-de-dados/recursao.md)
* [Módulo Enum](../basico/manipulacao-de-dados/enum.md)&#x20;
