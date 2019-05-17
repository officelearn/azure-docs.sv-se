---
title: 'Klassificering: Förutsäga kreditrisk'
titleSuffix: Azure Machine Learning service
description: Lär dig mer om att skapa en machine learning klassificerare utan att behöva skriva en enda rad kod med hjälp av det visuella gränssnittet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: f37c945758cfbd03889d79acf764e7f67022267a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789404"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Exempel 3 – klassificering: Förutsäga kreditrisk

Lär dig mer om att skapa en machine learning klassificerare utan att behöva skriva en enda rad kod med hjälp av det visuella gränssnittet. Det här exemplet träna en **tvåklassförhöjt beslutsträd** för att förutsäga kredit risken (hög eller låg) baserad på kredit programinformation som kredithistorik, ålder och antalet kreditkort.

Eftersom vi försöker besvara frågan ”vilken jag”? Detta kallas en klassificeringsproblem. Du kan dock använda samma grundläggande processen för att hantera alla typer av problem med machine learning, oavsett om det är regression, klassificering, klustring och så vidare.

Här är färdiga diagrammet det här experimentet:

![Diagram över experimentet](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj den **öppna** knappen för experimentet exempel 3:

    ![Öppna experimentet](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Relaterade exemplet

[Exempel 4 - klassificering: Kreditriskförutsägelse (kostnaden känslig)](ui-sample-classification-predict-credit-risk-cost-sensitive.md) ger en avancerad experiment som löser samma problem som det här experimentet. Den visar hur du utför _kostnaden känsliga_ klassificering med hjälp av en **kör Python-skript** modulen och jämföra prestanda för två binär klassificering algoritmer. Referera till det om du vill lära dig mer om hur du skapar klassificering experiment.

## <a name="data"></a>Data

Vi använder den tyska kreditkort datauppsättningen från UC Irvine-databasen.
Datauppsättningen innehåller 1 000 exempel med 20 funktioner och 1 etikett. Varje exempel representerar en person. Funktionerna omfattar bland numeriska och kategoriska. Se den [UCI webbplatsen](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) för betydelsen av kategoriska funktioner. Den sista kolumnen är den etiketten, som anger kreditrisken och har bara två möjliga värden: hög kreditrisk = 2 och låg kreditrisk = 1.

## <a name="experiment-summary"></a>Sammanfattning för experiment

Vi följer du stegen nedan för att skapa experimentet:

1. Dra modulen tyska UCI kreditkortsdata datauppsättning till den experimentets arbetsyta.
1. Lägg till en **redigera Metadata** modulen så att vi kan lägga till beskrivande namn för varje kolumn.
1. Lägg till en **dela Data** modul för att skapa uppsättningar för träning och testning. Ange andelen av rader i den första utdatauppsättningen till 0,7. Den här inställningen anger att 70% av data ska vara utdata till den vänstra porten från modulen och resten till rätt port. Vi använder vänstra datauppsättning för träning och rätt typ för testning.
1. Lägg till en **Tvåklassförhöjt beslutsträd** modul för att initiera en klassificerare för beslutsträd trädet.
1. Lägg till en **Träningsmodell** modulen. Anslut klassificeraren från föregående steg till den vänstra indataporten för den **Träningsmodell**. Lägg till träningsmängden (vänster utgående port på den **dela Data**) till den högra indataporten för den **Träningsmodell**. Den **träna modell** kommer träna klassificeraren.
1. Lägg till en **Poängmodell** modulen och ansluta den **Träningsmodell** modul till den. Lägga till uppsättningen test (högra porten för den **dela Data**) till den **Poängmodell**. Den **Poängmodell** blir förutsägelserna. Du kan välja dess utdataporten för att se förutsägelserna och troliga positivt klass.
1. Lägg till en **utvärdera modell** modulen och Anslut poängsatta datauppsättningen till dess vänstra indataporten. Välj om du vill se utvärderingsresultaten utdataporten för den **utvärdera modell** modul och välj **visualisera**.

Här är den klar experimentdiagram:

![Diagram över experimentet](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>Resultat

![Utvärdera resultaten](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

I bedömningsresultaten, kan du se att AUC av modellen är 0.776. Precisionen är 0.621 med tröskelvärde 0,5 återkallelsen är 0,456 och F1-poängen är 0.526.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – Regression: Förutsäga priset på en bil](ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 2 – Regression: Jämför algoritmer för bil pris förutsägelse](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exempel 4 - klassificering: Förutsäga kreditrisken (kostnad känsliga)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 5 - klassificering: Förutsäga kundomsättning](ui-sample-classification-predict-churn.md)