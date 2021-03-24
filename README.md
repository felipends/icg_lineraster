## ICG - Rasterização de linhas

#### José Felipe Nunes da Silva - 20170019610
#### Rebeca Raab Bias Ramos - 20170070453

### Rasterização de linhas

A rasterização de linhas é a técnica utilizada para desenhar linhas em uma tela formada por pixels, também conhecidos como displays raster (eg., displays de LCD ou LED). Para realizar tal tarefa é normalmente utilizado algum algoritmo que escolhe um conjunto de pixels que serão utilizados para formar uma linha, isto é, uma primitiva matemática descrita por dois vértices, fornecendo as coordenadas destes no plano cartesiano, de uma forma que seja utilizado o menor número de pixels possível, uma vez que, matematicamente, uma linha é infinitamente fina. Na figura a seguir é apresentado um exemplo de como uma linha contínua pode ser representada de forma discretizada em um display raster.

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/ab/Bresenham.svg/2000px-Bresenham.svg.png" alt="rasthered line" style="width:400px;transform:scaleX(-1);"/>

Existem diversos algoritmos para desempenhar tal tarefa. Neste Trabalho foi utilizado o algoritmo de Bresenham, que tem como base o critério do ponto médio e realiza apenas operações com números inteiros.

### Algoritmo de Bresenham

O algoritmo funciona de forma incremental, levando em conta o pixel anterior para escolher o seguinte. Como o pixel inicial é um vértice conhecido de coordenadas (x0, y0), escolhem-se os seguintes a partir dele. Os candidatos são sempre o pixel imediatamente a leste (E) de coordenadas (x0+1, y0) ou a nordeste (NE) de coordenadas (x0+1, y0+1). 

A escolha do pixel é feita avaliando a posição da reta em relação ao ponto médio entre os pixels candidatos, tal ponto tem coordenadas (x0+1, y0+0,5). Este processo se repete para os demais vértices (xi, yi) até que seja alcançado o vértice final (x1, y1).

Para avaliar a posição da reta em relação ao ponto médio citado no parágrafo anterior, é calculado o valor da equação implícita da reta naquele ponto $D(x+1,y+0,5) = a(x+1) + b(y+0,5) + c$ sendo $a = \Delta y$, $b = - \Delta x$ e $c = \Delta x * l$ ($l$ sendo o deslocamento no eixo y), caso o valor da equação seja 0, significa que a reta passa pelo ponto médio e o pixel E é escolhido e o próximo valor de decisão é dado por $D = D(x+1,y+0,5) + a$, o mesmo acontece caso o valor da equação seja positivo, caso contrário o pixel NE é escolhido e o próximo valor de decisão é dado por $D = D(x+1,y+0,5) + a + b$. 

Porém esta abordagem vale apenas para retas de coeficiente angular no intervalo \[0,1\], isto é, retas do primeiro octante de um plano cartesiano. Uma generalização pode ser realizada, como será mostrado nas seções seguintes.

### Implementação

Num primeiro momento foi implementada apenas a função **MidPointLineAlgorithm**. Nesta função são passados como parâmetros $x0, y0, x1, y1, color_0, color_1$, sendo os dois primeiros as coordenadas do primeiro pixel, os dois seguintes as coordenadas do úlitmo e os dois últimos as cores inicial e final da linha, que mais tarde são interpoladas ao decorrer dela. Com isso podemos calcular os valores de $\Delta$, onde temos que $\Delta x = x1 - x0$ e $\Delta y = y1 - y0$. Em seguida, calculamos o **D**, $d = 2 \Delta y - \Delta x$, assim como, os incremementos da variável de decisão definidos por $inc_L = 2 \Delta y$ e $inc_NE = 2 * (\Delta y - \Delta x)$.

Feito isso, iniciaremos os valores de x e y da seguinte forma, $x = x0$ e $y = y0$ e colorimos o pixel de coordenadas (x,y) com auxilio da função PutPixel, presente no framework fornecido pelo professor.

Ademais repetiremos os passos anteriores enquanto $x < x1$. Dentro deste loop verificaremos se $d <= 0$ e incrementar d de inc_L, ou seja caminhar para direita, caso contrario incrementar d de inc_NE e incrementar y de uma unidade, caminhando em diagonal. 

E por fim, chamamos a função PutPixel novamente para marcar o pixel com as coordenadas x e y.

A implementação desta função na linguagem JavaScript é exibida a seguir.

