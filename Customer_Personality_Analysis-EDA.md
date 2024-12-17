---
title: "Análise de Personalidade de Clientes - EDA"
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







# Atributos

- **Pessoas**:

**ID:** Identificador único do cliente    <br>
**Year_Birth:** Ano de nascimento do cliente   <br>
**Education:** Nível de instrução do cliente    <br>
**Marital_Status:** Estado civil do cliente    <br>
**Income:** Renda familiar anual do cliente    <br>
**Kidhome:** Número de crianças na residência do cliente    <br>
**Teenhome:** Número de adolescentes na casa do cliente    <br>
**Dt_Customer:** Data do cadastro do cliente na empresa    <br>
**Recency:** Quantidade de dias desde a última compra do cliente    <br>
**Complain:** 1 se o cliente reclamou nos últimos 2 anos, 0 caso contrário    <br>


- **Produtos**:

**MntWines:** valor gasto em produtos vitivinícolas nos últimos 2 anos    <br>
**MntFruits:** valor gasto com frutas nos últimos 2 anos    <br>
**MntMeatProducts:** Valor gasto com carnes nos últimos 2 anos    <br>
**MntFishProducts:** Valor gasto em produtos pesqueiros nos últimos 2 anos    <br>
**MntSweetProducts:** Valor gasto em produtos doces nos últimos 2 anos    <br>
**MntGoldProds:** Valor gasto em produtos de ouro nos últimos 2 anos    <br>


- **Promoção**:

**NumDealsPurchases:** Número de compras feitas com desconto    <br>
**AcceptedCmp1:** 1 se o cliente aceitou a oferta na 1ª campanha, 0 caso contrário    <br>
**AcceptedCmp2:** 1 se o cliente aceitou a oferta na 2ª campanha, 0 caso contrário    <br>
**AcceptedCmp3:** 1 se o cliente aceitou a oferta na 3ª campanha, 0 caso contrário    <br>
**AcceptedCmp4:** 1 se o cliente aceitou a oferta na 4ª campanha, 0 caso contrário    <br>
**AcceptedCmp5:** 1 se o cliente aceitou a oferta na 1ª campanha, 0 caso contrário    <br>
**Response:** 1 se o cliente aceitou a oferta na última campanha, 0 caso contrário    <br>


- **Local**:

**NumWebPurchases:** Número de compras feitas pelo site da empresa    <br>
**NumCatalogPurchases:** Número de compras feitas usando um catálogo    <br>
**NumStorePurchases:** Número de compras feitas diretamente nas lojas    <br>
**NumWebVisitsMonth:** Número de visitas ao site da empresa no último mês    <br>



# Pré-processamento

## Examinando o conjunto de dados


```r
dados <- read.csv("marketing_campaign.csv",
                  header = TRUE, sep = "\t")
head(dados, n=10)
```

```
     ID Year_Birth  Education Marital_Status Income Kidhome Teenhome Dt_Customer Recency MntWines
1  5524       1957 Graduation         Single  58138       0        0  04-09-2012      58      635
2  2174       1954 Graduation         Single  46344       1        1  08-03-2014      38       11
3  4141       1965 Graduation       Together  71613       0        0  21-08-2013      26      426
4  6182       1984 Graduation       Together  26646       1        0  10-02-2014      26       11
5  5324       1981        PhD        Married  58293       1        0  19-01-2014      94      173
6  7446       1967     Master       Together  62513       0        1  09-09-2013      16      520
7   965       1971 Graduation       Divorced  55635       0        1  13-11-2012      34      235
8  6177       1985        PhD        Married  33454       1        0  08-05-2013      32       76
9  4855       1974        PhD       Together  30351       1        0  06-06-2013      19       14
10 5899       1950        PhD       Together   5648       1        1  13-03-2014      68       28
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
   AcceptedCmp5 AcceptedCmp1 AcceptedCmp2 Complain Z_CostContact Z_Revenue Response
1             0            0            0        0             3        11        1
2             0            0            0        0             3        11        0
3             0            0            0        0             3        11        0
4             0            0            0        0             3        11        0
5             0            0            0        0             3        11        0
6             0            0            0        0             3        11        0
7             0            0            0        0             3        11        0
8             0            0            0        0             3        11        0
9             0            0            0        0             3        11        1
10            0            0            0        0             3        11        0
```


```r
cat("O Data Frame original possui", dim(dados)[1],"linhas e", 
    dim(dados)[2],"colunas")
```

```
O Data Frame original possui 2240 linhas e 29 colunas
```


```r
cat("Variáveis presentes no Data Frame: \n", colnames(dados), 
    "\n Total de variáveis:", length(colnames(dados)), sep = " \n ")
```

