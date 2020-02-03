---
title: 'Designer: förutsägelse kredit risk exempel'
titleSuffix: Azure Machine Learning
description: Bygg en klassificerare och Använd anpassade Python-skript för att förutsäga kredit risken med Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: ed8ee9b1c711ee0056377154379b8df56e0785df
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964608"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>Bygg en klassificerare & använda Python-skript för att förutsäga kredit risker med Azure Machine Learning designer

**Designer (för hands version) exempel 4**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Den här artikeln visar hur du skapar en komplex pipeline för maskin inlärning med hjälp av designern (för hands version). Du lär dig hur du implementerar anpassad logik med python-skript och jämför flera modeller för att välja det bästa alternativet.

Det här exemplet tränar en klassificerare till att förutsäga kredit risken med hjälp av kredit information, till exempel kredit historik, ålder och antal kredit kort. Du kan dock använda begreppen i den här artikeln för att ta itu med dina egna maskin inlärnings problem.

Här är det färdiga diagrammet för den här pipelinen:

[![diagram över pipelinen](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Krav

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Klicka på exempel 4 för att öppna det.

## <a name="data"></a>Data

I det här exemplet används det tyska kredit kortets data uppsättning från UC Irvine-lagringsplatsen. Den innehåller 1 000-exempel med 20 funktioner och en etikett. Varje exempel representerar en person. De 20 funktionerna omfattar numeriska och kategoriska funktioner. Mer information om data uppsättningen finns på sidan för [webb-och webb sidor](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29). Den sista kolumnen är etiketten, som anger kredit risken och bara har två möjliga värden: hög kredit risk = 2 och låg kredit risk = 1.

## <a name="pipeline-summary"></a>Sammanfattning av pipeline

I den här pipelinen jämför du två olika metoder för att skapa modeller för att lösa det här problemet:

- Utbildning med den ursprungliga data uppsättningen.
- Utbildning med en replikerad data uppsättning.

Med båda metoderna utvärderar du modeller genom att använda test data uppsättningen med replikering för att säkerställa att resultaten justeras med funktionen cost. Testa två klassificerare med båda metoderna: **dubbelriktad Vector-dator** och **besluts träd med två klasser**.

Kostnaden för att klassificera ett litet risk exempel som hög är 1, och kostnaden för att klassificera ett högrisk exempel som lägsta är 5. Vi använder en **execute python-skriptfil** för att redovisa den här felklassificerings kostnaden.

Här är diagrammet över pipelinen:

[![diagram över pipelinen](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Databearbetning

Börja med att använda modulen **metadata-redigeraren** för att lägga till kolumn namn för att ersätta standard kolumn namnen med mer meningsfulla namn som hämtats från data uppsättnings beskrivningen på den unika gruppen. Ange de nya kolumn namnen som kommaavgränsade värden i fältet **nytt kolumn** namn i **metadata-redigeraren**.

Sedan genererar du de inlärnings-och test uppsättningar som används för att utveckla riskhanterings modellen. Dela upp den ursprungliga data uppsättningen i utbildning och test uppsättningar av samma storlek med hjälp av modulen **dela data** . Om du vill skapa uppsättningar med samma storlek ställer du in **del av raderna i den första data uppsättnings** alternativet för att 0,7.

### <a name="generate-the-new-dataset"></a>Generera den nya data uppsättningen

Eftersom kostnaden för att uppskatta risken är hög, ställer du in kostnaden för att inte klassificera så här:

- För högrisk ärenden som är felklassificerade som låg risk: 5
- För låg risk fall klassificeras som hög risk: 1

Om du vill visa den här funktionen kan du generera en ny data uppsättning. I den nya data uppsättningen replikeras varje högrisk exempel fem gånger, men antalet låg risk exempel ändras inte. Dela data i utbildning och testa data uppsättningar före replikering för att förhindra att samma rad används i båda uppsättningarna.

För att replikera högrisk data, ska du ställa in python-koden i en **execute Python-skript** modul:

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

Den **dubbelriktade supporten för Vector Machine** kräver normaliserade data. Använd **normaliserings data** module för att normalisera intervallen för alla numeriska funktioner med en `tanh` omvandling. En `tanh` omvandling konverterar alla numeriska funktioner till värden inom ett intervall på 0 och 1 och bevarar den övergripande fördelningen av värden.

Den **dubbelriktade metoden Vector Machine** module hanterar sträng funktioner, konverterar dem till kategoriska-funktioner och sedan till binära funktioner med värdet noll eller ett. Det innebär att du inte behöver normalisera de här funktionerna.

## <a name="models"></a>Modeller

Eftersom du har tillämpat två klassificerare, **stöd för dubbelriktad Vector Machine** (SVM) och **två klass djup**och två data uppsättningar, genererar du totalt fyra modeller:

- SVM utbildade med ursprungliga data.
- SVM utbildade med replikerade data.
- Utökat besluts träd tränat med ursprungliga data.
- Utökat besluts träd tränat med replikerade data.

I det här exemplet används standard arbets flödet för data vetenskap för att skapa, träna och testa modellerna:

1. Initiera Learning-algoritmerna med hjälp av **dubbelriktad Vector-dator** och två Klasss **öknings bara besluts träd**.
1. Använd **träna modell** för att tillämpa algoritmen på data och skapa den faktiska modellen.
1. Använd **Poäng modellen** för att skapa poäng med hjälp av test exemplen.

I följande diagram visas en del av den här pipelinen där de ursprungliga och replikerade tränings uppsättningarna används för att träna två olika SVM-modeller. **Träna modellen** är ansluten till inlärnings uppsättningen och **Poäng modellen** är ansluten till test uppsättningen.

![Pipeline-diagram](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

I utvärderings fasen av pipelinen beräknar du precisionen för var och en av de fyra modellerna. För den här pipelinen använder du **utvärdera modell** för att jämföra exempel som har samma felklassificerings kostnad.

Modulen **utvärdera modell** kan beräkna prestanda måtten för så många som två poäng modeller. Så du kan använda en **utvärderings modell** för att utvärdera de två SVM-modellerna och en annan instans av **utvärdera modell** för att utvärdera de två bättre besluts träds modellerna.

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

Om du vill visa resultatet av pipelinen högerklickar du på visualisera utdata för den sista **Välj kolumner i data uppsättnings** modulen.

![Visualisera utdata](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

Den första kolumnen visar vilken Machine Learning-algoritm som används för att generera modellen.

Den andra kolumnen visar typen av inlärnings uppsättning.

Den tredje kolumnen innehåller det kostnads känsliga precision svärdet.

Från dessa resultat kan du se att den bästa precisionen tillhandahålls av modellen som skapades med **dubbelriktad Vector-dator** och utbildad på den replikerade träning-datauppsättningen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för designern:

- [Exempel 1 – regression: förutsäga ett bils pris](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exempel 2-regression: jämför algoritmer för bil förutsägelse av bilar](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exempel 3 – klassificering med funktions val: inkomst förutsägelse](how-to-designer-sample-classification-predict-income.md)
- [Exempel 5 – klassificering: förutsägelse omsättning](how-to-designer-sample-classification-churn.md)
- [Exempel 6 – klassificering: förutsäga flyg fördröjningar](how-to-designer-sample-classification-flight-delay.md)
- [Exempel 7 – text klassificering: Wikipedia SP 500-datauppsättning](how-to-designer-sample-text-classification.md)
