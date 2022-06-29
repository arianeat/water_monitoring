# Monitoramento da Superfície Hídrica de Reservatórios do Estado da Paraíba Utilizando *Deep Learning* e Imagens de Satélites

​		Este projeto consiste da criação de um modelo de rede neural convolucional para imagens multiespectrais com o objetivo de realizar o monitoramento de reservatórios hídricos localizados no estado da Paraíba através de imagens de satélites.



## Motivação

​	Secas de gravidade e duração variadas ocorrem com muita frequência no Nordeste do Brasil. Seus impactos afetam diretamente os setores da economia e a vida do povo nordestino. Na Paraíba, esse fenômeno provoca impactos tanto sociais quanto econômicos e afeta a vida de toda a população do Estado, principalmente na zona rural. Os prejuízos da falta de água na região atingem desde a segurança hídrica até a produção de alimentos e a criação de animais. Dada a pouca disponibilidade de água subterrânea e vazões fluviais, os reservatórios hídricos são a principal fonte de abastecimento de água na Paraíba. Portanto, o conhecimento do volume de água superficial dos reservatórios é necessário para uma gestão eficiente da água em termos de preparação para a seca e um melhor entendimento da hidrologia da região. 

​	O crescente número de sensores que orbitam o planeta Terra está produzindo produtos de imageamento da superfície terrestre com resoluções – tanto espacial quanto temporal – cada vez melhores. Isso, aliado à maior oferta de diferentes produtos de imageamento, está levando a um grande aumento do volume de dados de sensoriamento remoto disponíveis a quem queira extrair valiosas informações relacionadas à cobertura terrestre. 

​	Dessa forma, os dados de sensoriamento remoto, aliados com a aprendizagem profunda e a visão computacional, têm sido empregados para resolver problemas de grande complexidade e vêm gerando uma grande contribuição para a obtenção de métodos mais efetivos de controle ambiental. Diante desse cenário, este estudo propõe uma estrutura baseada em rede neural convolucional para reconhecer e monitorar a superfície de água dos reservatórios hídricos do estado da Paraíba através de imagens de satélite.



## Conjunto de Dados

