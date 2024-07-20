## FÓRMULAS DAX ## 
Fórmulas utilizadas no seguinte projeto:

dCalendario = 
    VAR varDataMinFato = MIN(Realizado[Período])
    VAR varDataMaxFato = MAX(Realizado[Período])
    VAR varMesMin = MONTH(varDataMinFato)
    VAR varMesMax = MONTH(varDataMaxFato)
    VAR varAnoMin = YEAR(varDataMinFato)
    VAR varAnoMax = YEAR(varDataMaxFato)
    VAR varDataMin = DATE(varAnoMin,varMesMin,01)
    VAR varDataMax = DATE(varAnoMax,varMesMax,31)
RETURN
CALENDAR(varDataMin,varDataMax)
```

### 2.0 - MÉTRICAS FINANCEIRAS ###
#### 2.1 - TOTAL ORÇADO ####
Soma de todos os valores presentes nas Tabelas fOrçado
```py
Total Orçado = SUM('Orçado'[Valor])
```
#### 2.2 - TOTAL REALIZADO ####
Soma de todos os valores presentes nas Tabelas fRealizado
```py
Total Realizado = SUM(Realizado[Valor Real])
```
#### 2.3 - SUBTOTAL ORÇADO ####
Os valores dos Subtotais não estão presentes em nenhuma tabela, então <b>é necessário encontrar os valores e fazer o cálculo manualmente.</b>
```py
Subtotal Orçado = 
IF(COUNTROWS('Formato DRE')=1,
CALCULATE('Metricas'[Total Orçado],
ALL('Formato DRE'),
'Formato DRE'[Cod Formato] <= VALUES('Formato DRE'[Cod Formato])
))
```
#### 2.4 - SUBTOTAL REALIZADO ####
Os valores dos Subtotais não estão presentes em nenhuma tabela, então <b>é necessário encontrar os valores e fazer o cálculo manualmente.</b>
```py
Subtotal Realizado = 
IF(COUNTROWS('Formato DRE')=1,
CALCULATE('Metricas'[Total Realizado],
ALL('Formato DRE'),
'Formato DRE'[Cod Formato] <= VALUES('Formato DRE'[Cod Formato])
))
```
#### 2.5 - ORÇADO ####

```py
Orçado = 
IF
    (AND
    (ISFILTERED('Plano de Contas'[Classificação 2])=TRUE(),
    (MAX('Formato DRE'[Subtotal?]))=1),
BLANK(),
SWITCH(TRUE(),
    SELECTEDVALUE('Formato DRE'[Subtotal?])=0, Metricas[Total Orçado],
    SELECTEDVALUE('Formato DRE'[Subtotal?])=1, Metricas[Subtotal Orçado]
))
```
#### 2.6 - REALIZADO ####

```py
Realizado = 
IF
    (AND
    (ISFILTERED('Plano de Contas'[Classificação 2])=TRUE(),
    (MAX('Formato DRE'[Subtotal?]))=1),
BLANK(),
SWITCH(TRUE(),
    SELECTEDVALUE('Formato DRE'[Subtotal?])=0, Metricas[Total Realizado],
    SELECTEDVALUE('Formato DRE'[Subtotal?])=1, Metricas[Subtotal Realizado]
))
```
#### 2.7 - DESVIO DO ORÇADO x REALIZADO ####
Para descobrir o valor do Desvio, basta diminuir o <b>Valor Realizado</b> pelo <b>Valor Orçado</b>.
```py
Desvio = [Realizado]-Metricas[Orçado]
```
#### 2.8 - DESVIO EM PORCENTAGEM ####
Já para calcular a porcentagem, basta dividir o <b>Valor do Desvio</b> pelo <b>Valor <u>Absluto</u> do Orçado</b>.
```py
Desvio % = DIVIDE([Desvio], ABS([Orçado]))
```
