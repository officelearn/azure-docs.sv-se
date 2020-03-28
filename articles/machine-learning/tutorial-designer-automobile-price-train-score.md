---
title: 'Handledning: Förutsäga bil pris med designern'
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar, poängsätter och distribuerar en maskininlärningsmodell med hjälp av ett dra-och-släpp-gränssnitt. Denna handledning är del ett av en tvådelad serie om att förutsäga bilpriser med hjälp av linjär regression.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 8b471dd509dca02ab2a66d70dbc7fac9988f1afe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472245"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Handledning: Förutsäga bil pris med designern (förhandsvisning)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

I den här självstudiekursen i två delar får du lära dig hur du använder Azure Machine Learning-designern för att träna och distribuera en maskininlärningsmodell som förutsäger priset på alla bilar. Designern är ett dra-och-släpp-verktyg som gör att du kan skapa maskininlärningsmodeller utan en enda kodrad.

I del ett av handledningen får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny pipeline.
> * Importera data.
> * Förbered data.
> * Träna en maskininlärningsmodell.
> * Utvärdera en maskininlärningsmodell.

I [del två](tutorial-designer-automobile-price-deploy.md) av självstudien distribuerar du din modell som en slutpunkt i realtid för att förutsäga priset på en bil baserat på tekniska specifikationer som du skickar den. 

> [!NOTE]
>En slutförd version av den här självstudien är tillgänglig som en exempelpipeline.
>
>Om du vill hitta den går du till designern på arbetsytan. I avsnittet **Ny pipeline** väljer du **Exempel 1 - Regression: Automobile Price Prediction(Basic)**.

## <a name="create-a-new-pipeline"></a>Skapa en ny pipeline

Azure Machine Learning-pipelines organiserar flera steg för maskininlärning och databearbetning till en enda resurs. Med pipelines kan du organisera, hantera och återanvända komplexa machine learning-arbetsflöden mellan projekt och användare.

Om du vill skapa en Azure Machine Learning-pipeline behöver du en Azure Machine Learning-arbetsyta. I det här avsnittet får du lära dig hur du skapar båda dessa resurser.

### <a name="create-a-new-workspace"></a>Skapa en ny arbetsyta

För att kunna använda designern behöver du först en Azure Machine Learning-arbetsyta. Arbetsytan är den resurs på den högsta nivån för Azure Machine Learning, den ger en centraliserad plats att arbeta med alla artefakter som du skapar i Azure Machine Learning.