```
Variáveis presentes no Data Frame: 
 
 ID 
 Year_Birth 
 Education 
 Marital_Status 
 Income 
 Kidhome 
 Teenhome 
 Dt_Customer 
 Recency 
 MntWines 
 MntFruits 
 MntMeatProducts 
 MntFishProducts 
 MntSweetProducts 
 MntGoldProds 
 NumDealsPurchases 
 NumWebPurchases 
 NumCatalogPurchases 
 NumStorePurchases 
 NumWebVisitsMonth 
 AcceptedCmp3 
 AcceptedCmp4 
 AcceptedCmp5 
 AcceptedCmp1 
 AcceptedCmp2 
 Complain 
 Z_CostContact 
 Z_Revenue 
 Response 
 
 Total de variáveis: 
 29
```


## Detectando valores ausentes e valores distintos


```r
df_preprocess <- dados

func_my_summary(df_preprocess)
```

```
              Variavel      Tipo    N Missing Missing_Percent N_unique
1                   ID   integer 2240       0             0.0     2240
2           Year_Birth   integer 2240       0             0.0       59
3            Education character 2240       0             0.0        5
4       Marital_Status character 2240       0             0.0        8
5               Income   integer 2240      24             1.1     1975
6              Kidhome   integer 2240       0             0.0        3
7             Teenhome   integer 2240       0             0.0        3
8          Dt_Customer character 2240       0             0.0      663
9              Recency   integer 2240       0             0.0      100
10            MntWines   integer 2240       0             0.0      776
11           MntFruits   integer 2240       0             0.0      158
12     MntMeatProducts   integer 2240       0             0.0      558
13     MntFishProducts   integer 2240       0             0.0      182
14    MntSweetProducts   integer 2240       0             0.0      177
15        MntGoldProds   integer 2240       0             0.0      213
16   NumDealsPurchases   integer 2240       0             0.0       15
17     NumWebPurchases   integer 2240       0             0.0       15
18 NumCatalogPurchases   integer 2240       0             0.0       14
19   NumStorePurchases   integer 2240       0             0.0       14
20   NumWebVisitsMonth   integer 2240       0             0.0       16
21        AcceptedCmp3   integer 2240       0             0.0        2
22        AcceptedCmp4   integer 2240       0             0.0        2
23        AcceptedCmp5   integer 2240       0             0.0        2
24        AcceptedCmp1   integer 2240       0             0.0        2
25        AcceptedCmp2   integer 2240       0             0.0        2
26            Complain   integer 2240       0             0.0        2
27       Z_CostContact   integer 2240       0             0.0        1
28           Z_Revenue   integer 2240       0             0.0        1
29            Response   integer 2240       0             0.0        2
```


```r
cat("Índices dos valores ausentes da variável Income: \n",
    which(is.na(df_preprocess$Income)), 
    "\n\nTotal de NA's:", 
    sum(is.na(df_preprocess$Income)), sep = " " )
```

```
Índices dos valores ausentes da variável Income: 
 11 28 44 49 59 72 91 92 93 129 134 313 320 1380 1383 1384 1387 2060 2062 2079 2080 2082 2085 2229 

Total de NA's: 24
```



```r
# Eliminar valores NA da variável Income
df_preprocess <- df_preprocess %>% drop_na(Income)

cat("Quantidade de valores ausentes (NA) após eliminação:", sum(is.na(df_preprocess)))
```

```
Quantidade de valores ausentes (NA) após eliminação: 0
```


## Detectando valores duplicados


```r
cat("Quantidade de valores duplicados é:", sum(duplicated(df_preprocess)))
```

```
Quantidade de valores duplicados é: 0
```



## Eliminar colunas Z_CostContact e Z_Revenue (por possuir o mesmo valor em todas as observações)


```r
unique(df_preprocess$Z_CostContact) %>% length()
```

```
[1] 1
```

```r
unique(df_preprocess$Z_Revenue) %>% length()
```

```
[1] 1
```

```r
df_preprocess[,c("Z_CostContact","Z_Revenue")] <- NULL
```


# Análise Exploratória


## Estatísticas descritivas


```r
df_eda <- df_preprocess

my_summary_all(df_eda)
```

