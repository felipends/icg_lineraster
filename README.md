## ICG - Rasterização de linhas

#### José Felipe Nunes da Silva - 20170019610
#### Rebeca Raab Bias Ramos - 20170070453

### Rasterização de linhas

A rasterização de linhas é a técnica utilizada para desenhar linhas em uma tela formada por pixels, também conhecidos como displays raster (eg., displays de LCD ou LED). Para realizar tal tarefa é normalmente utilizado algum algoritmo que escolhe um conjunto de pixels serão utilizados para formar uma linha (i.e., primitiva matemática descrita por dois vértices), fornecendo as coordenadas destes no plano cartesiano, de uma forma que seja utilizado o menor núemro de pixels possível, uma vez que, matematicamente, uma linha é infinitamente fina. Na figura a seguir é apresentado um exemplo de como uma linha contínua pode ser representada de forma discretizada em um display raster.

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/ab/Bresenham.svg/2000px-Bresenham.svg.png" alt="rasthered line" style="width:400px;transform:scaleX(-1);"/>

Existem diversos algoritmos para desempenhar tal tarefa. Neste Trabalho foi utilizado o algoritmo de Bresenham, que tem como base o critério do ponto médio e realiza apenas operações com números inteiros.

### Algoritmo de Bresenham

O algoritmo funciona de forma incremental, levando em conta o pixel anterior para escolher o seguinte. Como o pixel inicial é um vértice conhecido de coordenadas (x0, y0), escolhem-se os seguintes a partir dele, os candidatos são sempre o pixel imediatamente a leste (E) de coordenadas (x0+1, y0) ou a nordeste (NE) de coordenadas (x0+1, y0+1). Tal escolha é feita avaliando a posição da reta em relação ao ponto médio entre os pixels candidatos, tal ponto tem coordenadas (x0+1, y0+0,5). Tal processo se repete para os demais vértices (xi, yi) até que seja alcançado o vértice final (x1, y1).

Para avaliar a posição da reta em relação ao ponto médio citado no parágrafo anterior, é calculado o valor da equação implícita da reta naquele ponto ($D(x+1,y+0,5) = a(x+1) + b(y+0,5) + c$) sendo $a = \Delta y$, $b = - \Delta x$ e $c = \Delta x * l$, caso o valor da equação seja 0, significa que a reta passa pelo ponto médio e o pixel E é escolhido e o próximo valor de decisão é dado por $D = D(x+1,y+0,5) + a$, o mesmo acontece caso o valor da equação seja positivo, caso contrário o pixel NE é escolhido e o próximo valor de decisão é dado por $D = D(x+1,y+0,5) + a + b$. 

### Função MidPointLine

Nesta função são passados como parâmetros $x0, y0, x1, y1, color$. Com isso podemos calcular os valores de $\Delta$, onde temos que $\Delta x = x1 - x0$ e $\Delta y = = y1 - y0$. Em seguida, calculamos o **D**, $d = 2deltaY - deltaX$, assim como, os incremementos da variável de decisão definidos por $inc_L = 2 \Delta y$ e $inc_NE = 2 * (\Delta y - \Delta x)$.

Feito isso, iniciaremos os valores de x e y da seguinte forma, $x = x0$ e $y = y0$ e colorimos o pixel de coordenadas (x,y) com auxilio da função PutPixel.

Ademais repetiremos os passos anteriores enquanto $x < x1$. Dentro deste loop verificaremos se $d <= 0$ e incrementar d de inc_L, ou seja caminhar para direita, caso contrario incrementar d de inc_NE e incrementar y de uma unidade, caminhando em diagonal. 

E por fim, chamamos a função PutPixel novamente para marcar o pixel com as coordenadas x e y.

Entretanto, vale ressaltar que esse algoritmo é valido apenas para retas que passam pelo primeiro octante. 


### Função PutPixel
### Função DrawTriangle
### Função colorInterpolation



### Referências

- [https://cflavs.wordpress.com/2016/03/10/cgt1-poligonos-e-interpolacao-de-cores/](https://cflavs.wordpress.com/2016/03/10/cgt1-poligonos-e-interpolacao-de-cores/)
- [https://en.wikipedia.org/wiki/Bresenham's_line_algorithm](https://en.wikipedia.org/wiki/Bresenham%27s_line_algorithm)