---
title: 'Klassificering: Förutsäga kreditrisken (kostnad känsliga)'
titleSuffix: Azure Machine Learning service
description: Detta visuella gränssnittet exempelexperiment visar hur du använder en anpassad Python-skriptet för att utföra kostnadskänslig binär klassificering. Du kan förutsäga kreditrisk baserat på informationen i en kreditansökan.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 433c258f86705f66e0163100407be7996d68bc6b
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65440951"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Exempel 4 - klassificering: Förutsäga kreditrisken (kostnad känsliga)

Detta visuella gränssnittet exempelexperiment visar hur du använder en anpassad Python-skriptet för att utföra kostnadskänslig binär klassificering. Kostnaden för misclassifying positivt exemplen är fem gånger kostnaden för misclassifying negativt exemplen.

Det här exemplet kan du förutsäga kreditrisk baserat på information som anges i en kreditansökan, med hänsyn till felklassificering kostnaderna.

I det här experimentet jämför vi två olika metoder för att skapa modeller för att lösa problemet:

- Utbildning med den ursprungliga datauppsättningen.
- Utbildning med en replikerad datauppsättning.

Med båda metoderna utvärdera det modeller med hjälp av test-datauppsättning med replikering så att resultat ligger i linje med funktionen kostnaden. Testar vi två klassificerare med båda metoderna: **Två-Class Support Vector Machine** och **Tvåklassförhöjt beslutsträd**.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj den **öppna** knappen för experimentet exempel 4:

    ![Öppna experimentet](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="related-sample"></a>Relaterade exemplet

Se [exempel 3 – klassificering: Kreditriskförutsägelse (grundläggande)](ui-sample-classification-predict-churn.md) för ett grundläggande experiment som löser samma problem som det här experimentet utan justera för felklassificering kostnader.

## <a name="data"></a>Data

Vi använder den tyska kreditkort datauppsättningen från UC Irvine-databasen. Den här datauppsättningen innehåller 1 000 exempel med 20 funktioner och 1 etikett. Varje exempel representerar en person. De 20 funktionerna inkluderar numeriska och kategoriska funktioner. Se den [UCI webbplatsen](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) för mer information om datauppsättningen. Den sista kolumnen är den etiketten, som anger kreditrisken och har bara två möjliga värden: hög kreditrisk = 2 och låg kreditrisk = 1.

## <a name="experiment-summary"></a>Sammanfattning för experiment

Kostnaden för misclassifying ett exempel på låg risk som hög är 1 och kostnaden för misclassifying ett med hög risk exempel så låga är 5. Vi använder en **kör Python-skript** modulen att kompensera för den här felklassificering kostnad.

Här är diagram över experimentet:

[![Diagram över experimentet](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Databearbetning

Vi börjar med hjälp av den **Metadata redigeraren** modul för att lägga till kolumnen som ska ersätta standardkolumnvärdena med mer beskrivande namn, hämtas från datauppsättningen beskrivningen på webbplatsen UCI. Vi ger de nya kolumnnamnen som fil med kommaavgränsade värden i den **ny kolumn** namnfältet för den **Metadata redigeraren**.

Nu ska vi generera utbildningen och testa uppsättningar som används för att utveckla förutsägelsemodell för risk. Vi dela upp den ursprungliga datauppsättningen i uppsättningar för träning och testning av samma storlek med hjälp av den **dela Data** modulen. Om du vill skapa uppsättningar av samma storlek, vi ställer in den **del av rader i den första utdatauppsättningen** möjlighet att 0,5.

### <a name="generate-the-new-dataset"></a>Skapa den nya datauppsättningen

Eftersom kostnaden för undervärdera risk är hög, ange vi kostnaden för felklassificering så här:

- Med hög risk fall felklassificerade som lågrisk: 5
- Låg risk fall felklassificerade som hög risk: 1

För att återspegla den här kostnaden funktionen vi för att generera en ny datauppsättning. Varje med hög risk exempel replikeras fem gånger i den nya datauppsättningen, men antalet med låg risk exempel ändras inte. Vi dela upp data till datauppsättningar för träning och testning innan replikering till att förhindra att samma rad i mängderna.

Om du vill replikera data med hög risk, låter vi den här Python-koden i en **kör Python-skript** modulen:

```
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

Den **kör Python-skript** modulen replikerar datauppsättningar för både för träning och testning.

### <a name="feature-engineering"></a>Funktionstekniker

Den **två-Class Support Vector Machine** algoritmen kräver normaliserade data. Så vi använder den **normalisera Data** modulen att normalisera intervallen för alla numeriska funktioner med en `tanh` omvandling. En `tanh` omvandling konverterar alla numeriska funktioner till värden inom ett intervall med 0 och 1 samtidigt som den övergripande distributionen av värden.

Den **två-Class Support Vector Machine** modulen hanterar sträng-funktioner, till kategoriska funktioner och sedan till binär funktioner med ett värde på 0 eller 1. Så vi inte behöver att normalisera de här funktionerna.

## <a name="models"></a>Modeller

Eftersom vi använder två klassificerare **två-Class Support Vector Machine** (SVM) och **Tvåklassförhöjt beslutsträd**, och även använder två datauppsättningar, vi generera totalt fyra modeller:

- SVM tränas med ursprungliga data.
- SVM tränas med replikerade data.
- Förbättrat beslutsträd tränas med ursprungliga data.
- Förbättrat beslutsträd tränas med replikerade data.

Vi kan använda standard experimentella arbetsflödet för att skapa, träna och testa modeller:

1. Initiera learning-algoritmer, använda **två-Class Support Vector Machine** och **Tvåklassförhöjt beslutsträd**.
1. Använd **Träningsmodell** att använda algoritmen för data och skapa den faktiska modellen.
3. Använd **Poängmodell** frambringa resultat med hjälp av test-exemplen.

I följande diagram visas en del av det här experimentet där de ursprungliga och replikerade utbildning som används för att träna två olika SVM modeller. **Träna modellen** är ansluten till träningsmängden, och **Poängmodell** är ansluten till test-uppsättningen.

![Experimentdiagram](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)


I steget utvärdering av experimentet compute vi noggrannheten för var och en av de fyra modellerna. Det här experimentet som vi använder **utvärdera modell** att jämföra exempel som har samma felklassificering kostnad.

Den **utvärdera modell** modulen kan beräkna prestandamått för upp till två poängsatta modeller. Så vi använder en instans av **utvärdera modell** att utvärdera de två modellerna SVM och en annan instans av **utvärdera modell** att utvärdera de två modellerna för beslutsträd.

Observera att det replikerade testdata används som indata för **Poängmodell**. Med andra ord omfattar sista Precision poängen kostnaden för att hämta etiketterna fel.

## <a name="combine-multiple-results"></a>Kombinera flera resultat

Den **utvärdera modell** modulen genererar en tabell med en enskild rad som innehåller olika mått. Om du vill skapa en enda uppsättning resultatens noggrannhet först använder vi **Lägg till rader** att kombinera resultaten till en enda tabell. Vi använder sedan följande skript i Python i den **kör Python-skript** modulen lägger du till modellnamnet och utbildning metod för varje rad i tabellen med resultat:

```
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```


## <a name="results"></a>Resultat

Om du vill visa resultatet av experimentet, kan du högerklicka på visualisera utdata för senast **Välj kolumner i datauppsättning** modulen.

![Visualisera utdata](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

Den första kolumnen visar en lista över de machine learning-algoritm som används för att generera modellen.
Den andra kolumnen anger vilken typ av träningsmängden.
Den tredje kolumnen innehåller värdet kostnadskänslig precision.

På dessa resultat kan du se att bäst noggrannhet tillhandahålls av modellen som har skapats med **två-Class Support Vector Machine** och tränade på replikerade utbildning datauppsättningen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – Regression: Förutsäga priset på en bil](ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 2 – Regression: Jämför algoritmer för bil pris förutsägelse](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering: Förutsäga kreditrisk](ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 5 - klassificering: Förutsäga kundomsättning](ui-sample-classification-predict-churn.md)
