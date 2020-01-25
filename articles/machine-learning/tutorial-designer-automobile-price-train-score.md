---
title: 'Självstudie: förutsäga Automobile-priset med designern'
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar, poängs ätter och distribuerar en maskin inlärnings modell med hjälp av ett dra-och-släpp-gränssnitt. Den här självstudien är en del av en serie i två delar om förutsägelse av bil priser med hjälp av linjär regression.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 639a61cddde27b0d989e5a3dd4c599c353182a73
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720202"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Självstudie: förutsäga Automobile-priset med designer (för hands version)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här självstudien får du lära dig hur du använder Azure Machine Learning designer för att utveckla och distribuera en förutsägelse analys som förutsäger priset på en bil.

I del ett av självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny pipeline.
> * Importera data.
> * Förbered data.
> * Träna en maskin inlärnings modell.
> * Utvärdera en maskin inlärnings modell.

I [del två](tutorial-designer-automobile-price-deploy.md) av självstudien distribuerar du din modell som en inferencing-slutpunkt i real tid för att förutsäga priset på en bil baserat på tekniska specifikationer som du skickar den. 

> [!NOTE]
>En slutförd version av den här självstudien är tillgänglig som en exempel pipeline.
>
>Du hittar det genom att gå till designern på arbets ytan. I det **nya pipeline** -avsnittet väljer du **exempel 1-regression: Automobile pris förutsägelse (grundläggande)** .

## <a name="create-a-new-pipeline"></a>Skapa en ny pipeline

Azure Machine Learning pipelines ordnar flera maskin inlärnings-och data bearbetnings steg i en enda resurs. Pipelines gör det möjligt att organisera, hantera och återanvända komplexa Machine Learning-arbetsflöden mellan projekt och användare.

Om du vill skapa en Azure Machine Learning pipeline behöver du en Azure Machine Learning arbets yta. I det här avsnittet får du lära dig hur du skapar båda dessa resurser.

### <a name="create-a-new-workspace"></a>Skapa en ny arbetsyta

