---
title: "Análise de Personalidade de Clientes - Clusters"
author: "Gleynner Ghiotto"
output:
  html_document:
    df_print: paged
#    number_sections: true
    toc: true
#    toc_float: true
    toc_depth: 3
    theme: readable
    highlight: pygments
editor_options: 
  chunk_output_type: inline
---



<style> body {text-align: justify} </style>


```r
# Carregar pacotes utilizados nesta etapa:

library(tidyverse)
library(factoextra)
library(reshape2)
```





# Carregar dados

Carregar conjunto de dados trabalhado na etapa de pré-processamento e análise exploratória de dados (EDA).


```r
df_eda <- read.csv("df_eda_marketing_campaign.csv",
                  header = TRUE)
head(df_eda, n=10)
```

```
     ID Year_Birth    Education Marital_Status Income Kidhome Teenhome Dt_Customer Recency MntWines
1  5524       1957 Postgraduate          Alone  58138       0        0  2012-09-04      58      635
2  2174       1954 Postgraduate          Alone  46344       1        1  2014-03-08      38       11
3  4141       1965 Postgraduate      In couple  71613       0        0  2013-08-21      26      426
4  6182       1984 Postgraduate      In couple  26646       1        0  2014-02-10      26       11
5  5324       1981 Postgraduate      In couple  58293       1        0  2014-01-19      94      173
6  7446       1967 Postgraduate      In couple  62513       0        1  2013-09-09      16      520
7   965       1971 Postgraduate          Alone  55635       0        1  2012-11-13      34      235
8  6177       1985 Postgraduate      In couple  33454       1        0  2013-05-08      32       76
9  4855       1974 Postgraduate      In couple  30351       1        0  2013-06-06      19       14
10 5899       1950 Postgraduate      In couple   5648       1        1  2014-03-13      68       28
   MntFruits MntMeatProducts MntFishProducts MntSweetProducts MntGoldProds NumDealsPurchases
1         88             546             172               88           88                 3
2          1               6               2                1            6                 2
3         49             127             111               21           42                 1
4          4              20              10                3            5                 2
5         43             118              46               27           15                 5
6         42              98               0               42           14                 2
7         65             164              50               49           27                 4
8         10              56               3                1           23                 2
9          0              24               3                3            2                 1
10         0               6               1                1           13                 1
   NumWebPurchases NumCatalogPurchases NumStorePurchases NumWebVisitsMonth AcceptedCmp3 AcceptedCmp4
1                8                  10                 4                 7            0            0
2                1                   1                 2                 5            0            0
3                8                   2                10                 4            0            0
4                2                   0                 4                 6            0            0
5                5                   3                 6                 5            0            0
6                6                   4                10                 6            0            0
7                7                   3                 7                 6            0            0
8                4                   0                 4                 8            0            0
9                3                   0                 2                 9            0            0
10               1                   0                 0                20            1            0
   AcceptedCmp5 AcceptedCmp1 AcceptedCmp2 Complain Response Idade Education_dummy Marital_Status_dummy
1             0            0            0        0        1    67               1                    0
2             0            0            0        0        0    70               1                    0
3             0            0            0        0        0    59               1                    1
4             0            0            0        0        0    40               1                    1
5             0            0            0        0        0    43               1                    1
6             0            0            0        0        0    57               1                    1
7             0            0            0        0        0    53               1                    0
8             0            0            0        0        0    39               1                    1
9             0            0            0        0        1    50               1                    1
10            0            0            0        0        0    74               1                    1
   N_Children Dt_inscricao_ate_atual Gasto_total Numero_compras Num_campaign_acc Valor_medio_de_compra
1           1                   4415        1617             22                1                 73.50
2           3                   3865          27              4                0                  6.75
3           1                   4064         776             20                0                 38.80
4           2                   3891          53              6                0                  8.83
5           2                   3913         422             14                0                 30.14
6           2                   4045         716             20                0                 35.80
7           2                   4345         590             17                0                 34.71
8           2                   4169         169              8                0                 21.12
9           2                   4140          46              5                1                  9.20
10          3                   3860          49              1                1                 49.00
```


# Gerar clusters


A ideia principal da Análise de Cluster é a possibilidade de classificar os objetos em grupos, de forma que, dentro de um mesmo grupo, os objetos sejam o mais similares possível e, de forma análoga, que os diversos grupos (clusters) sejam o mais diferente possível em sua constituição. Como este tipo de aprendizado é não supervisionado, os dados não possuem rótulos e o algoritmo aprende as relações entre eles. 

O algoritmo K-means começa com a inicialização aleatória de k centroides. Para cada ponto do conjunto de dados, calcula-se a distância até os centroides e os pontos são atribuídos ao cluster cujo centroide está mais próximo dele. Após essa atribuição, a posição de cada centroide é recalculada por meio da média dos pontos pertencentes de cada cluster. O processo de atribuição de pontos e o recalculo dos centroides é repetido até que as posições dos centroides se estabilizem, ou seja, não ocorram mudanças significativas.



```r
nomes <- c("Income","Kidhome","MntFruits","MntMeatProducts","MntFishProducts","MntSweetProducts",
           "NumWebPurchases","NumCatalogPurchases","NumStorePurchases","NumWebVisitsMonth","Idade",
           "N_Children","Gasto_total","Numero_compras","Valor_medio_de_compra","Class_VMC")

df_cluster <- df_eda[,nomes[-16]] 
  
X1 <- df_cluster %>% scale()
```


