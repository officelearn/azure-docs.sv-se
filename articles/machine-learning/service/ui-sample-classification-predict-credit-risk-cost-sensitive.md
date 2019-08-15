---
title: 'Exempel på visuella gränssnitt #4: Klassificering för att förutsäga kredit risk (kostnads känsligt)'
titleSuffix: Azure Machine Learning service
description: Den här artikeln visar hur du skapar ett komplext dator inlärnings experiment med hjälp av det visuella gränssnittet. Du lär dig hur du implementerar anpassade Python-skript och jämför flera modeller för att välja det bästa alternativet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: ee4b67c82ef2bf5a1ef9c060687cc1c937328e66
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990419"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Exempel 4 – klassificering: Förutsägelse kredit risk (kostnads känsligt)

Den här artikeln visar hur du skapar ett komplext dator inlärnings experiment med hjälp av det visuella gränssnittet. Du lär dig hur du implementerar anpassad logik med python-skript och jämför flera modeller för att välja det bästa alternativet.

Det här exemplet tränar en klassificerare till att förutsäga kredit risken med hjälp av kredit information, till exempel kredit historik, ålder och antal kredit kort. Du kan dock använda begreppen i den här artikeln för att ta itu med dina egna maskin inlärnings problem.

Om du precis har kommit igång med Machine Learning kan du ta en titt på det [grundläggande klassificerar exemplet](ui-sample-classification-predict-credit-risk-basic.md) först.

Här är det färdiga diagrammet för det här experimentet:

