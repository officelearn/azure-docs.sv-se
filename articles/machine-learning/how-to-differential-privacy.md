---
title: Bevara data sekretessen med WhiteNoise-paketen
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder metod tips för differentiell integritet för att Azure Machine Learning modeller med WhiteNoise-paketen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 05/17/2020
ms.openlocfilehash: b43fd25e77e07b7b64416d8784d47c14fed48ea7
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "84981854"
---
# <a name="use-differential-privacy-in-azure-machine-learning"></a>Använd differentiell sekretess i Azure Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du använder metod tips för differentiell integritet för att Azure Machine Learning modeller med WhiteNoise python-paket.

Differentiell integritet är den guld standard definitionen av sekretess. System som följer denna definition av sekretess ger starka garantier mot en rad olika data återuppbyggnads-och återidentifierings attacker, inklusive attacker från angripare som har hjälp information. Läs mer om hur [differentiell integritet fungerar](./concept-differential-privacy.md).

> [!NOTE]
> Observera att vi byter namn på Toolkit och kommer att introducera det nya namnet under de kommande veckorna. 

## <a name="prerequisites"></a>Krav

- Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.
- [Python 3](https://www.python.org/downloads/)

## <a name="install-whitenoise-packages"></a>Installera WhiteNoise-paket

### <a name="standalone-installation"></a>Fristående installation

Biblioteken är utformade för att fungera från distribuerade Spark-kluster och kan installeras precis som andra paket.

Anvisningarna nedan förutsätter att dina `python` och- `pip` kommandon är mappade till `python3` och `pip3` .

Använd pip för att installera [WhiteNoise python-paket](https://pypi.org/project/opendp-whitenoise/).

`pip install opendp-whitenoise`

Kontrol lera att paketen har installerats genom att starta en python-prompt och skriva:

```python
import opendp.whitenoise.core
import opendp.whitenoise.sql
```

Om importen lyckas installeras biblioteken och är redo att användas.

### <a name="docker-image"></a>Docker-avbildning

Du kan också använda WhiteNoise-paket med Docker.

Hämta `opendp/whitenoise` avbildningen för att använda biblioteken i en Docker-behållare som innehåller Spark, Jupyter och exempel kod.

```sh
docker pull opendp/whitenoise:privacy
```

När du har hämtat avbildningen startar du Jupyter-servern:

```sh
docker run --rm -p 8989:8989 --name whitenoise-run opendp/whitenoise:privacy
```

Detta startar en Jupyter-Server vid Port `8989` på `localhost` , med lösen ord `pass@word99` . Förutsatt att du har använt kommando raden ovan för att starta behållaren med namn `whitenoise-privacy` , kan du öppna en bash-Terminal i Jupyter-servern genom att köra:

```sh
docker exec -it whitenoise-run bash
```

Docker-instansen rensar all status vid avstängning, så att du förlorar eventuella antecknings böcker som du skapar i den aktiva instansen. För att undvika detta kan du binda montera en lokal mapp till behållaren när du startar den:

```sh
docker run --rm -p 8989:8989 --name whitenoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/whitenoise:privacy
```

Alla antecknings böcker som du skapar under mappen *Mina-antecknings böcker* kommer att lagras i det lokala fil systemet.

## <a name="perform-data-analysis"></a>Utför data analys

Om du vill förbereda en differentiell privat version måste du välja en data källa, en statistik och några sekretess parametrar, vilket anger sekretess skydds nivån.  

I det här exemplet beskrivs de PUMS (Public use mikrodata) i Kalifornien som representerar anonymiserats-poster av unionsmedborgare:

```python
import os
import sys
import numpy as np
import opendp.whitenoise.core as wn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

I det här exemplet beräknar vi medelvärdet och var Ian sen för ålder.  Vi använder totalt `epsilon` 1,0 (Epsilon är vår sekretess parameter och sprider vår sekretess budget över de två antal som vi vill beräkna. Läs mer om [Sekretess mått](concept-differential-privacy.md#differential-privacy-metrics).

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = wn.dp_variance(data = wn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

Resultatet ser ut ungefär så här:

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

Det finns några viktiga saker att anteckna i det här exemplet.  Först `Analysis` representerar objektet ett diagram för data bearbetning.  I det här exemplet beräknas medelvärdet och avvikelsen från samma källmapp.  Du kan dock lägga till mer komplexa uttryck som kombinerar indata med utdata på olika sätt.

Analys diagrammet innehåller `data_upper` och `data_lower` metadata som anger de nedre och övre gränserna för åldrar.  Dessa värden används för att exakt kalibrera bruset för att säkerställa differentiell integritet.  Dessa värden används också i viss hantering av avvikande eller saknade värden.

Slutligen håller analys diagrammet reda på den totala sekretess budgeten som har använts.

Du kan använda biblioteket för att skapa mer komplexa analys diagram, med flera mekanismer, statistik och verktygs funktioner:

| Statistik    | Internkontroll | Verktyg  |
| ------------- |------------|------------|
| Antal         | Gaussisk   | Långa       |
| Histogram     | Geometriska  | Ihopfogning   |
| Medelvärde          | Laplace    | Digitalisera   |
| Quantiles     |            | Filter     |
| Summa           |            | Tillvitelse |
| Varians/kovarians |      | Transformering  |

Mer information finns i den [bärbara datorn för grundläggande data analys](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/basic_data_analysis.ipynb) .

## <a name="approximate-utility-of-differentially-private-releases"></a>Ungefärligt verktyg för differentiellt privata versioner

Eftersom differentiell integritet fungerar genom att kalibrera brus kan verktyget av versioner variera beroende på integritets risken.  I allmänhet är det brus som krävs för att skydda varje enskild vara försumbart när exempel storlekarna ökar, men överbelasta resultatet för versioner som riktar sig mot en enskild individ.  Analytiker kan granska noggrannhets informationen för en version för att avgöra hur användbar versionen är:

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

Resultatet av åtgärden bör se ut ungefär så här:

```text
Age accuracy is: 0.2995732273553991
```

Det här exemplet beräknar medelvärdet enligt ovan och använder `get_accuracy` funktionen för att begära noggrannhet vid `alpha` 0,05. En `alpha` i 0,05 representerar 95% intervall, i det frisläppta värdet kommer att falla inom de rapporterade noggrannhets gränserna om 95% av tiden.  I det här exemplet är den rapporterade noggrannheten 0,3, vilket innebär att det släppta värdet kommer att vara inom ett intervall av bredd 0,6, ungefär 95% av tiden.  Det är inte rätt att tänka på det här värdet som ett felfält eftersom det släppta värdet faller utanför det rapporterade noggrannhets intervallet med den hastighet som anges av `alpha` , och värden utanför intervallet kan vara utanför båda riktningarna.

Analytiker kan fråga `get_accuracy` efter olika värden för `alpha` att få smalare eller bredare konfidens intervall utan att det uppstår ytterligare sekretess kostnader.

## <a name="generate-a-histogram"></a>Generera ett histogram

Den inbyggda `dp_histogram` funktionen skapar ett särskilt privat histogram över någon av följande data typer:

- En kontinuerlig variabel där uppsättningen siffror måste delas upp i lager platser
- En Boolean-eller dichotomous-variabel som bara kan ha två värden
- En kategoriska-variabel där det finns distinkta kategorier som räknas som strängar

Här är ett exempel på en `Analysis` specificerad lager plats för ett kontinuerligt variabelt histogram:

```python
income_edges = list(range(0, 100000, 10000))

with wn.Analysis() as analysis:
    data = wn.Dataset(path = data_path, column_names = var_names)

    income_histogram = wn.dp_histogram(
            wn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Eftersom enskilda individer är åtskilda mellan histogrammets lager platser debiteras sekretess kostnaden bara en gång per histogram, även om histogrammet innehåller många lager platser.

Mer information om histogram finns i histogram- [anteckningsboken](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/histograms.ipynb).

## <a name="generate-a-covariance-matrix"></a>Generera en kovarians mat ris

WhiteNoise erbjuder tre olika funktioner med sin `dp_covariance` funktion:

- Kovarians mellan två vektorer
- Matris för kovarians i en matris
- Matris för tvär varians i ett par med matriser

Här är ett exempel på beräkning av en skalär kovarians:

```python
with wn.Analysis() as analysis:
    wn_data = wn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = wn.dp_covariance(
      left = wn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = wn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

Mer information finns i [antecknings boken för kovarians](
https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/covariance.ipynb)

## <a name="next-steps"></a>Efterföljande moment

- Utforska [WhiteNoise-exempel Notebooks](https://github.com/opendifferentialprivacy/whitenoise-samples/tree/master/analysis).