```
                ID Year_Birth    Income Kidhome Teenhome Recency MntWines MntFruits MntMeatProducts
Min           0.00    1893.00   1730.00    0.00     0.00    0.00     0.00      0.00            0.00
Q1         2814.75    1959.00  35303.00    0.00     0.00   24.00    24.00      2.00           16.00
Mediana    5458.50    1970.00  51381.50    0.00     0.00   49.00   174.50      8.00           68.00
Media      5588.35    1968.82  52247.25    0.44     0.51   49.01   305.09     26.36          167.00
Q3         8421.75    1977.00  68522.00    1.00     1.00   74.00   505.00     33.00          232.25
Max       11191.00    1996.00 666666.00    2.00     2.00   99.00  1493.00    199.00         1725.00
DevPad     3249.38      11.99  25173.08    0.54     0.54   28.95   337.33     39.79          224.28
N_missing     0.00       0.00      0.00    0.00     0.00    0.00     0.00      0.00            0.00
          MntFishProducts MntSweetProducts MntGoldProds NumDealsPurchases NumWebPurchases
Min                  0.00             0.00         0.00              0.00            0.00
Q1                   3.00             1.00         9.00              1.00            2.00
Mediana             12.00             8.00        24.50              2.00            4.00
Media               37.64            27.03        43.97              2.32            4.09
Q3                  50.00            33.00        56.00              3.00            6.00
Max                259.00           262.00       321.00             15.00           27.00
DevPad              54.75            41.07        51.82              1.92            2.74
N_missing            0.00             0.00         0.00              0.00            0.00
          NumCatalogPurchases NumStorePurchases NumWebVisitsMonth AcceptedCmp3 AcceptedCmp4 AcceptedCmp5
Min                      0.00              0.00              0.00         0.00         0.00         0.00
Q1                       0.00              3.00              3.00         0.00         0.00         0.00
Mediana                  2.00              5.00              6.00         0.00         0.00         0.00
Media                    2.67              5.80              5.32         0.07         0.07         0.07
Q3                       4.00              8.00              7.00         0.00         0.00         0.00
Max                     28.00             13.00             20.00         1.00         1.00         1.00
DevPad                   2.93              3.25              2.43         0.26         0.26         0.26
N_missing                0.00              0.00              0.00         0.00         0.00         0.00
          AcceptedCmp1 AcceptedCmp2 Complain Response
Min               0.00         0.00     0.00     0.00
Q1                0.00         0.00     0.00     0.00
Mediana           0.00         0.00     0.00     0.00
Media             0.06         0.01     0.01     0.15
Q3                0.00         0.00     0.00     0.00
Max               1.00         1.00     1.00     1.00
DevPad            0.24         0.12     0.10     0.36
N_missing         0.00         0.00     0.00     0.00
```


## Análises univariadas

### ID (Identificador único do cliente)


```r
summary(df_eda$ID)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
      0    2815    5458    5588    8422   11191 
```

```r
cat("\nQuantidade de valores distintos em ID é igual a", 
    length(unique(df_eda$ID)),"em um total de",dim(df_eda)[1],
    "linhas, isto é, os valores de ID são únicos.\n\nQuantidade de valores duplicados:",
    sum(duplicated(df_eda$ID)))
```

```

Quantidade de valores distintos em ID é igual a 2216 em um total de 2216 linhas, isto é, os valores de ID são únicos.

Quantidade de valores duplicados: 0
```


### Year_Birth (Ano de nascimento do cliente)


```r
summary(df_eda$Year_Birth)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   1893    1959    1970    1969    1977    1996 
```

```r
boxplot(df_eda$Year_Birth,horizontal = T)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-61-1.png" alt="plot of chunk unnamed-chunk-61"  />
<p class="caption">plot of chunk unnamed-chunk-61</p>
</div>

Criar coluna com a idade de cada cliente:


```r
# Calcular a idade do cliente
df_eda$Idade <- round(year(Sys.Date()) - df_eda$Year_Birth, digits = 2)

summary(df_eda$Idade)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  28.00   47.00   54.00   55.18   65.00  131.00 
```

```r
boxplot(df_eda$Idade,horizontal = T)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-62-1.png" alt="plot of chunk unnamed-chunk-62"  />
<p class="caption">plot of chunk unnamed-chunk-62</p>
</div>

```r
# Percebe-se clientes com idade muito elevada
```

Eliminar clientes com idade acima de 120 anos


```r
df_eda <- df_eda[df_eda$Idade < 120,]

summary(df_eda$Idade)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  28.00   47.00   54.00   55.08   65.00   84.00 
```

Verificar as 10 idades mais frequentes:


```r
# as dez idades mais frequentes
head(df_eda %>% count(Idade) %>% arrange(desc(n)),10)
```

```
   Idade  n
1     48 89
2     53 86
3     49 83
4     52 78
5     46 76
6     54 75
7     59 74
8     51 72
9     55 70
10    50 69
```

### Education (Nível de instrução do cliente)

Classificar o nível educacional nas classes Undergraduate e Postgraduate


```r
df_eda$Education[df_eda$Education %in% c("Basic","2n Cycle")] <- "Undergraduate"
df_eda$Education[df_eda$Education %in% c("Graduation","Master","PhD")] <- "Postgraduate"

cat("As categorias existentes em Education são:\n",
    unique(df_eda$Education),sep = "\n")
```

```
As categorias existentes em Education são:

Postgraduate
Undergraduate
```


Verificar o quantidade/percentual de clientes em cada classe:


