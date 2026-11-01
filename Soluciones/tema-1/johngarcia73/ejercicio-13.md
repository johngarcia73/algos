# Ejercicio 13.

> **Enunciado**  En un splay tree que empieza como un camino de nodos se accede a las claves en orden. Acota el costo total con el lema de acceso y la cuenta de la sección 5 de la conferencia 4, y compáralo con lo que mide la sección 6. ¿Es ajustada la cota?

## Idea

El árbol inicial es un camino: su raíz contiene la clave mayor y cada nodo tiene como hijo izquierdo al nodo con la clave inmediatamente anterior. Por tanto, la clave `1` está en el fondo del árbol y el árbol tiene una profundidad máxima de `n-1`.

Si accedemos a las claves en orden creciente, el primer acceso puede ser caro. Sin embargo, *splay* no se limita a subir el nodo mediante rotaciones simples: en los casos `zig-zig` reorganiza primero al padre y después al nodo buscado. Esa reorganización evita que el árbol siga comportándose como un camino largo en los accesos siguientes.

El lema de acceso da una garantía general de `O(log n)` amortizado por acceso. Aplicada sin aprovechar el orden especial de la secuencia, produce una cota total `O(n log n)`. 

## Algoritmo

Para cada nodo `x`, definimos:

- `s(x)`: número de nodos del subárbol cuya raíz es `x`, contando el propio `x`.
- `r(x) = log₂ s(x)`: rango de `x`.
- `Φ = Σ_x r(x)`: potencial del árbol.

En el camino inicial, los tamaños de los subárboles son exactamente `1, 2, ..., n`: la hoja tiene tamaño `1`, su padre tamaño `2`, y así sucesivamente hasta la raíz, cuyo subárbol contiene los `n` nodos. Por tanto,

$$

\Phi_0=\sum_{j=1}^{n}\log_2 j=\log_2(n!).

$$

En particular, usando `n! ≤ nⁿ`,

$$

\Phi_0=\log_2(n!)\leq n\log_2 n.

$$

Así, el potencial inicial es `O(n log n)`.

El lema de acceso de Sleator y Tarjan establece que, al hacer *splay* de un nodo `x` en un árbol cuya raíz es `t`, el coste amortizado satisface

$$

\widehat c_i\leq 3\bigl(r(t)-r(x)\bigr)+1.

$$

En cada acceso el árbol sigue teniendo `n` nodos. Por ello, la raíz contiene todo el árbol y

$$

r(t)=\log_2 n.

$$

Además, `s(x) ≥ 1`, de modo que `r(x) ≥ 0`. Sustituyendo en el lema:

$$

\widehat c_i\leq 3\log_2 n+1.

$$

La secuencia contiene `n` accesos, uno por cada clave, en el orden `1,2,...,n`. Sumando los costes amortizados:

$$

\sum_{i=1}^{n}\widehat c_i
\leq n(3\log_2 n+1).

$$


Por definición del coste amortizado,

$$

\widehat c_i=c_i+\Phi_i-\Phi_{i-1},

$$

donde `c_i` es el coste real del acceso y `Φ_i` es el potencial después de ese acceso. Al sumar, las diferencias de potencial se telescopian:

$$

\sum_{i=1}^{n}c_i
=\sum_{i=1}^{n}\widehat c_i-(\Phi_n-\Phi_0).

$$

Como el potencial es una suma de logaritmos de tamaños de subárboles, `Φ_n ≥ 0`. Entonces

$$

\begin{aligned}
\sum_{i=1}^{n}c_i
&\leq n(3\log_2 n+1)+\Phi_0\\
&=n(3\log_2 n+1)+\log_2(n!)\\
&\leq n(3\log_2 n+1)+n\log_2 n\\
&=4n\log_2 n+n.
\end{aligned}

$$

Por tanto, la cota que se obtiene aplicando directamente el lema de acceso y el potencial inicial es

$$

\boxed{\sum_{i=1}^{n}c_i=O(n\log n).}

$$

Esta es una cota superior para el coste total de los `n` accesos.

## Comparación con la sección 6 de la conferencia 4

La sección 6 mide el coste de acceder a las claves `1,2,...,n` repetidamente, durante tres rondas completas, empezando por el mismo árbol camino. Para *splay*, obtiene los siguientes promedios de rotaciones por acceso:

| Número de nodos `n` | Splay: rotaciones por acceso | `log₂ n` |
|---:|---:|---:|
| 100 | 4,2 | 6,6 |
| 200 | 4,2 | 7,6 |
| 400 | 4,3 | 8,6 |
| 800 | 4,4 | 9,6 |

El coste medio medido se mantiene aproximadamente constante, mientras que `log₂ n` crece. En el experimento, el coste total de las `3n` operaciones es, por tanto, aproximadamente proporcional a `n`, no a `n log n`.

La misma sección compara con la heurística más sencilla de «subir a la raíz» mediante rotaciones simples. Para ella, el promedio pasa de `50,5` rotaciones por acceso con `n=100` a `400,5` con `n=800`. El experimento ilustra por qué el orden de las rotaciones en los pasos `zig-zig` importa: *splay* aplana eficazmente el árbol durante los accesos ordenados, mientras que subir a la raíz deja un camino largo que vuelve a encarecer los accesos.

## Conclusión: ¿es ajustada la cota?

La cota `O(n log n)` obtenida con el lema de acceso no es ajustada para el comportamiento que muestra esta secuencia. El análisis genérico no aprovecha que los accesos se hacen en orden; en la sección 6 observamos experimentalmente un coste medio cercano a una constante por acceso, es decir, un coste total de aspecto lineal.

Con el lema de acceso y el potencial  queda demostrada la cota `O(n log n)`.

Para afirmar formalmente una cota lineal en esta secuencia haría falta un argumento adicional específico para accesos ordenados (esto es el Teorema de Acceso Secuencial de los splay trees).

Por otro lado, el coste es al menos `n`, porque se realizan `n` accesos y la conferencia cuenta al menos una unidad por acceso. Así, si se dispone de esa cota lineal superior adicional, el coste total sería `Θ(n)`.