```js
function MidPointLineAlgorithm(x0, y0, x1, y1, color_0, color_1) {
  
  let dx = x1-x0;
  let dy = y1-y0;
  let d = 2 * dy - dx;

  let inc_L = 2 * dy;
  let inc_NE = 2 * (dy-dx);
  let x = x0;
  let y = y0;

  color_buffer.putPixel(x, y, color_0);
  while(x < x1){
    if (d <= 0){
      d += inc_L;
      x++;
    }else{
      d += inc_NE;
      x++;
      y++;
    }
    color_buffer.putPixel(x, y, color_0);
  }
}
```

Como citado na seção anterior, esta versão do algoritmo é válida apenas para retas que passam pelo primeiro octante. Como a apresentada na imagem a seguir, produzida ao executar a função com os seguintes parâmetros. 

```js
MidPointLineAlgorithm(25, 30, 100,80, [255,0,0,255], [255,255,0,255]); 
 ```

<img src="https://imgur.com/9BX6vUg.png" alt="rasthered line" style="width:400px;"/>

Para desenhar retas com diferentes coeficientes angulares, é proposta uma generalização do algoritmo a seguir.

### Generalização

Para realizar a generalização dos demais octantes pode-se utilizar o algoritmo implementado para o primeiro octante e realizar reflexões sobre os eixos. Sendo necessário verificar as distancias dos vértices no eixo x e do eixo y.

Para realizarmos esta transformação podemos utilizar os seguintes passos:

1. Primeiro, devemos calcular os valores de $\Delta x$ e $\Delta y$;
2. Então devemos analisar os valores de $\Delta x$ e $\Delta y$ para determinar em qual octante a reta se encontra.
3. Se o valor de $\Delta y$ for menor que o de $\Delta x$ utilizamos a função **plotLow** ou **plotHigh** para desenhar a reta ao longo do eixo x ou y, 
    1. Se verificamos se x0 é maior que x1 se for, invertemos a ordem dos vértices para ser desenhada a linha, senão a ordem dos vértices é mantida, ambas utilizando a função **plotLow**;
    2. Se y0 for maior que y1, invertemos a ordem dos vértices para ser desenhada a linha, senão a ordem dos vértices é mantida, ambas utilizando a função **plotHigh**;

Para implementar o algoritmo com tal generalização foi criada uma classe chamada Line, que recebe em seu construtor os parâmetros para a rasterização da linha, nela foram implementados os métodos **plotLow** e **plotHigh** para os propósitos já citados. A classe conta ainda com o método de interpolação de cores, explicado na seção a seguir.

A implementação da classe e seus métodos é apresentada a seguir, na linguagem JavaScript .

```js
class Line {
  constructor(x0, y0, x1, y1, color_0, color_1){
    this.x0 = x0;
    this.y0 = y0;
    this.x1 = x1;
    this.y1 = y1;
    this.color_0 = color_0;
    this.color_1 = color_1;
  }

  plotHigh() {
    let dx = this.x1 - this.x0;
    let dy = this.y1 - this.y0;
    
    let inc_x = 1;
    if(dx < 0){
      inc_x = -1;
      dx = -dx;
    }
    
    let d = (2*dx) - dy;
    let x = this.x0;
    
    let color = this.color_0;
    let dr = (this.color_1[0] - this.color_0[0])/dy;
    let dg = (this.color_1[1] - this.color_0[1])/dy;
    let db = (this.color_1[2] - this.color_0[2])/dy;
    
    for(let y = this.y0; y <= this.y1; y++){
      color = this.colorInterpolation(color, dr, dg, db);
      color_buffer.putPixel(x, y, color);
      if (d > 0) {
        x += inc_x;
        d += (2*(dx - dy));
      } else {
        d += 2*dx;
      }
    }
  }

  plotLow() {
    let dx = this.x1 - this.x0;
    let dy = this.y1 - this.y0;
    
    let inc_y = 1;
    
    if(dy < 0){
      inc_y = -1;
      dy = -dy;
    }
    
    let d = (2*dy) - dx;
    let y = this.y0;
    
    let color = this.color_0;
    let dr = (this.color_1[0] - this.color_0[0])/dx;
    let dg = (this.color_1[1] - this.color_0[1])/dx;
    let db = (this.color_1[2] - this.color_0[2])/dx;
    
    for(let x = this.x0; x <= this.x1; x++){
      color = this.colorInterpolation(color, dr, dg, db);
      color_buffer.putPixel(x, y, color);

      if(d > 0){
        y += inc_y;
        d += (2*(dy - dx));
      } else {
        d += 2*dy;
      }
    }
  }

  colorInterpolation(current_color, dr = 0, dg = 0, db = 0) {
    let color_result = current_color;

    color_result = [color_result[0] + dr, color_result[1] + dg, color_result[2] + db];

    return color_result;
  }
}

```