Om du har en Azure Machine Learning-arbetsyta med en Enterprise-utgåva går du [vidare till nästa avsnitt](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Skapa pipelinen

1. Logga in på [ml.Azure.com](https://ml.azure.com)och välj den arbets yta som du vill arbeta med.

1. Välj **Designer**.

    ![Skärm bild av den visuella arbets ytan som visar hur du får åtkomst till designern](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Välj **lättanvända inbyggda moduler**.

1. Längst upp på arbets ytan väljer du standard pipelinen för pipelinen **– skapad – på**. Byt namn på den till *bil förutsägelser*. Namnet behöver inte vara unikt.

## <a name="import-data"></a>Importera data

Det finns flera exempel data uppsättningar som ingår i designern som du kan experimentera med. I den här självstudien använder du **bil-pris data (RAW)** . 

1. Till vänster om arbets ytan för pipelinen är en palett med data uppsättningar och moduler. Välj **data uppsättningar**och Visa sedan avsnittet **exempel** för att visa tillgängliga exempel data uppsättningar.

1. Välj data uppsättningens **bil pris data (RAW)** och dra den till arbets ytan.

   ![Dra data till arbets yta](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Visualisera datan

Du kan visualisera data för att förstå den data uppsättning som du kommer att använda.

1. Välj modulen **Automobile Price data (RAW)** .

1. Välj **utdata**i fönstret Egenskaper till höger om arbets ytan.

1. Välj diagram ikonen för att visualisera data.

    ![Visualisera datan](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Välj olika kolumner i data fönstret om du vill visa information om var och en.

    Varje rad representerar en bil och variablerna som är kopplade till varje bil visas som kolumner. Det finns 205 rader och 26 kolumner i den här data uppsättningen.

## <a name="prepare-data"></a>Förbered data

Data uppsättningar kräver vanligt vis lite för bearbetning före analys. Du kanske har märkt vissa saknade värden när du kontrollerade data uppsättningen. De värden som saknas måste rengöras så att modellen kan analysera data korrekt.

### <a name="remove-a-column"></a>Ta bort en kolumn

När du tränar en modell måste du göra något om de data som saknas. I den här data uppsättningen saknar kolumnen **normaliserade förluster** många värden, så du utesluter den kolumnen från modellen helt och hållet.

1. Ange **Välj** i rutan Sök högst upp på paletten för att hitta modulen **Välj kolumner i data uppsättning** .

1. Dra modulen **Välj kolumner i data uppsättning** till arbets ytan. Släpp modulen under data uppsättnings modulen.

1. Anslut data uppsättningen för **Automobil pris data (RAW)** till modulen **Välj kolumner i data uppsättning** . Dra från data uppsättningens utgående port, som är den lilla cirkeln längst ned i data uppsättningen på arbets ytan, till Indataporten för **Select-kolumner i data uppsättningen**, som är den lilla cirkeln överst i modulen.

    > [!TIP]
    > Du skapar ett data flöde via din pipeline när du ansluter utdataporten för en modul till en annan indataport.
    >

    ![Anslut moduler](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Välj modulen **Välj kolumner i data uppsättning** .

1. I fönstret Egenskaper till höger om arbets ytan väljer du **alla kolumner**.

1. Välj **+** för att lägga till en ny regel.

1. I den nedrullningsbara menyn väljer du **Uteslut** och **kolumn namn**.
    
1. Ange *normaliserade förluster* i text rutan.

1. I det nedre högra hörnet väljer du **Spara** för att stänga kolumn väljaren.

    ![Undanta en kolumn](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Välj modulen **Välj kolumner i data uppsättning** . 

1. I fönstret Egenskaper markerar du text rutan **kommentar** och anger *exkludera normaliserade förluster*.

    Kommentarerna visas i grafen för att hjälpa dig att organisera din pipeline.

### <a name="clean-missing-data"></a>Rensa data som saknas

Din data uppsättning har fortfarande värden som saknas efter att du tagit bort kolumnen **normaliserade förluster** . Du kan ta bort återstående data som saknas med hjälp av modulen **Rensa data som saknas** .

> [!TIP]
> Att rensa saknade värden från indata är ett krav för att använda de flesta moduler i designern.

1. Skriv **Rensa** i sökrutan för att hitta modulen **Rensa data som saknas** .

1. Dra modulen **Rensa data som saknas** till pipeline-arbetsytan. Anslut den till modulen **Välj kolumner i data uppsättning** . 

1. I fönstret Egenskaper väljer du **ta bort hela raden** under **rensnings läge**.

1. I rutan Egenskaper **kommentar** anger du *ta bort saknade värde rader*. 

    Din pipeline bör nu se ut ungefär så här:
    
    ![Select-Column](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Träna en Machine Learning-modell

Nu när du har modulerna på plats för att bearbeta data kan du ställa in träna moduler.

Eftersom du vill förutsäga pris, vilket är ett tal, kan du använda en Regressions algoritm. I det här exemplet använder du en linjär Regressions modell.

### <a name="split-the-data"></a>Dela data

Att dela data är en vanlig uppgift i Machine Learning. Du kommer att dela upp dina data i två separata data uppsättningar. En data uppsättning kommer att träna modellen och den andra kommer att testa hur väl modellen utförs.

1. Ange **dela data** i sökrutan för att hitta modulen **dela data** . Anslut den vänstra porten för modulen **Rensa data som saknas** till modulen **dela data** .

    > [!IMPORTANT]
    > Se till att de vänstra Utdataportarna av **rensa saknade data** ansluter till **delade data**. Den vänstra porten innehåller de rensade data. Rätt port innehåller discarted-data.

1. Välj modulen **dela data** .

1. I fönstret Egenskaper ställer du in **bråk talet i den första data uppsättningen för utdata** till 0,7.

    Det här alternativet delar upp 70 procent av data för att träna modellen och 30 procent för att testa den. Data uppsättningen 70 procent kan nås via den vänstra utdataporten. Återstående data kommer att vara tillgängliga via den högra utdataporten.

1. I rutan Egenskaper **kommentar** anger du *dela in data uppsättningen i Training set (0,7) och test uppsättning (0,3)* .

### <a name="train-the-model"></a>Träna modellen

Träna modellen genom att ge den en data uppsättning som inkluderar priset. Algoritmen skapar en modell som förklarar förhållandet mellan funktionerna och priset som presenteras av tränings data.

1. Om du vill välja Learning-algoritmen rensar du sökrutan för modulens palett.

1. Expandera **Machine Learning algoritmer**.
    
    Med det här alternativet visas flera kategorier av moduler som du kan använda för att initiera Learning-algoritmer.

1. Välj **regressions** > **linjär regression**och dra den till pipeline-arbetsytan.

1. Leta upp och dra modulen **träna modell** till pipeline-arbetsytan. 

1. Anslut utdata från modulen **linjär regression** till vänster indata för modulen **träna modell** .

1. Anslut övnings data utmatningen (den vänstra porten) för modulen **dela data** till rätt indata för modulen **träna modell** .
    
    > [!IMPORTANT]
    > Se till att de vänstra Utdataportarna för **delade data** ansluter till **träna modell**. Den vänstra porten innehåller inlärnings uppsättningen. Rätt port innehåller test uppsättningen.

    ![Skärm bild som visar korrekt konfiguration av modulen träna modell. Modulen linjär regression ansluter till den vänstra porten för modulen träna modell och modulen dela data ansluts till rätt port för träna modell](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Välj modulen **träna modell** .

1. I fönstret Egenskaper väljer du **Redigera kolumn** väljare.

1. I dialog rutan **etikett kolumn** expanderar du den nedrullningsbara menyn och väljer **kolumn namn**. 

1. I text rutan anger du *pris* för att ange det värde som din modell ska förutsäga.

    Din pipeline bör se ut så här:

    ![Skärm bild som visar korrekt konfiguration av pipelinen efter att du lagt till modulen träna modell.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="score-a-machine-learning-model"></a>Score en Machine Learning-modell

När du har tränat din modell genom att använda 70 procent av data kan du använda den för att se hur väl modellen fungerar.

1. Ange *Poäng modell* i sökrutan för att hitta modulen **Poäng modell** . Dra modulen till pipeline-arbetsytan. 

1. Anslut utdata från modulen **träna modell** till den vänstra Indataporten för **Poäng modell**. Anslut test data utmatningen (höger port) för modulen **dela data** till den högra Indataporten för **Poäng modellen**.

## <a name="evaluate-a-machine-learning-model"></a>Utvärdera en Machine Learning-modell

Använd modulen **utvärdera modell** för att utvärdera hur bra din modell bevisar test data uppsättningen.

1. Välj *utvärdera* i sökrutan för att hitta modulen **utvärdera modell** . Dra modulen till pipeline-arbetsytan. 

1. Anslut utdata från modulen **Poäng modell** till den vänstra inmatningen av **utvärdera modell**. 

    Den sista pipelinen bör se ut ungefär så här:

    ![Skärm bild som visar korrekt konfiguration av pipelinen.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="run-the-pipeline"></a>Köra en pipeline

[!INCLUDE [aml-ui-create-training-compute](../../includes/aml-ui-create-training-compute.md)]

### <a name="view-scored-labels"></a>Visa Poäng etiketter

När körningen är klar kan du visa resultatet av pipeline-körningen. Börja med att titta på förutsägelserna som genereras av Regressions modellen.

1. Välj modulen **Poäng modell** för att visa dess utdata.

1. I fönstret Egenskaper väljer du **utdata** > diagram ikon ![visualisera ikon](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) för att visa resultat.

    Här kan du se de förutsagda priser och de faktiska priserna från test data.

    ![Skärm bild av utmatnings visualiseringen som markerar den markerade etikett kolumnen](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Utvärdera modeller

Använd **utvärdera modell** för att se hur väl den tränade modellen utförts på test data uppsättningen.

1. Välj modulen **utvärdera modell** för att visa dess utdata.

1. I fönstret Egenskaper väljer du **utdata** > diagram ikon ![visualisera ikon](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) för att visa resultat.

Följande statistik visas för din modell:

* **Medelvärde för absolut fel (Mae)** : medelvärdet av absoluta fel. Ett fel är skillnaden mellan det förväntade värdet och det faktiska värdet.
* **Rot genomsnitts fel (rmse)** : kvadratroten ur genomsnittet av de förutsägelser som gjorts på test data uppsättningen.
* **Relativa absoluta fel**: Medelvärdet av absoluta fel i förhållande till den absoluta skillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
* **Relativa kvadratfel**: Medelvärdet av kvadratfel i förhållande till kvadratskillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
* **Friktionskoefficienten**: även känt som R-kvadratvärdet anger det här statistik måttet hur väl en modell passar data.

För all felstatistik gäller att mindre är bättre. Ett mindre värde anger att förutsägelserna är närmare de faktiska värdena. För att fastställa koefficienten är det närmare värdet en (1,0), desto bättre förutsägelser.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I en del av den här självstudien har du slutfört följande uppgifter:

* Skapa en pipeline
* Förbereda data
* Träna modellen
* Poäng och utvärdera modellen

I del två får du lära dig hur du distribuerar din modell som en slut punkt i real tid.

> [!div class="nextstepaction"]
> [Fortsätt till att distribuera modeller](tutorial-designer-automobile-price-deploy.md)