​	O conjunto de dados de águas superficiais usado neste projeto pode ser baixado do [Zenodo](https://zenodo.org/record/5205674#.Yrcf4XbMLt8).

​	O conjunto de dados é formado por 95 imagens no formato TIFF coletadas do Sentinel-2  *Multispectral Instrument* (MSI) com correção atmosférica em todo o mundo. Ele é formado por vários tipos de corpos d'água e não d'água, o que suporta um aprendizado completo e de alta qualidade das características das águas superficiais. As imagens do conjunto são compostas por seis bandas ao total: quatro bandas de resolução de 10 m  (Blue, Green, Red, NIR)  e duas bandas de resolução de 20m que abrangem as partes do espectro visível ao infravermelho (SWIR). 

​	 As máscaras são basicamente etiquetas para cada pixel. Cada pixel recebe uma das duas categorias:

- Classe 1: Pixel pertencente a água.
- Classe 0: Pixel não pertencente a água.

​	Exemplo de imagem do conjunto de dados: 

![exemplo_dataset](https://github.com/arianeat/water_monitoring/blob/main/images/exemplo_dataset.png?raw=true)

​	As imagens do conjunto de dados apresentam tamanhos variados. Para o treinamento da rede foi utilizado imagens de tamanho 128x128, sendo assim foram realizadas operações nas imagens, como:

* Padding - para que todas as imagens fiquem no tamanho certo para serem cortadas
* Corte - para as imagens ficarem no tamanho 128x128;
* Flip vertical - dobrando o número de imagens para o treinamento.

​	Ao final do *data augmentation* foram formadas 7600 imagens no tamanho 128x128, que foram divididas entre treino, teste e validação.

## Modelo

​	A arquitetura escolhida para o modelo foi a U-Net. A U-Net é uma implementação particular de uma CNN que historicamente tem um bom desempenho em problemas de segmentação de imagens.

​	A UNET foi desenvolvida por [Olaf Ronneberger et al.](https://arxiv.org/abs/1505.04597) para segmentação de imagens biomédicas. A arquitetura contém dois caminhos. O primeiro caminho é o caminho de contração (também chamado de codificador) que é usado para capturar o contexto na imagem. O codificador é apenas uma pilha tradicional de camadas convolucionais e de pool máximo. O segundo caminho é o caminho de expansão simétrico (também chamado de decodificador) que é usado para permitir a localização precisa usando convoluções transpostas. Assim, é uma rede totalmente convolucional (FCN) de ponta a ponta, ou seja, contém apenas camadas convolucionais e não contém nenhuma camada densa, pela qual pode aceitar imagens de qualquer tamanho.

​	No artigo original, a UNET é descrita da seguinte forma:

![u_net](https://github.com/arianeat/water_monitoring/blob/main/images/u_net.png?raw=true)

​	Observe que no artigo original, o tamanho da imagem de entrada é 572x572x3, porém, usaremos a imagem de entrada de tamanho 128x128x6. 

​	Para a avaliação do modelo de segmentação semântica foi usada a métrica *Intersection-Over-Union* (IoU), também conhecido como Índice *Jaccard*. Ela é uma das métricas mais usadas na segmentação semântica. Mais informações sobre essa métrica pode ser encontrada [aqui](https://towardsdatascience.com/metrics-to-evaluate-your-semantic-segmentation-model-6bcb99639aa2). Também foi usada a Precision e Recall como métricas secundárias para determinar o quão bom ou ruim nosso modelo está classificando Falsos Positivos e Falsos Negativos.



## Resultados

​	Os gráficos a seguir mostram a performance do modelo após o treinamento de 50 épocas com *bathsize* de 32.

![metrics](https://github.com/arianeat/water_monitoring/blob/main/images/metrics.png?raw=true)

​	A imagem abaixo apresenta o desempenho da rede em algumas imagens presentes no conjunto de dados de teste.

![result_test](https://github.com/arianeat/water_monitoring/blob/main/images/result_test.png?raw=true)

   Utilizando o índice de *Jaccard* para avaliar a eficiência do modelo no conjunto de teste o resultado foi de 95,19% de precisão.

## Monitoramento da Superfície Hídrica dos Reservatórios

​	Para essa etapa do projeto foram escolhidos 4 reservatórios localizados em 4 bacias hidrográficas distintas do estado da Paraíba:

* Açude Engenheiro Avidos (Bacia Região do Alto Curso do Rio Piranhas)

* Açude Argemiro de Figueiredo (Acauã) (Região do Médio Curso do Rio Paraíba)
* Açude Lagoa do Arroz (Bacia Peixe)

* Açude Gramame-Mamuaba (Bacia Gramame)

​	As imagens foram obtidas através do Google Earth Engine (GEE). O GEE é uma plataforma de análise geoespacial baseada na nuvem, que permite aos usuários visualizar, analisar e baixar imagens de satélite do nosso planeta.

​	Para inferência no modelo de *deep learning* treinado, foram baixadas imagens no formato TIFF com as bandas B2, B3, B4, B8, B11 e B12 da missão Sentinel-2 L2A. Como as nuvens afetam consideravelmente o desempenho da rede, foram eliminadas do conjunto as imagens com grande presença de nuvens na superfície do reservatório.

​	

### Açude Engenheiro Avidos

​	Está localizado na antiga sede do município de São José de Piranhas, que ficou alagado e a cidade foi reconstruída um pouco ao sul. É o terceiro maior açude do estado da Paraíba, com capacidade para 293 milhões de metros cúbicos de água. A figura abaixo apresenta uma imagem do Açude Engenheiro Ávidos (esquerda) e uma imagem da máscara de água prevista pelo modelo de *deep learning* criado.

![](https://github.com/arianeat/water_monitoring/blob/main/images/avidos.png?raw=true)

​	Considerando que a área da superfície do reservatório hídrico é proporcional ao volume do mesmo, faremos uma comparação do gráfico do monitoramento superfície hídrica obtido a partir do modelo com os dados de volume do reservatório disponíveis no site da [Agência Executiva das Águas (AESA)](http://www.aesa.pb.gov.br/aesa-website/monitoramento/ultimos-volumes/). Na figura abaixo, a imagem da esquerda apresenta o resultado obtido pelo modelo e  na figura a direita os dados retirados do site da AESA para o mesmo reservatório no mesmo período de tempo. Podemos perceber que os dois gráficos apresentam um comportamento semelhante.

![](https://github.com/arianeat/water_monitoring/blob/main/images/result_avidos.png?raw=true)

​	

### Açude Argemiro de Figueiredo (Acauã)

   O Açude de Acauã fica localizado no município de Itatuba e é classificado como barragem de grande porte. Apresenta uma capacidade de armazenamento de cerca de 253 milhões de metros cúbicos de água potável. 

![acaua](https://github.com/arianeat/water_monitoring/blob/main/images/acaua.png?raw=true)

​	Na figura a seguir podemos verificar a relação do gráfico da superfície hídrica obtido a partir do modelo com o gráfico de volume do açude Acauã disponível no site da AESA.  

![result_acaua](https://github.com/arianeat/water_monitoring/blob/main/images/result_acaua.png?raw=true)



### Açude Lagoa do Arroz

 O açude Lagoa do Arroz é localizado no município de Cajazeiras, estado da Paraíba. Tem capacidade de armazenamento de 80 milhões de metros cúbicos de água.

![arroz](https://github.com/arianeat/water_monitoring/blob/main/images/arroz.png?raw=true)

​	A figura abaixo apresenta o resultado obtido pelo modelo (imagem direita) e  os dados retirados do site da AESA (imagem esquerda) para o açude Lagoa do Arroz  no mesmo período de tempo. 

![result_arroz](https://github.com/arianeat/water_monitoring/blob/main/images/result_arroz.png?raw=true)

### Açude Gramame-Mamuaba

​	O açude de Gramame-Mamuaba fica localizado na cidade de Conde e abastece João Pessoa e cidades da região metropolitana, com capacidade de armazenar cerca de 57 milhões de metros cúbicos de água. A figura a seguir apresenta uma imagem do Açude Gramame-Mamuaba (esquerda) e uma imagem da máscara de água prevista pelo modelo de *deep learning* criado.

![gramame](https://github.com/arianeat/water_monitoring/blob/main/images/gramame.png?raw=true)

​	A figura abaixo apresenta a comparação do gráfico gerado pelo modelo criado (esquerda) e o gráfico obtido como os dados presente na AESA (direita) para o açude Gramame/Mamuaba.

![result_gramame](https://github.com/arianeat/water_monitoring/blob/main/images/result_gramame.png?raw=true)

​	Dos quatro açudes apresentados neste trabalho, o açude Gramame/Mamuaba foi o que apresentou uma menor relação entre o gráfico gerado e os dados presentes na AESA. Isso pode ser explicado pela localização do açude, o litoral do estado da paraíba, onde tem uma maior presença de nuvens, o que dificulta a obtenção de imagens limpas (sem nuvens) para a inferência do modelo.



## Referências 

1. [An applicable and automatic method for earth surface water mapping based on multispectral images. International Journal of Applied Earth Observation and Geoinformation](https://www.sciencedirect.com/science/article/pii/S0303243421001793)

2. [Metrics to Evaluate your Semantic Segmentation Model](https://towardsdatascience.com/metrics-to-evaluate-your-semantic-segmentation-model-6bcb99639aa2)

3. [Monitoramento - Reservatórios Hídricos - AESA](http://www.aesa.pb.gov.br/aesa-website/monitoramento/ultimos-volumes/)

4. [Ronneberger et al. (2015)'s U-Net](https://arxiv.org/abs/1505.04597)

5. [Understanding Semantic Segmentation with UNET](https://towardsdatascience.com/understanding-semantic-segmentation-with-unet-6be4f42d4b47)