```r
# Quantidade de Undergraduate e Postgraduate
n_educacao <- df_eda %>% count(Education) %>% 
  mutate(percentage= n/dim(df_eda)[1],
         percent_lab = paste0(round(percentage*100,2),"%")) 

ggplot(data = n_educacao) +
  geom_col(aes(x = Education, y = n), width =0.5) +
  labs(x = "Educação",y = "Quantidade") +
  theme_bw() +
  geom_text(aes(x = Education, y = n,label=percent_lab), vjust = -0.5) +
  scale_y_continuous(limits = c(0,2100), labels = scales::comma)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-66-1.png" alt="plot of chunk unnamed-chunk-66"  />
<p class="caption">plot of chunk unnamed-chunk-66</p>
</div>

Criar variável dummy para representar as classes Undergraduate (com 0) e Postgraduate (com 1):

```r
df_eda$Education_dummy <- ifelse(df_eda$Education == "Undergraduate", 0, 1)
```


### Marital_Status (Estado civil do cliente)


```r
unique(df_eda$Marital_Status)
```

```
[1] "Single"   "Together" "Married"  "Divorced" "Widow"    "Alone"    "Absurd"   "YOLO"    
```

Classificar estado civil em Alone e In Couple:


```r
# Classes da variável Marital_Status
df_eda$Marital_Status[df_eda$Marital_Status %in% c("Single","Divorced","Absurd","Widow","YOLO")] <- "Alone"
df_eda$Marital_Status[df_eda$Marital_Status %in% c("Married","Together")] <- "In couple"

cat("As classificações para o estado civil são: \n",
    unique(df_eda$Marital_Status)[1],
    unique(df_eda$Marital_Status)[2], sep = "\n")
```

```
As classificações para o estado civil são: 

Alone
In couple
```

Verificar a quantidade de clientes que vivem sozinhas e com companheiros:


```r
# Quantidade de Alone e In couple
n_marital <- df_eda %>% count(Marital_Status) %>% 
  mutate(percentage = n/dim(df_eda)[1],
          percent_lab = paste0(round(percentage*100,2),"%"))

ggplot(data = n_marital) +
  geom_col(aes(x = Marital_Status, y = n), width =0.5) +
  labs(x = "Estado civil",y = "Quantidade") +
  theme_bw() +
  geom_text(aes(x = Marital_Status, y = n,label=percent_lab), vjust = -0.5) +
  scale_y_continuous(limits = c(0,1500))
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-70-1.png" alt="plot of chunk unnamed-chunk-70"  />
<p class="caption">plot of chunk unnamed-chunk-70</p>
</div>


Criar variável dummy para representar as classes Alone (com 0) e In couple (com 1):

```r
df_eda$Marital_Status_dummy <- ifelse(df_eda$Marital_Status == "Alone", 0, 1)
```


### Income (Renda familiar anual do cliente)


```r
summary(df_eda$Income)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   1730   35246   51373   52237   68487  666666 
```

```r
par(mfrow = c(1,2))
boxplot(df_eda$Income, horizontal = T,xlab = "Renda anual")
hist(df_eda$Income,breaks = 100, main = NULL,xlab = "Renda anual",ylab=NULL)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-72-1.png" alt="plot of chunk unnamed-chunk-72"  />
<p class="caption">plot of chunk unnamed-chunk-72</p>
</div>

```r
# Outlier!!! 
```

Eliminar valor outlier!


```r
df_eda <- df_eda[df_eda$Income < 600000,]
```



```r
summary(df_eda$Income)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   1730   35234   51371   51959   68487  162397 
```

```r
par(mfrow = c(1,2))
boxplot(df_eda$Income, horizontal = T, xlab = "Renda anual")
hist(df_eda$Income,breaks = 100,xlab = "Renda anual", main = NULL,ylab=NULL)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-74-1.png" alt="plot of chunk unnamed-chunk-74"  />
<p class="caption">plot of chunk unnamed-chunk-74</p>
</div>


### Kidhome e Teenhome (Número de crianças e adolescentes na residência do cliente)


```r
unique(df_eda$Kidhome)
```

```
[1] 0 1 2
```

```r
unique(df_eda$Teenhome)
```

```
[1] 0 1 2
```


```r
percent_kidhome <- as.data.frame(prop.table(table(df_eda$Kidhome)))

g1 <- ggplot(data = percent_kidhome) +
  geom_col(aes(x = Var1, y = Freq), width =0.5) +
  labs(x="Nº de crianças",y = "Percentual", title = "kidhome") +
  scale_y_continuous(limits = c(0,1), labels = scales::percent) +
  geom_text(aes(x = Var1, y = Freq,label=paste0(round(Freq,2)*100,"%")), vjust = -0.5) +
  theme_bw()

percent_Teenhome <- as.data.frame(prop.table(table(df_eda$Teenhome)))
g2 <- ggplot(data = percent_Teenhome) +
  geom_col(aes(x = Var1, y = Freq), width =0.5) +
  labs(x="Nº de adolescentes",y = "Percentual", title = "Teenhome") +
  scale_y_continuous(limits = c(0,1), labels = scales::percent) +
  geom_text(aes(x = Var1, y = Freq,label=paste0(round(Freq,2)*100,"%")), vjust = -0.5) +
  theme_bw()

(g1+g2)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-76-1.png" alt="plot of chunk unnamed-chunk-76"  />
<p class="caption">plot of chunk unnamed-chunk-76</p>
</div>

Calcular o número de filhos por cliente e verificar o valor percentual de cada uma das quantidades: 


```r
df_eda$N_Children <- df_eda$Kidhome + df_eda$Teenhome 
percent_N_Children <- as.data.frame(prop.table(table(df_eda$N_Children)))