```r
set.seed(1)
wcss = vector()
for (i in 1:10) {
  kmeans = kmeans(x = X1, centers = i)
  wcss[i] = sum(kmeans$withinss)
}

ggplot(data = data.frame(n = c(1:10),WCSS = wcss),aes(x = n, y=WCSS)) +
  geom_line(linewidth=0.7,linetype = 1,lineend	= "round",linejoin = "round",linemitre=1) + 
  geom_point(size = 2.5) +
  labs(x="Número de clusters (k)",y="WCSS", title="Método de Elbow") +
  scale_x_continuous(breaks = seq(0,10,2)) +
  theme_bw()
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-5-1.png" alt="plot of chunk unnamed-chunk-5"  />
<p class="caption">plot of chunk unnamed-chunk-5</p>
</div>

O método de Elbow, também conhecido como método do cotovelo, é um gráfico onde eixo x representa o número de clusters (k) e o eixo y representa a soma dos quadrados das distâncias de cada ponto ao centroide de seu respectivo cluster (WCSS, Within-Cluster Sum of Squares). O objetivo é encontrar um ponto no gráfico onde a diminuição da soma do WCSS se torna menos acentuada, formando um "cotovelo". Esse ponto indica um bom número de clusters, pois é onde adicionar mais clusters não traz uma redução significativa da compacidade dos clusters.

Para o exemplo em questão, utilizaremos 3 clusters, pois, a partir desse ponto, a redução do WCSS em função do número de clusters torna-se menos acentuada.


```r
set.seed(1)
kmeans = kmeans(x = X1, centers = 3)
previsoes = kmeans$cluster

X1 <- as.data.frame(X1)
```



```r
fviz_cluster(kmeans, X1,
             main = "Clusters - K-means",
             ggtheme = theme_minimal()) +
  geom_hline(aes(yintercept =0), linetype = 2) +
  geom_vline(aes(xintercept =0), linetype = 2) 
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-7-1.png" alt="plot of chunk unnamed-chunk-7"  />
<p class="caption">plot of chunk unnamed-chunk-7</p>
</div>



```r
data.frame(Cluster = previsoes,
           Valor_medio_de_compra = df_cluster$Valor_medio_de_compra,
           df_cluster %>% select(-"Valor_medio_de_compra"),
           check.names = F) %>% dplyr::group_by(Cluster) %>% 
  dplyr::summarise_all(list(mean)) %>% t() %>% as.data.frame() %>% 
  .[-1,] %>% round_df(2)
```

```
                            V1       V2       V3
Valor_medio_de_compra    75.60    42.09    14.94
Income                76649.76 58868.79 34845.03
Kidhome                   0.03     0.17     0.82
MntFruits                70.98    23.61     4.87
MntMeatProducts         478.07   136.89    24.30
MntFishProducts         104.07    31.94     6.73
MntSweetProducts         73.61    23.98     4.81
NumWebPurchases           5.16     6.27     2.22
NumCatalogPurchases       6.19     3.15     0.56
NumStorePurchases         8.35     7.87     3.25
NumWebVisitsMonth         2.86     5.41     6.52
Idade                    55.41    59.37    52.28
N_Children                1.24     1.99     2.30
Gasto_total            1431.58   734.33   103.27
Numero_compras           19.71    17.29     6.03
```

Por meio dos clusters formados, o grupo de número 1 é composto pelos clientes com o maior valor médio de compra, isto é, o grupo em que o gasto total dividido pelo número de compras é maior. Nesse grupo, os clientes têm maior renda média anual e tendem a possuir menor número de filhos. Nota-se também que este perfil de clientes possui maiores gastos com frutas, produtos cárneos, pesqueiros e doces. Ainda, eles têm preferência por realizar suas compras por meio da loja física e catálogo, sendo o tipo de pessoas que menos visita o site da empresa. 

O grupo 2 possui um perfil intermediário, possuindo nível de compra não tão alto quanto o grupo 1 e nem tão baixo quanto o grupo 3. Esse perfil de clientes são os que mais utilizam o web site da empresa para fazer suas compras. Já o grupo 3 possui um perfil mais conservador, sendo formado pelos clientes que possuem maior número de crianças em casa e é o grupo que mais visitou o web site da empresa no último mês (possivelmente em busca de promoções).

Abaixo segue uma representação da tabela acima, represantada por seus valores em escala, onde: os valores positivos represantam gastos acima da média; valores negativos representam gastos abaixo da média; e, valores iguais a zero, gastos iguais ao valor médio.


```r
cluster_summary <- data.frame(Cluster = previsoes,
                        rev(X1) %>% select(-"Valor_medio_de_compra"), 
                        Valor_medio_de_compra = df_cluster$Valor_medio_de_compra,
                        check.names = F) %>% dplyr::group_by(Cluster) %>% 
  dplyr::summarise_all(list(mean)) 
melt_cluster_summary <- melt(as.matrix(cluster_summary[,-1]))

ggplot(melt_cluster_summary, aes(Var2, Var1)) +
  scale_fill_continuous(type = "viridis", direction = -1) +
  geom_tile(aes(fill = value)) +
  geom_text(aes(label = round(value, 1))) +
  theme_bw() +
  ggtitle("Strength of Each of the Variables in the Clusters") +
  theme(plot.title = element_text(hjust = 0.5)) +
  labs(x="Variable", y="Cluster") +
  coord_flip()
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-9-1.png" alt="plot of chunk unnamed-chunk-9"  />
<p class="caption">plot of chunk unnamed-chunk-9</p>
</div>