Om du har en Azure Machine Learning-arbetsyta med en Enterprise-utgåva [går du vidare till nästa avsnitt](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Skapa pipelinen

1. Logga in <a href="https://ml.azure.com?tabs=jre" target="_blank">på ml.azure.com</a>och välj den arbetsyta som du vill arbeta med.

1. Välj **Designer**.

    ![Skärmbild av den visuella arbetsytan som visar hur du kommer åt designern](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Välj **Lätt att använda fördefinierade moduler**.

1. Högst upp på arbetsytan väljer du standardpipellinenamnet **Pipeline-Created-on**. Byt namn på den till *Automobile pris förutsägelse*. Namnet behöver inte vara unikt.

## <a name="set-the-default-compute-target"></a>Ange standardberäkningsmål

En pipeline körs på ett beräkningsmål, vilket är en beräkningsresurs som är kopplad till arbetsytan. När du har skapat ett beräkningsmål kan du återanvända det för framtida körningar.

Du kan ange ett **standardberäkningsmål** för hela pipelinen, som talar om för varje modul att använda samma beräkningsmål som standard. Du kan dock ange beräkningsmål per modul.

1. Bredvid pipelinenamnet väljer du **ikonen Redskap** ![Skärmbild av kugghjulsikonen](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) högst upp på arbetsytan för att öppna fönstret **Inställningar.**

1. Välj **beräkningsmål** i fönstret Inställningar till **Select compute target**höger om arbetsytan .

    Om du redan har ett tillgängligt beräkningsmål kan du välja det för att köra den här pipelinen.

    > [!NOTE]
    > Designern kan bara köra experiment på Azure Machine Learning Compute-mål. Andra beräkningsmål visas inte.

1. Ange ett namn för beräkningsresursen.

1. Välj **Spara**.

    > [!NOTE]
    > Det tar ungefär fem minuter att skapa en beräkningsresurs. När resursen har skapats kan du återanvända den och hoppa över den här väntetiden för framtida körningar.
    >
    > Beräkningsresursen skalas automatiskt till noll noder när den är inaktiv för att spara kostnader. När du använder den igen efter en fördröjning kan det uppstå ungefär fem minuters väntetid medan den skalas upp igen.

## <a name="import-data"></a>Importera data

Det finns flera exempeldatauppsättningar som ingår i designern som du kan experimentera med. För den här självstudien använder du **Automobile prisdata (Raw)**. 

1. Till vänster om pipeline-arbetsytan finns en palett med datauppsättningar och moduler. Välj **Datauppsättningar**och visa sedan avsnittet **Exempel** om du vill visa tillgängliga exempeldatauppsättningar.

1. Markera **prisdata för datauppsättningen Automobile (Raw)** och dra dem till arbetsytan.

   ![Dra data till arbetsyta](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Visualisera datan

Du kan visualisera data för att förstå den datauppsättning som du ska använda.

1. Välj modulen **Bilprisdata (Raw).**

1. Välj **Utdata**i rutan modulinformation till höger om arbetsytan .

1. Markera diagramikonen för att visualisera data.

    ![Visualisera datan](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Markera de olika kolumnerna i datafönstret om du vill visa information om var och en.

    Varje rad representerar en bil och de variabler som är associerade med varje bil visas som kolumner. Det finns 205 rader och 26 kolumner i den här datauppsättningen.

## <a name="prepare-data"></a>Förbereda data

Datauppsättningar kräver vanligtvis viss förbearbetning före analys. Du kanske har märkt några värden som saknas när du inspekterade datauppsättningen. Dessa saknade värden måste rengöras så att modellen kan analysera data korrekt.

### <a name="remove-a-column"></a>Ta bort en kolumn

När du tränar en modell måste du göra något åt data som saknas. I den här datauppsättningen saknar kolumnen **normaliserade förluster** många värden, så du utesluter den kolumnen från modellen helt och hållet.

1. Expandera avsnittet **Dataomvandling** i modulpaletten till vänster om arbetsytan och leta reda på modulen **Välj kolumner i datauppsättning.**

1. Dra modulen **Markera kolumner i datauppsättningen** till arbetsytan. Släpp modulen under datauppsättningsmodulen.

1. Anslut datauppsättningen **För bilprisdata (Rå)** till modulen **Välj kolumner i datauppsättning.** Dra från datauppsättningens utdataport, som är den lilla cirkeln längst ned i datauppsättningen på arbetsytan, till indataporten **i Välj kolumner i Datauppsättning ,** som är den lilla cirkeln högst upp i modulen.

    > [!TIP]
    > Du skapar ett dataflöde via pipelinen när du ansluter utdataporten för en modul till en indataport för en annan.
    >

    ![Anslut moduler](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Markera modulen **Välj kolumner i datauppsättning.**

1. Välj Redigera kolumn i rutan modulinformation till höger om **arbetsytan**.

1. Expandera **listrutan Kolumnnamn** bredvid **Inkludera**och markera **Alla kolumner**.

1. Välj **+** om du vill lägga till en ny regel.

1. Välj **Uteslut** och **Kolumnnamn**på de nedrullningsbara menyerna .
    
1. Ange *normaliserade förluster* i textrutan.

1. Längst ned till höger väljer du **Spara** för att stänga kolumnväljaren.

    ![Utesluta en kolumn](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Markera modulen **Välj kolumner i datauppsättning.** 

1. Markera textrutan **Kommentera** i fönstret modulinformation till höger om arbetsytan och ange *Uteslut normaliserade förluster*.

    Kommentarer visas i diagrammet som hjälper dig att organisera pipelinen.

### <a name="clean-missing-data"></a>Rensa data som saknas

Datauppsättningen saknas fortfarande när du har tagit bort kolumnen **normaliserade förluster.** Du kan ta bort återstående data som saknas med hjälp av modulen **Rensa data som saknas.**

> [!TIP]
> Rengöring av saknade värden från indata är en förutsättning för att använda de flesta modulerna i designern.

1. Expandera avsnittet **Dataomvandling**i modulpaletten till vänster om arbetsytan och hitta modulen **Rensa data som saknas.**

1. Dra modulen **Rensa data som saknas** till pipeline-arbetsytan. Anslut den till modulen **Välj kolumner i datauppsättning.** 

1. Välj modulen **Rensa data som saknas.**

1. Välj **Redigera kolumn**i informationsfönstret för modulinformation till höger om arbetsytan .

1. Expandera den nedrullnings-menyn bredvid **Inkludera**i fönstret Kolumner som ska rensas i fönstret Kolumner som ska **rensas.** Markera, **Alla kolumner**

1. Välj **Spara**

1. I rutan modulinformation till höger om arbetsytan väljer du **Ta bort hela raden** under **Rengöringsläge**.

1. Markera rutan **Kommentar** i rutan modulinformation till höger om arbetsytan och ange *Ta bort saknade värderader*. 

    Din pipeline bör nu se ut ungefär så här:
    
    ![Markera kolumn](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Träna en maskininlärningsmodell

Nu när du har modulerna på plats för att bearbeta data kan du ställa in utbildningsmodulerna.

Eftersom du vill förutsäga pris, vilket är ett tal, kan du använda en regressionsalgoritm. I det här exemplet använder du en linjär regressionsmodell.

### <a name="split-the-data"></a>Dela upp data

Att dela upp data är en vanlig uppgift i maskininlärning. Du kommer att dela upp dina data i två separata datauppsättningar. En datauppsättning kommer att träna modellen och den andra kommer att testa hur väl modellen presterade.

1. Expandera avsnittet **Dataomvandling** i modulpaletten och leta reda på modulen **Dela data.**

1. Dra modulen **Dela data** till pipeline-arbetsytan.

1. Anslut den vänstra porten för modulen **Rensa data som saknas** till modulen **Dela data.**

    > [!IMPORTANT]
    > Se till att de vänstra utmatningsportarna **i Clean Missing Data** ansluter till Delade **data**. Den vänstra porten innehåller de rensade data. Den högra porten innehåller discarted data.

1. Välj modulen **Dela data.**

1. I rutan modulinformation till höger om arbetsytan anger du **Radfraktionen i den första utdatauppsättningen** till 0,7.

    Det här alternativet delar 70 procent av data för att träna modellen och 30 procent för att testa den. Datauppsättningen på 70 procent kommer att vara tillgänglig via den vänstra utdataporten. De återstående data kommer att vara tillgängliga via rätt utdataport.

1. I rutan modulinformation till höger om arbetsytan markerar du rutan **Kommentar** och anger *Dela datauppsättningen i träningsuppsättningen (0,7) och testuppsättningen (0,3)*.

### <a name="train-the-model"></a>Träna modellen

Träna modellen genom att ge den en datauppsättning som inkluderar priset. Algoritmen konstruerar en modell som förklarar förhållandet mellan funktionerna och priset som presenteras av träningsdata.

1. Expandera **Machine Learning-algoritmer**i modulpaletten .
    
    Det här alternativet visar flera kategorier av moduler som du kan använda för att initiera utbildningsalgoritmer.

1. Välj **Regression** > **Linjär regression**och dra den till pipeline-arbetsytan.

1. Anslut utdata från den **linjära regressionsmodulen** till den vänstra ingången till modulen **Tågmodell.**

1. Expandera avsnittet **Modulutbildning**i modulpaletten och dra modulen **Tågmodell** till arbetsytan.

1. Välj modulen **Tågmodell** och dra den till pipeline-arbetsytan.

1. Anslut träningsdatautdatautdata (vänster port) i modulen **Dela data** till den högra ingången till modulen **Tågmodell.**
    
    > [!IMPORTANT]
    > Se till att de vänstra utmatningsportarna för **Split Data** ansluter till **Train Model**. Den vänstra porten innehåller träningsuppsättningen. Den högra porten innehåller testuppsättningen.

    ![Skärmbild som visar rätt konfiguration av tågmodellmodulen. Den linjära regressionsmodulen ansluter till vänster port av tågmodellmodulen och modulen Split Data ansluter till den högra hamnen i Tågmodell](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Välj **träningsmodellmodulen**.

1. Välj **Redigera kolumnväljare** i informationsfönstret för modulinformation till höger om arbetsytan.

1. Expandera den nedrullningsbara menyn i dialogrutan **Etikettkolumn** och välj **Kolumnnamn**. 

1. I textrutan anger du *pris* för att ange det värde som modellen ska förutsäga.

    >[!IMPORTANT]
    > Se till att du anger kolumnnamnet exakt. Kapitalisera inte **priset**. 

    Din pipeline ska se ut så här:

    ![Skärmbild som visar rätt konfiguration av pipelinen när du har lagt till modulen Tågmodell.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

### <a name="add-the-score-model-module"></a>Lägga till modulen Poängmodell

När du har tränat din modell med 70 procent av data kan du använda den för att få de andra 30 procenten att se hur bra din modell fungerar.

1. Ange *poängmodell* i sökrutan för att hitta modulen **Poängmodell.** Dra modulen till pipeline-arbetsytan. 

1. Anslut utdataporten för modulen **Träna modell** till den vänstra indataporten för **Poängsätta modell**. Anslut utdataporten för testning (den högra porten) för modulen **Dela data** till den högra indataporten för **Poängsätta modell**.

### <a name="add-the-evaluate-model-module"></a>Lägga till modulen Utvärdera modell

Använd modulen **Utvärdera modell** för att utvärdera hur väl din modell gjorde testet datauppsättningen.

1. Ange *utvärdera* i sökrutan för att hitta modulen **Utvärdera modell.** Dra modulen till pipeline-arbetsytan. 

1. Anslut utdata från **poängmodellmodulen** till den vänstra ingången **i Utvärdera modell**. 

    Den slutliga rörledningen bör se ut ungefär så här:

    ![Skärmbild som visar rätt konfiguration av pipelinen.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="submit-the-pipeline"></a>Skicka pipelinen

Nu när din pipeline är konfigurerad kan du skicka in en pipeline-körning för att träna din maskininlärningsmodell. Du kan skicka en pipeline-körning när som helst när du skapar pipelines i designern. Du kan göra detta för att kontrollera ditt arbete när du går för att verifiera din pipeline fungerar som förväntat.

1. Högst upp på arbetsytan väljer du **Skicka**.

1. Välj **Skapa nytt**i dialogrutan **Konfigurera pipelinekörning** .

    > [!NOTE]
    > Experiment grupp liknande pipeline körs tillsammans. Om du kör en pipeline flera gånger kan du välja samma experiment för efterföljande körningar.

    1. Ange ett beskrivande namn för **Nytt experimentnamn**.

    1. Välj **Skicka**.
    
    Du kan visa körstatus och information längst upp till höger på arbetsytan.

### <a name="view-scored-labels"></a>Visa poängsatta etiketter

När körningen är klar kan du visa resultatet av pipeline-körningen. Titta först på de förutsägelser som genereras av regressionsmodellen.

1. Välj modulen **Poängmodell** om du vill visa dess utdata.

1. I rutan modulinformation till höger om arbetsytan väljer du ![ **Utdata** ](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) > diagramikon visualisera ikonen för att visa resultat.

    Här kan du se de förväntade priserna och de faktiska priserna från testdata.

    ![Skärmbild av utdatavisualiseringen som markerar kolumnen Poängsatt etikett](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Utvärdera modeller

Använd **utvärdera modellen** för att se hur väl den tränade modellen presterade på testdatauppsättningen.

1. Välj modulen **Utvärdera modell** om du vill visa utdata.

1. I rutan modulinformation till höger om arbetsytan väljer du ![ **Utdata** > diagramikon visualisera ikonen](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) för att visa resultat.

Följande statistik visas för din modell:

* **Genomsnittligt absolut fel (MAE)**: Medelvärdet av absoluta fel. Ett fel är skillnaden mellan det förväntade värdet och det faktiska värdet.
* **Root Mean Squared Error (RMSE)**: Kvadratroten för medelvärdet av kvadratfel för förutsägelser som gjorts på testdatauppsättningen.
* **Relativa absoluta fel**: Medelvärdet av absoluta fel i förhållande till den absoluta skillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
* **Relativa kvadratfel**: Medelvärdet av kvadratfel i förhållande till kvadratskillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
* **Bestämningskoefficient**: Även känt som R-kvadratvärdet anger det här statistiska måttet hur väl en modell passar data.

För all felstatistik gäller att mindre är bättre. Ett mindre värde anger att förutsägelserna ligger närmare de faktiska värdena. För bestämningskoefficienten är ju närmare dess värde är till en (1,0), desto bättre förutsägelser.

## <a name="clean-up-resources"></a>Rensa resurser

Hoppa över det här avsnittet om du vill fortsätta med del 2 i självstudien, [distribuera modeller](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I del två får du lära dig hur du distribuerar din modell som slutpunkt i realtid.

> [!div class="nextstepaction"]
> [Fortsätt att distribuera modeller](tutorial-designer-automobile-price-deploy.md)