ggplot(data = percent_N_Children) +
  geom_col(aes(x = Var1, y = Freq), width =0.5) +
  labs(x="Nº de filhos",y = "Percentual") +
  scale_y_continuous(limits = c(0,1),labels = scales::percent_format(scale = 100,accuracy =5L)) +
  geom_text(aes(x = Var1, y = Freq,label=paste0(round(Freq,2)*100,"%")), vjust = -0.5) +
  theme_bw()
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-77-1.png" alt="plot of chunk unnamed-chunk-77"  />
<p class="caption">plot of chunk unnamed-chunk-77</p>
</div>


### Dt_Customer (Data do cadastro do cliente na empresa)


```r
df_eda$Dt_Customer <- as_date(dmy(df_eda$Dt_Customer))
class(df_eda$Dt_Customer)
```

```
[1] "Date"
```

```r
summary(df_eda$Dt_Customer)
```

```
        Min.      1st Qu.       Median         Mean      3rd Qu.         Max. 
"2012-07-30" "2013-01-16" "2013-07-08" "2013-07-10" "2013-12-31" "2014-06-29" 
```

Quantidade de dias desde a data da inscrição na empresa até o dia atual:


```r
df_eda$Dt_inscricao_ate_atual <- Sys.Date() - df_eda$Dt_Customer
df_eda$Dt_inscricao_ate_atual <- as.numeric(df_eda$Dt_inscricao_ate_atual)

summary(df_eda$Dt_inscricao_ate_atual)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   3823    4003    4179    4177    4352    4522 
```


### Recency (Quantidade de dias desde a última compra do cliente)


```r
summary(df_eda$Recency)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   0.00   24.00   49.00   49.02   74.00   99.00 
```

### MntWines, MntFruits, MntMeatProducts, MntFishProducts, MntSweetProducts e MntGoldProds


```r
summary(df_eda$MntWines)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    0.0    24.0   175.5   305.3   505.0  1493.0 
```

```r
summary(df_eda$MntFruits)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   0.00    2.00    8.00   26.33   33.00  199.00 
```

```r
summary(df_eda$MntMeatProducts)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    0.0    16.0    68.0   167.0   232.2  1725.0 
```

```r
summary(df_eda$MntFishProducts)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   0.00    3.00   12.00   37.65   50.00  259.00 
```

```r
summary(df_eda$MntSweetProducts)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   0.00    1.00    8.00   27.05   33.00  262.00 
```

```r
summary(df_eda$MntGoldProds)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   0.00    9.00   24.50   43.93   56.00  321.00 
```

Calcular a quantidade total gasto em produtos:


```r
df_eda$Gasto_total <- df_eda$MntWines + df_eda$MntFruits + df_eda$MntMeatProducts +
  df_eda$MntFishProducts + df_eda$MntSweetProducts + df_eda$MntGoldProds

summary(df_eda$Gasto_total)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    5.0    69.0   397.0   607.3  1048.0  2525.0 
```



```r
par(mfrow = c(1,2))
boxplot(df_eda$Gasto_total,horizontal = T,xlab ="Gasto total")
hist(df_eda$Gasto_total, breaks = 100,xlab ="Gasto total", main = NULL)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-83-1.png" alt="plot of chunk unnamed-chunk-83"  />
<p class="caption">plot of chunk unnamed-chunk-83</p>
</div>


### NumDealsPurchases (Número de compras feitas com desconto)


```r
summary(df_eda$NumDealsPurchases)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  0.000   1.000   2.000   2.325   3.000  15.000 
```

```r
par(mfrow = c(1,2))
boxplot(df_eda$NumDealsPurchases, horizontal = T,xlab ="Compras com desconto")
hist(df_eda$NumDealsPurchases, breaks = 100,xlab ="Compras com desconto", main = NULL)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-84-1.png" alt="plot of chunk unnamed-chunk-84"  />
<p class="caption">plot of chunk unnamed-chunk-84</p>
</div>

### NumWebPurchases, NumCatalogPurchases e NumStorePurchases

Número total de compras por cliente:


```r
df_eda$Numero_compras <- df_eda$NumWebPurchases + df_eda$NumCatalogPurchases + df_eda$NumStorePurchases

summary(df_eda$Numero_compras)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
   0.00    6.00   12.00   12.57   18.25   32.00 
```



