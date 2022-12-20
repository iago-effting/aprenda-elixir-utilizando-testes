# 🔥 Condicionais

Condicionais nos ajudam a controlar o fluxo de nossa aplicação. Elas permitem execuções condicionais de blocos de código. Em elixir temos algumas formas de fazer isso

* `if/2` e `else/1`
* `case/2`
* `cond/1`

**Vamos defini-los de modo simples para seguirmos mais a fundo em cada um deles.**

`if/2` nos ajuda a condicionar um valor a uma execução, caso não atendida, cai no `else`

{% code lineNumbers="true" %}
```elixir
if expression do
  # ...
else 
  # ...
end
```
{% endcode %}

`case/2` nos ajuda a condicionar um valor de acordo com valores diferente de entrada. Caindo em cada caso especificado pelo valor esperado. Comumento utilizando [pattern matching](../pattern-matching.md).&#x20;

{% code lineNumbers="true" %}
```elixir
case {:iago, 30} do
  {:elixir, 10} -> 
    "Essa clausura não dará match"
    
  {:iago, age} ->
    "Essa clausura irá dar match e fará o bind da variavel age para 30"
    
  _ -> 
    "Qualquer valor" # Caso nenhuma das clausuras acima de certo, ele cairá aqui
end 
```
{% endcode %}

`cond/1` nos ajuda condicionar por meio de expressões, caindo na primeira condição que seja verdadeira.

{% code lineNumbers="true" %}
```elixir
x = 3

cond do
  x == 5 ->
   "Isso não será verdadeiro"
   
  (x + 8) > 10 ->
    "Nem isso"
    
  x == 3 ->
    "Mas isso será" # logo essa clausura será sucesso e esse bloco será executado.
    
  true ->
    "Nunca virá aqui, pois a clausura anterior é verdadeira"
end
```
{% endcode %}

Agora vamos seguir mais a fundo em cada uma das condicionais.



###
