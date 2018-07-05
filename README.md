# Tecnologias e Aplicações

## Segmentação de estradas em imagens do Sentinel-2

### Problemática

Este projeto tem como principal objetivo a obtenção de um mapa de estradas a partir do processamento de um conjunto de imagens provenientes do satélite Sentinel-2, utilizando para isso técnicas de processamento de imagem com a ajuda da ferramenta **OpenCV** no **Python**.

A segmentação de estradas é um problema complexo, que neste caso necessita da utilização de técnicas que envolvem *deep learning* para resolver o problema.

### Processo de Implementação

Utilizando a linguagem **Python**, foi implementada uma técnica baseada no algoritmo de *Canny*.
A técnica passa pelo uso da segmentação de *Canny* para detetar estradas, pois este algoritmo é especialmente eficaz na deteção de zonas com elevada frequência. Dado que as intensidades das estradas são diferentes do meio que as rodeia, esta variação deve ser detetada pelo algoritmo de *Canny*.

De forma a simplificar a avaliação dos resultados optamos por dividir em secções a imagem original tirada pelo satélite e como tal, iremos processar cada uma delas igualmente no entanto só analisaremos uma dessas secções.

De forma a determinar que imagens usar, utilizamos o **QGIS** para fazer a análise das mesmas. Optamos por utilizar então a imagem **TCI**.

A aplicação, *codigo.py*, faz a leitura da imagem adequada da pasta *R10m* e determina, mediante os argumentos passados, que técnica utilizar para a obtenção de melhores resultados.

#### Funções e API's utilizadas

Para poder aplicar os algoritmos necessários, recorremos à API *gdal*, que é utilizada para ler os ficheiros com a extensão *.jp2*. Para além dessa, utilizamos também as bibliotecas *numpy*, *cv2* e *matplotlib*.

As função principal do programa é a *segment_roads*, que divide a imagem em partes mais pequenas e itera por cada uma aplicando o algoritmo de segmentação.

A função *process_canny* e *process_morphology* processam a imagem recebida de forma a produzir uma imagem com a segmentação efetuada, mas com distintos modos de funcionamento.

#### Implementação do algoritmo de Canny

O problema que surge ao aplicar o algoritmo de *Canny* provém da possibilidade de deteção de bordas noutras partes da imagem que tenham variações bruscas de intensidades (altas frequências). Devido a uma ligeira sensibilidade do algoritmo a zonas de alta frequência, em que por exemplo, as cores das casas sejam muito díspares ou na existência de nuvens que possam ser detetadas como sendo estradas, o que resulta numa imagem que deteta mais coisas do que devia, ou seja deteta mais do que as estadas. 

Inicialmente fizemos uma equalização do histograma para melhor ditribuir as diferentes intensidades. No entanto, o resultado obtido tinha mais "estradas" do que era suposto, isto porque detetou demasiadas coisas que não estradas.

<p>
  <img src="imagens/resultado1.png" width="700" />
</p>

De seguida experimentamos fazer uma *gamma correction* para tentar melhorar o contraste da imagem. O resultado obtido foi de facto melhor do que a primeira tentativa no entanto ainda se notava que existiam demasiadas deteções de objetos que não eram estradas.

Por fim utilizamos uma técnica de deteção de pontos claros na imagem. Esta função, quando aplicada, remove as partes mais claras da imagem. Como de facto, as estradas não apresentam intensidades próximas das apresentadas nas nuvens, em princípio não ocorrerá a eliminação de estradas no resultado final.

Depois utilizarmos a técnica de *gamma correction*, a imagem resultante foi esta:

<p>
  <img src="imagens/resultado3.png" width="700" />
</p>

Analisando a imagem resultante, denotamos que o problema nas deteções, em grande parte, continua a ser nas casas que têm muito contraste com o meio envolvente.

Numa tentativa final de melhorar os resultados diminuindo as frequências gerais da imagem, foi passado um filtro passa-baixo (gaussiano) com o *kernel* de 5 por 5. Este filtro diminui a variação brusca das intensidades em píxeis adjacentes, logo será de esperar que o resultado consista numa menor frequência geral e, assim, numa menor deteção de bordas.

As imagens seguintes correspondem às imagens anteriores com a única alteração sendo a passagem do resultado, antes do algoritmo de *Canny* ser aplicado, de um filtro passa-baixo:

<p>
  <img src="imagens/resultado4.png" width="700" />
</p>

<p>
  <img src="imagens/resultado2.png" width="700" />
</p>

A última imagem sofreu uma perda considerável das estradas porque o filtro passa-baixo fez um *blur* demasiado elevado na imagem original,resultando numa borda demasiado suave para ser detetada.

### Conclusão

Embora as operações usadas sejam capazes de detetar estradas, por vezes detetam demasiados objetos indesejáveis. Consegue-se concluir que o algoritmo funciona melhor nos casos em que há menos casas e outros tipos de edifícios, ou seja quando estamos perante ambientes mais rurais, como se pode ver nos exemplos mostrados.

Podemos concluir que os melhores resultados foram obtidos através da aplicação da *gamma correction* e da deteção de pontos de elevada intensidade, sendo que em certos casos os resultados são melhorados aplicando um filtro passa-baixo antes de aplicar o algoritmo de *Canny*.

Dado o reduzido tempo de prática com este tipo de técnicas e programas, reconhecemos que de facto existem múltiplos problemas com a nossa implementação assim como a comparação de resultados, no entanto consideramos que tendo em conta todos os fatores conseguimos de facto implementar uma solução capaz de fazer em parte o que foi pedido no enunciado.


#### José Cunha - A74702
#### Pedro Fonseca - A74166