```r
par(mfrow = c(1,2))
boxplot(df_eda$Numero_compras,xlab ="Nº de compras por cliente", horizontal = T)
hist(df_eda$Numero_compras, breaks = 100,xlab ="Nº de compras por cliente", main = NULL)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-86-1.png" alt="plot of chunk unnamed-chunk-86"  />
<p class="caption">plot of chunk unnamed-chunk-86</p>
</div>

Verificar a quantidade de compras feitas em cada local de venda:


```r
local <- c("Web","Catalogo","Store")
valor <- c(sum(df_eda$NumWebPurchases),
           sum(df_eda$NumCatalogPurchases),
           sum(df_eda$NumStorePurchases))
local_vendas <- data.frame(local,valor)
local_vendas$local <- factor(local_vendas$local, levels = c("Catalogo","Web","Store"))

ggplot(data = local_vendas) +
  geom_col(aes(x = reorder(local,-valor), y = valor), width = 0.5) +
  labs(x = "Local de compra",y = "Quantidade total comprada em cada local") +
  theme_bw() + geom_text(aes(x = local, y = valor,label=valor), vjust = -0.5) +
  scale_y_continuous(limits = c(0,14000))
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-87-1.png" alt="plot of chunk unnamed-chunk-87"  />
<p class="caption">plot of chunk unnamed-chunk-87</p>
</div>

### NumWebVisitsMonth (Número de visitas ao site da empresa no último mês)


```r
summary(df_eda$NumWebVisitsMonth)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  0.000   3.000   6.000   5.321   7.000  20.000 
```

### AcceptedCmp1, AcceptedCmp2, AcceptedCmp3, AcceptedCmp4, AcceptedCmp5 e Response


```r
unique(df_eda$AcceptedCmp1)
```

```
[1] 0 1
```

```r
unique(df_eda$AcceptedCmp2)
```

```
[1] 0 1
```

```r
unique(df_eda$AcceptedCmp3)
```

```
[1] 0 1
```

```r
unique(df_eda$AcceptedCmp4)
```

```
[1] 0 1
```

```r
unique(df_eda$AcceptedCmp5)
```

```
[1] 0 1
```

```r
unique(df_eda$Response)
```

```
[1] 1 0
```

Calcular o número de campanhas aceitas por cada cliente:


```r
df_eda$Num_campaign_acc <- df_eda$AcceptedCmp1 + df_eda$AcceptedCmp2 +
  df_eda$AcceptedCmp3 + df_eda$AcceptedCmp4 + df_eda$AcceptedCmp5 + df_eda$Response



n_campaign_acc <- df_eda %>% count(Num_campaign_acc) %>% mutate(perc_camp = n/sum(n),
                                                                 perc_lab = paste0(round(perc_camp*100,2),"%"))
n_campaign_acc$Num_campaign_acc <-  as.factor(n_campaign_acc$Num_campaign_acc)

ggplot(data = n_campaign_acc) +
  geom_col(aes(x = Num_campaign_acc, y = n), width = 0.5) +
  labs(x = "Campanhas aceitas",y = "Quantidade") +
  theme_bw() + geom_text(aes(x = Num_campaign_acc, y = n,label=perc_lab), vjust = -0.5) +
  scale_y_continuous(limits = c(0,2000))
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-90-1.png" alt="plot of chunk unnamed-chunk-90"  />
<p class="caption">plot of chunk unnamed-chunk-90</p>
</div>

### Complain (1 se o cliente reclamou nos últimos 2 anos, 0 caso contrário)


```r
unique(df_eda$Complain)
```

```
[1] 0 1
```

```r
cat("Foram feitas um total de",sum(df_eda$Complain),"reclamações.")
```

```
Foram feitas um total de 20 reclamações.
```


```r
n_complain <- df_eda %>% count(Complain)
n_complain$Complain <-  as.factor(n_complain$Complain)

ggplot(data = n_complain) +
  geom_col(aes(x = Complain, y = n), width = 0.5) +
  labs(x = "Reclamações",y = "Quantidade") +
  geom_text(aes(x = Complain, y = n,label=n), vjust = -0.5) +
  theme_bw()
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-92-1.png" alt="plot of chunk unnamed-chunk-92"  />
<p class="caption">plot of chunk unnamed-chunk-92</p>
</div>



## Calcular o valor médio por compra


```r
# Eliminas valor de compra igual a zero (6 linhas)
df_eda <- df_eda[df_eda$Numero_compras > 0,]  

df_eda$Valor_medio_de_compra <- round(df_eda$Gasto_total/df_eda$Numero_compras, 
                                       digits = 2)
```


## Análise Bivariadas

### Total de gasto por nível educacional


