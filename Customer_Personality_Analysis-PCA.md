---
title: "Análise de Personalidade de Clientes - PCA"
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
library(tidyverse)
library(factoextra)
```


# Carregar dados

Carregar conjunto de dados trabalhado na etapa anterior, isto é, no arquivo em que foi feito o pré-processamento e análise exploratória de dados (EDA).


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



# Aplicação de Componentes Principais

A Análise de Componentes Principais (PCA, sigla em inglês) é uma técnica estatística multivariada amplamente utilizada para a redução de dimensionalidade e extração de informações em conjuntos de dados de alta dimensionalidade, desenvolvida por Karl Pearson em 1901.

O PCA transforma um conjunto de variáveis correlacionadas em um conjunto de variáveis não correlacionadas, chamadas de componentes principais. Esses componentes são combinações lineares das variáveis originais e são ordenados de acordo com a quantidade de variação que explicam nos dados. Desta forma, o primeiro componente principal captura a maior parte da variação, o segundo componente principal captura a segunda maior parte, e assim por diante.

Além da redução de dimensionalidade, a PCA também é útil para identificar padrões, detectar outliers, pré-processar dados antes de aplicar outros métodos estatísticos e lidar com multicolinearidade, que ocorre quando as variáveis independentes estão altamente correlacionadas.



```r
df_pca <- df_eda[,sapply(df_eda, mode) == "numeric"] 
df_pca$Class_VMC <- ifelse(df_pca$Valor_medio_de_compra >= 49.42, "Melhores clientes", 
                                    ifelse(df_pca$Valor_medio_de_compra < 49.42 & 
                                             df_pca$Valor_medio_de_compra >= 29.92,
                                           "Intermediarios", "Clientes ruins"))

df_pca$Class_VMC <- factor(df_pca$Class_VMC, levels = c("Melhores clientes","Intermediarios", "Clientes ruins"))

df_pca_select <- df_pca %>% 
  select(-c("ID","Year_Birth","Dt_inscricao_ate_atual","Recency","Complain","AcceptedCmp1",
   "AcceptedCmp2","AcceptedCmp3","AcceptedCmp4","AcceptedCmp5","Response","NumDealsPurchases","Teenhome",
   "Num_campaign_acc","MntGoldProds","MntWines","Education_dummy","Marital_Status_dummy"))
# "MntFruits","MntFishProducts","MntSweetProducts","NumStorePurchases","Idade"
#nomes <- colnames(df_pca_select)
```



```r
pca_out <- prcomp(df_pca_select %>% select(-"Class_VMC"), scale=TRUE)
summary(pca_out)
```

```
Importance of components:
                          PC1     PC2     PC3     PC4    PC5     PC6     PC7     PC8    PC9    PC10
Standard deviation     2.8400 1.18079 1.02650 0.96237 0.8608 0.81359 0.66112 0.64201 0.6209 0.58548
Proportion of Variance 0.5377 0.09295 0.07025 0.06174 0.0494 0.04413 0.02914 0.02748 0.0257 0.02285
Cumulative Proportion  0.5377 0.63067 0.70091 0.76266 0.8121 0.85619 0.88532 0.91280 0.9385 0.96136
                          PC11    PC12    PC13    PC14      PC15
Standard deviation     0.49180 0.40640 0.37930 0.16958 1.242e-15
Proportion of Variance 0.01612 0.01101 0.00959 0.00192 0.000e+00
Cumulative Proportion  0.97748 0.98849 0.99808 1.00000 1.000e+00
```



```r
fviz_pca_biplot(pca_out, repel = F,
                col.var = "black", # cor das variáveis
                col.ind = as.factor(df_pca_select$Class_VMC), 
                alpha.ind = 0.9,
                pointsize = 1,
                addEllipses = F,
                palette  = c('royalblue3','chartreuse4','darkgoldenrod1'),
                legend.title = "Classes de MPG",
                title  = "Biplot",
                ggtheme = theme_bw()
) + scale_shape_manual(values=c(19,19,19))
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-5-1.png" alt="plot of chunk unnamed-chunk-5"  />
<p class="caption">plot of chunk unnamed-chunk-5</p>
</div>