[![Diagram över experimentet](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj knappen **Öppna** för exempel fyra experiment:

    ![Öppna experimentet](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

Vi använder det tyska kredit kortets data uppsättning från UC Irvine-lagringsplatsen. Den här data uppsättningen innehåller 1 000-exempel med 20 funktioner och 1 etikett. Varje exempel representerar en person. De 20 funktionerna omfattar numeriska och kategoriska funktioner. Mer information om data uppsättningen finns på sidan för en [webb sida](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) . Den sista kolumnen är etiketten, som anger kredit risken och bara har två möjliga värden: hög kredit risk = 2 och låg kredit risk = 1.

## <a name="experiment-summary"></a>Experiment Sammanfattning

I det här experimentet jämför vi två olika metoder för att skapa modeller för att lösa det här problemet:

- Utbildning med den ursprungliga data uppsättningen.
- Utbildning med en replikerad data uppsättning.

Med båda metoderna utvärderar vi modeller genom att använda test data uppsättningen med replikering för att säkerställa att resultaten justeras med funktionen cost. Vi testar två klassificerare med båda metoderna: **Dubbelriktad dubbelriktad dator** och **besluts träd med två klasser**.

Kostnaden för att klassificera ett litet risk exempel som hög är 1, och kostnaden för att klassificera ett högrisk exempel som lägsta är 5. Vi använder en **execute python-skriptfil** för att redovisa den här felklassificerings kostnaden.

Här är grafen till experimentet:

[![Diagram över experimentet](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Databearbetning

Vi börjar med att använda modulen **metadata** -redigeraren för att lägga till kolumn namn för att ersätta standard kolumn namnen med mer meningsfulla namn som hämtats från data uppsättnings beskrivningen på den unika en-plats. Vi tillhandahåller de nya kolumn namnen som kommaavgränsade värden i fältet **nytt kolumn** namn i **metadata**-redigeraren.

Därefter genererar vi de inlärnings-och test uppsättningar som används för att utveckla risk förutsägelse modellen. Vi delar upp den ursprungliga data uppsättningen i utbildning och test uppsättningar av samma storlek med hjälp av modulen **dela data** . Om du vill skapa uppsättningar med samma storlek ställer vi in **del av raderna i det första alternativet för data uppsättning för utdata** till 0,5.

### <a name="generate-the-new-dataset"></a>Generera den nya data uppsättningen

Eftersom kostnaden för att uppskatta risken är hög, ställer vi in kostnaderna för att klassificera så här:

- För högrisk ärenden som är felklassificerade som låg risk: 5
- För låg risk fall klassificeras som hög risk: 1

För att avspegla denna kostnads funktion genererar vi en ny data uppsättning. I den nya data uppsättningen replikeras varje högrisk exempel fem gånger, men antalet låg risk exempel ändras inte. Vi delar upp data i utbildnings-och test data uppsättningar innan replikeringen kan förhindra att samma rad används i båda uppsättningarna.

För att replikera högrisk data sätter vi den här python-koden i en **execute Python-skript** modul:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**Execute python script** module replikerar både utbildning och test-datauppsättningar.

### <a name="feature-engineering"></a>Funktionstekniker

Den **dubbelriktade supporten för Vector Machine** kräver normaliserade data. Vi använder därför **normaliserings data** module för att normalisera intervallen för alla numeriska funktioner med en `tanh` omvandling. En `tanh` omvandling konverterar alla numeriska funktioner till värden inom ett intervall på 0 och 1, samtidigt som den övergripande fördelningen av värden bevaras.

Den **dubbelriktade metoden Vector Machine** module hanterar sträng funktioner, konverterar dem till kategoriska-funktioner och sedan till binära funktioner med värdet 0 eller 1. Därför behöver vi inte normalisera de här funktionerna.

## <a name="models"></a>Modeller

Eftersom vi tillämpar två klassificerare, har **två klass stöd för Vector Machine** (SVM) och ett **besluts träd med två klass**, och även använder två data uppsättningar, genererar vi totalt fyra modeller:

- SVM utbildade med ursprungliga data.
- SVM utbildade med replikerade data.
- Utökat besluts träd tränat med ursprungliga data.
- Utökat besluts träd tränat med replikerade data.

Vi använder standard experiment arbets flödet för att skapa, träna och testa modellerna:

1. Initiera Learning-algoritmerna med hjälp av **dubbelriktad Vector-dator** och två Klasss öknings bara **besluts träd**.
1. Använd **träna modell** för att tillämpa algoritmen på data och skapa den faktiska modellen.
1. Använd **Poäng modellen** för att skapa poäng med hjälp av test exemplen.

I följande diagram visas en del av det här experimentet där de ursprungliga och replikerade tränings uppsättningarna används för att träna två olika SVM-modeller. **Träna modellen** är ansluten till inlärnings uppsättningen och **Poäng modellen** är ansluten till test uppsättningen.

![Experiment diagram](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

I utvärderings fasen av experimentet beräknar vi precisionen för var och en av de fyra modellerna. För det här experimentet använder vi **utvärdera modell** för att jämföra exempel som har samma felklassificerings kostnad.

Modulen **utvärdera modell** kan beräkna prestanda måtten för så många som två poäng modeller. Vi använder därför en utvärderings **modell** för att utvärdera de två SVM-modellerna och en annan instans av **utvärdera modell** för att utvärdera de två bättre besluts träds modellerna.

Observera att den replikerade test data uppsättningen används som indata för **Poäng modellen**. Med andra ord inkluderar de slutliga noggrannhets poängen kostnaden för att få etiketter fel.

## <a name="combine-multiple-results"></a>Kombinera flera resultat

Modulen **utvärdera modell** skapar en tabell med en enda rad som innehåller olika mått. För att skapa en enda uppsättning noggrannhets resultat använder vi först **Lägg till rader** för att kombinera resultaten till en enda tabell. Vi använder sedan följande Python-skript i modulen **Kör Python-skript** för att lägga till modell namn och övnings metod för varje rad i resultat tabellen:

```Python
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

Om du vill visa resultatet av experimentet högerklickar du på visualisera utdata för den sista **Välj kolumner i data uppsättnings** modulen.

![Visualisera utdata](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

Den första kolumnen visar vilken Machine Learning-algoritm som används för att generera modellen.
Den andra kolumnen visar typen av inlärnings uppsättning.
Den tredje kolumnen innehåller det kostnads känsliga precision svärdet.

Från dessa resultat kan du se att den bästa precisionen tillhandahålls av modellen som skapades med **dubbelriktad Vector-dator** och utbildad på den replikerade träning-datauppsättningen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – regression: Förutsäga ett bils pris](ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 2 – regression: Jämför algoritmer för bilpris förutsägelser för bilar](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering: Förutsägelse kredit risk](ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 5 – klassificering: Förutsäg omsättning](ui-sample-classification-predict-churn.md)
- [Exempel 6 – klassificering: Förutsäg fördröjningar i flygning](ui-sample-classification-predict-flight-delay.md)