```r
Gst_niv_educ <- df_eda %>% group_by(Education) %>% summarise(Gasto_Edu = sum(Gasto_total)) %>% 
  mutate(perc_gasto = Gasto_Edu/sum(Gasto_Edu),
         perc_lab = paste0(round(perc_gasto*100,2),"%"))

ggplot(data = Gst_niv_educ) +
  geom_col(aes(x = Education,y = Gasto_Edu), width = 0.5) +
  labs(x="Nível educacional",y = "Valor/percentual de gasto") +
  theme_bw() + geom_text(aes(x = Education,y = Gasto_Edu,label=perc_lab), vjust = -0.5) +
  scale_y_continuous(limits = c(0,1300000), breaks = seq(0,1300000,250000),
                     labels = scales::comma)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-94-1.png" alt="plot of chunk unnamed-chunk-94"  />
<p class="caption">plot of chunk unnamed-chunk-94</p>
</div>


### Valor médio de compra por nível educacional


```r
ggplot(data = df_eda %>% group_by(Education) %>% 
  summarise(media = mean(Valor_medio_de_compra))) +
  geom_col(aes(x = Education, y = media),linewidth=1.8) +
  labs(x=NULL,y="Valor médio de compra") +
  geom_text(aes(x = Education,y = media,label= paste0(round(media,1))), vjust = -0.5) +
  theme_bw() 
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-95-1.png" alt="plot of chunk unnamed-chunk-95"  />
<p class="caption">plot of chunk unnamed-chunk-95</p>
</div>


### Total de gasto por estado civil


```r
Gst_est_civ <- df_eda %>% group_by(Marital_Status) %>% summarise(Gasto_Marital = sum(Gasto_total)) %>% 
  mutate(perc_gasto = Gasto_Marital/sum(Gasto_Marital),
         perc_lab = paste0(round(perc_gasto*100,2),"%"))

ggplot(data = Gst_est_civ) +
  geom_col(aes(x = Marital_Status,y = Gasto_Marital), width = 0.5) +
  labs(x="Estado civil",y = "Valor/percentual de gasto") +
  theme_bw()  + geom_text(aes(x = Marital_Status,y = Gasto_Marital,label=perc_lab), vjust = -0.5) +
  scale_y_continuous(limits = c(0,1000000), labels = scales::comma)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-96-1.png" alt="plot of chunk unnamed-chunk-96"  />
<p class="caption">plot of chunk unnamed-chunk-96</p>
</div>

### Valor médio de compra por estado civil


```r
ggplot(data = df_eda %>% group_by(Marital_Status) %>% 
  summarise(media = mean(Valor_medio_de_compra))) +
  geom_col(aes(x = Marital_Status, y = media),linewidth=1.8) +
  labs(x=NULL,y="Valor médio de compra") +
  geom_text(aes(x = Marital_Status,y = media,label= paste0(round(media,1))), vjust = -0.5) +
  theme_bw() 
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-97-1.png" alt="plot of chunk unnamed-chunk-97"  />
<p class="caption">plot of chunk unnamed-chunk-97</p>
</div>


### Total de gasto por número de filhos


```r
Gst_n_Children <- df_eda %>% group_by(N_Children) %>% summarise(Gasto_Child = sum(Gasto_total)) %>% 
  mutate(perc_gasto = Gasto_Child/sum(Gasto_Child),
         perc_lab = paste0(round(perc_gasto*100,2),"%"))
Gst_n_Children$N_Children <- factor(Gst_n_Children$N_Children)

ggplot(data = Gst_n_Children) +
  geom_col(aes(x = N_Children,y = Gasto_Child), width = 0.5) +
  labs(x = "Nº de filhos",y = "Valor/percentual de gasto") +
  theme_bw()  + geom_text(aes(x = N_Children,y = Gasto_Child,label=perc_lab), vjust = -0.5) +
  scale_y_continuous(limits = c(0,800000), labels = scales::comma)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-98-1.png" alt="plot of chunk unnamed-chunk-98"  />
<p class="caption">plot of chunk unnamed-chunk-98</p>
</div>


### Valor médio de compra por número de filhos


```r
ggplot(data = df_eda %>% group_by(N_Children) %>% 
  summarise(media = mean(Valor_medio_de_compra))) +
  geom_col(aes(x = N_Children, y = media),linewidth=1.8) +
  labs(x=NULL,y="Valor médio de compra") +
  geom_text(aes(x = N_Children,y = media,label= paste0(round(media,1))), vjust = -0.5) +
  theme_bw() 
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-99-1.png" alt="plot of chunk unnamed-chunk-99"  />
<p class="caption">plot of chunk unnamed-chunk-99</p>
</div>


### Total de gasto dado o número de campanhas aceitas