Para realizar a verificação dos octantes e desenhar as linhas de acordo o resultado encontrado, foi implementada a função **MidPointLineAlgorithm**, mostrada a seguir.

```js
function MidPointLineAlgorithm(x0, y0, x1, y1, color_0, color_1) {
  let line;
  const dy = Math.abs(y1 - y0);
  const dx = Math.abs(x1 - x0);
  if(dy < dx){
    if (x0 > x1) {
      line = new Line(x1, y1, x0, y0, color_1, color_0);
      line.plotLow();
    } else {
      line = new Line(x0, y0,x1, y1, color_0, color_1);
      line.plotLow();
    }
  } else {
    if (y0 > y1) {
      line = new Line(x1, y1, x0, y0, color_1, color_0);
      line.plotHigh();
    } else {
      line = new Line(x0, y0, x1, y1, color_0, color_1);
      line.plotHigh();
    }
  }
}
```

### Interpolação de cores

O processo de interpolação de cores consiste em começar a rasterizar uma linha com uma cor inicial e, gradativamente, transitar para uma segunda cor ao logo da linha, dando um efeito gradiente. Para reproduzir este efeito, foram realizados cálculos de interpolação linear, para definir a cor de cada pixel da reta.

Inicialmente calcula-se os valores de diferença de intensidade de cada canal do sistema rgb dividido pelo delta do eixo que está sendo levado em conta para a rasterização da linha. O valor de diferença de cada canal é incrementado ao seu correspondente na cor atual a cada iteração do loop de rasterização, fazendo com que cada pixel seja colorido de acordo com a progressão do gradiente. 

A implementação da interpolação foi dividida em duas partes, primeiro são realizados os cálculos dos valores de incremento de cada canal da seguinte forma:

```js
let dr = (this.color_1[0] - this.color_0[0])/dx;
let dg = (this.color_1[1] - this.color_0[1])/dx;
let db = (this.color_1[2] - this.color_0[2])/dx;
```

Sendo ``dr`` o valor de incremento do canal vermelho, ``dg`` correspondendo ao canal verde e ``db`` ao canal azul.

Dentro do laço de rasterização a cor do pixel é escolhida através da função ```colorInterpolation```, que toma como parâmetro a cor do pixel anterior e incrementa cada canal como já explicado. A função foi implementada como a seguir:

```js
colorInterpolation(current_color, dr = 0, dg = 0, db = 0) {
	let color_result = current_color;

	color_result = [color_result[0] + dr, color_result[1] + dg, color_result[2] + db];

	return color_result;
}
```

Tendo agora a possibilidade de rasterizar linhas em qualquer octante e com cores interpoladas, ao executar a função **MidPointLineAlgorithm** com os argumentos a seguir, obtem-se o resultado abaixo.

A execução

```js
MidPointLineAlgorithm(25, 30, 100,80, [255,0,0,255], [255,255,0,255]);
```
resulta em:

<img src="https://imgur.com/D7HQExH.png" alt="rasthered line" style="width:400px;"/>

### Triângulo
Para desenhar o triangulo definimos tres pontos e interligamos os mesmos através da função **DrawTriangle**. Que foi implementada na linguagem JavaScript, como a seguir:

```js
function DrawTriangle(x0, y0, x1, y1, x2, y2, color_0, color_1, color_2) {
	MidPointLineAlgorithm(x0, y0, x1, y1, color_0, color_1);
	MidPointLineAlgorithm(x1, y1, x2, y2, color_1, color_2);
	MidPointLineAlgorithm(x2, y2, x0, y0, color_2, color_0);
}
```
A seguir é exibida a chamada da função **DrawTriangle** com os seguintes parâmetros:

```js
DrawTriangle(25, 30, 50, 100, 100, 15, [255,0,0,255], [0,0,255,255], [0,255,0,255]);
```

Como resultado obtvemos a mostrada abaixo, onde é possivel notar a mudança gradual das cores no triangulo.
<img src="https://imgur.com/K2Rp809.png" alt="rasthered line" style="width:400px;"/>

### Referências

- [http://fleigfleig.blogspot.com/](http://fleigfleig.blogspot.com/)
- [https://cflavs.wordpress.com/2016/03/10/cgt1-poligonos-e-interpolacao-de-cores/](https://cflavs.wordpress.com/2016/03/10/cgt1-poligonos-e-interpolacao-de-cores/)
- [https://en.wikipedia.org/wiki/Bresenham's_line_algorithm](https://en.wikipedia.org/wiki/Bresenham%27s_line_algorithm)