```r
fviz_pca_var(pca_out,
             col.var = "contrib", # Cor por contribuições para o PC
             gradient.cols = c("#00AFBB", "#E7B800", "#FC4E07"),
             repel = F,
             ggtheme = theme_bw(),
             legend.title = "Cont.Var")
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-5-2.png" alt="plot of chunk unnamed-chunk-5"  />
<p class="caption">plot of chunk unnamed-chunk-5</p>
</div>

Conforme explicitado na imagem acima, o 3° quadrante é composto pelos clientes que possuem maiores valores médios de compra (marcados no biplot com a cor azul). Conforme este quadrante, os clientes com maiores valor médio de compra, foram mais impactados por possuirem gastos elevados com produtos cárneos, doces, peixes e frutas.

O 2° quadrante apresenta uma mistura entre clientes bons e intermediários. Na região de maior concentração de pontos azuis, representando os bons clientes (que gastam mais), observa-se que eles são impactados por possuirem maior renda anual (income) e compram mais por meio do uso do catálogo. Todavia, os clientes que tendem a comprar maior quantidade de produtos costumam frequentar a loja física, porém, isso não significa que são os que gastão mais. Observa-se ainda que, a medida em que aumenta a concentração de pontos verdes (clientes intermediários) nesse quadrante, tais clientes tendem a comprar mais usando o site da empresa, e clientes com idade mais avançadas tendem a diminuir o valor médio por compra.

No 1° e 4° quadrantes, os clientes com menores médias de compra são clientes com maior número de crianças/filhos, e esses clientes tendem a fazer mais visitas ao website da empresa. 


- Variáveis por quadrante:


```r
# The rotation matrix provides the principal component loadings;
loadings <- pca_out$rotation %>% as.data.frame()
loadings <- loadings[,c("PC1","PC2")]

loadings$Quadrante <-
  ifelse(
    loadings$PC1 >= 0 &
      loadings$PC2 >= 0,
    1,
    ifelse(
      loadings$PC1 <= 0 &
        loadings$PC2 >= 0,
      2,
      ifelse(
        loadings$PC1 <= 0 &
          loadings$PC2 <= 0,
        3,
        4
      )
    )
  )


rbind(loadings[loadings$Quadrante == "1",] %>% arrange(desc(PC1)),
      loadings[loadings$Quadrante == "2",] %>% arrange(PC1),
      loadings[loadings$Quadrante == "3",] %>% arrange(PC1),
      loadings[loadings$Quadrante == "4",] %>% arrange(desc(PC1))
)
```

```
                              PC1          PC2 Quadrante
NumWebVisitsMonth      0.22518135  0.256222249         1
N_Children             0.21005988  0.364084964         1
Gasto_total           -0.33162087  0.018881291         2
Numero_compras        -0.31053349  0.323520871         2
Income                -0.30226472  0.091737788         2
NumCatalogPurchases   -0.28937038  0.004430166         2
NumStorePurchases     -0.26273611  0.264451933         2
NumWebPurchases       -0.19446352  0.531298093         2
Idade                 -0.05427458  0.459058583         2
Valor_medio_de_compra -0.29558648 -0.087358300         3
MntMeatProducts       -0.29222121 -0.178127465         3
MntFishProducts       -0.25291077 -0.185636614         3
MntSweetProducts      -0.24459383 -0.143076426         3
MntFruits             -0.24396656 -0.175760465         3
Kidhome                0.24041473 -0.014244221         4
```



```r
cor(pca_out$x[,1:2], df_pca_select %>% select(-"Class_VMC") %>% scale()) %>% t()
```

```
                             PC1          PC2
Income                -0.8584401  0.108322648
Kidhome                0.6827845 -0.016819369
MntFruits             -0.6928717 -0.207535406
MntMeatProducts       -0.8299163 -0.210330326
MntFishProducts       -0.7182736 -0.219197019
MntSweetProducts      -0.6946532 -0.168942567
NumWebPurchases       -0.5522818  0.627349078
NumCatalogPurchases   -0.8218198  0.005231075
NumStorePurchases     -0.7461778  0.312261004
NumWebVisitsMonth      0.6395212  0.302543513
Idade                 -0.1541413  0.542049713
N_Children             0.5965758  0.429906242
Gasto_total           -0.9418124  0.022294755
Numero_compras        -0.8819237  0.382008750
Valor_medio_de_compra -0.8394737 -0.103151413
```