```r
# gasto pelo número de campanhas aceitas
Gst_campaign_acc <- df_eda %>% 
  group_by(Num_campaign_acc) %>% summarise(Gasto_camp_acc = sum(Gasto_total)) %>% 
  mutate(perc_gasto_camp = round((Gasto_camp_acc/sum(Gasto_camp_acc))*100,2),
         perc_lab = paste0(perc_gasto_camp,"%"))

ggplot(data = Gst_campaign_acc) +
  geom_col(aes(x = Num_campaign_acc,y = Gasto_camp_acc), width = 0.5) +
  labs(x = "Campanhas aceitas",y = "Valor/percentual de gasto") +
  theme_bw() + geom_text(aes(x = Num_campaign_acc,y = Gasto_camp_acc,label = perc_lab), vjust = -0.5) +
  scale_y_continuous(limits = c(0,1000000), breaks = c(0,200000,400000,600000,800000,1000000),
                     labels = scales::comma) +
  scale_x_continuous(limits = c(-0.5,5.5), breaks = seq(0,5,1))
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-100-1.png" alt="plot of chunk unnamed-chunk-100"  />
<p class="caption">plot of chunk unnamed-chunk-100</p>
</div>


### Valor médio de compra dado o número de campanhas aceitas


```r
ggplot(data = df_eda %>% group_by(Num_campaign_acc) %>% 
  summarise(media = mean(Valor_medio_de_compra))) +
  geom_col(aes(x = Num_campaign_acc, y = media),size=1.8) +
  labs(x="Campanhas aceitas",y="Valor médio de compra") +
  geom_text(aes(x = Num_campaign_acc,y = media,label= paste0(round(media,1))), vjust = -0.5) +
  scale_x_continuous(limits = c(-0.5,5.5), breaks = seq(0,5,1)) +
  theme_bw() 
```

```
Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
ℹ Please use `linewidth` instead.
This warning is displayed once every 8 hours.
Call `lifecycle::last_lifecycle_warnings()` to see where this warning was generated.
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-101-1.png" alt="plot of chunk unnamed-chunk-101"  />
<p class="caption">plot of chunk unnamed-chunk-101</p>
</div>


### Quantidade de observações por classes 


```r
df_eda %>% group_by(Education) %>% count()
```

```
# A tibble: 2 × 2
# Groups:   Education [2]
  Education         n
  <chr>         <int>
1 Postgraduate   1954
2 Undergraduate   252
```

```r
df_eda %>% group_by(Marital_Status) %>% count()
```

```
# A tibble: 2 × 2
# Groups:   Marital_Status [2]
  Marital_Status     n
  <chr>          <int>
1 Alone            780
2 In couple       1426
```

```r
df_eda %>% group_by(N_Children) %>% count()
```

```
# A tibble: 4 × 2
# Groups:   N_Children [4]
  N_Children     n
       <int> <int>
1          0   628
2          1  1113
3          2   415
4          3    50
```


### Dispersão do valor médio de compra pela renda anual do cliente

- Disciminando por Nível Educacional


```r
ggplot(data = df_eda) +
  geom_point(aes(x = Income, y = Valor_medio_de_compra, color = Education),alpha=0.5,size=1.8) +
  labs(x="Renda anual",y="Valor médio de compra") +
  theme_bw() +
  scale_colour_manual("Nível educacional", values = c("darkorchid","deepskyblue1"))
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-103-1.png" alt="plot of chunk unnamed-chunk-103"  />
<p class="caption">plot of chunk unnamed-chunk-103</p>
</div>

- Disciminando por quantidade de filhos


```r
df_eda$N_Children <- factor(df_eda$N_Children)

ggplot(data = df_eda) +
  geom_point(aes(x = Income, y = Valor_medio_de_compra, color = N_Children),alpha=0.5,size=1.8) +
  labs(x="Renda anual",y="Valor médio por compra") +
  theme_bw() +
  scale_colour_manual("Nº de filhos", values = c("deepskyblue1","antiquewhite3","forestgreen","darkorchid"))
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-104-1.png" alt="plot of chunk unnamed-chunk-104"  />
<p class="caption">plot of chunk unnamed-chunk-104</p>
</div>


### Dispersão do Gasto total em compras pela renda anual do cliente

- Disciminando por quantidade de filhos


```r
ggplot(data = df_eda) +
  geom_point(aes(x = Income, y =  Gasto_total, color = N_Children),alpha=0.5,size=1.8) +
  labs(x="Renda anual",y="Gasto total") +
  theme_bw() +
  scale_colour_manual("Nº de filhos", values = c("deepskyblue1","antiquewhite3","forestgreen","darkorchid"))
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-105-1.png" alt="plot of chunk unnamed-chunk-105"  />
<p class="caption">plot of chunk unnamed-chunk-105</p>
</div>

## Correlação entre variáveis 


```r
df_eda$N_Children <- as.integer(df_eda$N_Children)

df_cor <- df_eda[,sapply(df_eda, mode) == "numeric"] %>% 
  select(-c("ID","Dt_Customer","Recency","Complain",
            "AcceptedCmp1","AcceptedCmp2","AcceptedCmp3",
            "AcceptedCmp4","AcceptedCmp5","NumDealsPurchases")) %>% scale()

correlacao <- cor(df_cor)
corrplot(correlacao, type = "upper", order = "hclust", 
         tl.col = "black", tl.srt = 45, tl.cex = 0.7)
```

<div class="figure" style="text-align: center">
<img src="figure/unnamed-chunk-106-1.png" alt="plot of chunk unnamed-chunk-106"  />
<p class="caption">plot of chunk unnamed-chunk-106</p>
</div>


