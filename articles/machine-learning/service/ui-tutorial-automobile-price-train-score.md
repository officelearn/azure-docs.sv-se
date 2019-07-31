---
title: 'Självstudier: Förutsäg bil pris med Visual Interface'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du tränar, poängs ätter och distribuerar en maskin inlärnings modell med hjälp av ett dra och släpp-visuellt gränssnitt. Den här självstudien är en del av en serie i två delar om förutsägelse av bil priser med linjär regression.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/21/2019
ms.openlocfilehash: b0d227b71677db1d6b4ce8386b02cf957ca259f7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668411"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Självstudier: Förutsäg bil pris med Visual Interface

I den här självstudien får du lära dig hur du använder det visuella gränssnittet Azure Machine Learning tjänsten för att utveckla och distribuera en förutsägelse analys lösning som förutsäger priset på en bil. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

I del ett konfigurerar du din miljö, drar och släpper data uppsättningar och analys moduler till en interaktiv arbets yta och kopplar ihop dem för att skapa ett experiment. 

I del ett av självstudien får du lära dig att:

> [!div class="checklist"]
> * Importera och rensa data
> * Träna en Machine Learning-modell
> * Poäng och utvärdera en modell

I [del två](ui-tutorial-automobile-price-deploy.md) av självstudien får du lära dig hur du distribuerar din förutsägelse modell som en Azure-webbtjänst så att du kan använda den för att förutsäga priset på en bil baserat på tekniska specifikationer som du skickar den. 

En slutförd version av den här självstudien är tillgänglig som ett exempel experiment.

Du hittar det genom att välja **Lägg till ny**på  ****sidan experiment**och sedan välja 1-regression: Test av bil pris förutsägelse (grundläggande** ).

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Om du har en Azure Machine Learning service-arbetsyta går du vidare till nästa avsnitt.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-new-experiment"></a>Skapa nytt experiment

1. Öppna din arbets yta i [Azure Portal](https://portal.azure.com/).

1. Välj **visuellt gränssnitt**i arbets ytan. Välj sedan **Starta visuellt gränssnitt**. 

    ![Skärm bild av Azure Portal som visar hur du får åtkomst till det visuella gränssnittet från en Machine Learning service-arbetsyta](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

1. Skapa ett nytt experiment genom att välja **+ nytt** längst ned i fönstret med visuella gränssnitt.

1. Välj **Tom experiment**.

1. Välj standard experiment namnet **"experiment som skapats på...** " överst på arbets ytan och Byt namn på det till något meningsfullt. Till exempel **"pris förutsägelse för bil"** . Namnet behöver inte vara unikt.

## <a name="specify-data"></a>Ange data

Maskin inlärningen är beroende av data. Som tur är finns flera exempel data uppsättningar som ingår i det här gränssnittet och som du kan använda för att experimentera med. I den här självstudien använder du data för exempel data uppsättning **bil (RAW)** . 

1. Till vänster om arbetsytan för experimentet finns en palett med datauppsättningar och moduler. Välj **sparade data uppsättningar** och välj sedan **exempel** för att visa tillgängliga exempel data uppsättningar.

1. Välj data uppsättningen, **bil pris data (RAW)** och dra den till arbets ytan.

   ![Dra data till arbets yta](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

1. Välj vilka kolumner med data som ska användas. Skriv **Select** (Sök) i sökrutan överst på paletten för att hitta modulen **Välj kolumner i data uppsättning** .

1. Klicka och dra modulen **Välj kolumner i data uppsättning** till arbets ytan. Släpp modulen under data uppsättnings modulen.

1. Anslut den data uppsättning som du lade till tidigare till modulen **Välj kolumner i data uppsättning** genom att klicka och dra. Dra från data uppsättningens utdataport, som är den lilla cirkeln längst ned i data uppsättningen på arbets ytan, till Indataporten för **utvalda kolumner i data uppsättningen**, som är den lilla cirkeln överst i modulen.

    > [!TIP]
    > Du skapar ett data flöde genom experimentet när du ansluter utdataporten för en modul till en annan indataport.
    >

    ![Anslut moduler](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    Det röda utrops tecknet indikerar att du inte har angett egenskaperna för modulen än.

1. Välj modulen **Välj kolumner i data uppsättning** .

1. I fönstret **Egenskaper** till höger om arbets ytan väljer du **Redigera kolumner**.

    I dialog rutan **Välj kolumner** väljer du **alla kolumner** och inkluderar **alla funktioner**. Dialogrutan bör se ut så här:

     ![kolumn-väljare](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. Klicka på **OK** längst ned till höger för att stänga kolumn väljaren.

## <a name="run-the-experiment"></a>Kör experimentet

Du kan när som helst klicka på utdataporten för en data uppsättning eller modul för att se hur data ser ut som vid den punkten i data flödet. Om alternativet **visualisera** är inaktiverat, måste du först köra experimentet.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Experimentet körs när Compute-målet är tillgängligt. När körningen är klar visas en grön bock markering i varje modul.


## <a name="visualize-the-data"></a>Visualisera datan

Nu när du har kört det första experimentet kan du visualisera data för att förstå mer om den data uppsättning som du har.

1. Välj utdataporten längst ned i **Välj kolumner i data uppsättningen** och välj sedan **visualisera**.

1. Klicka på olika kolumner i fönstret data för att visa information om den kolumnen.

    I den här datamängden representerar varje rad en bil, och de variabler som är associerade med varje bil visas som kolumner. Det finns 205 rader och 26 kolumner i den här data uppsättningen.

     Varje gången du klickar på en kolumn med data visas **statistik** och **visualiserings** bilden för den kolumnen till vänster. Om du till exempel klickar på antalet antal **dörrar** visas det två unika värden och två värden som saknas. Rulla nedåt för att se värdena: två och fyra dörrar.

     ![Förhandsgranska data](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. Klicka på varje kolumn för att lära dig mer om din data uppsättning och fundera över om dessa kolumner kan vara användbara för att förutsäga priset på en bil.

## <a name="prepare-data"></a>Förbereda data

En data uppsättning kräver vanligt vis lite för bearbetning innan den kan analyseras. Du kanske har lagt märke till att värden saknas vid visualisering av data uppsättningen. Dessa värden som saknas måste rensas bort så att modellen kan analysera informationen korrekt. Du tar bort alla rader som saknar värden. Dessutom har kolumnen **normaliserade förluster** en stor del av saknade värden, så du kommer att utesluta den kolumnen från modellen helt och hållet.

> [!TIP]
> Du måste rensa värden som saknas i indata för att kunna använda de flesta moduler.

### <a name="remove-column"></a>Ta bort kolumn

Ta först bort de **normaliserade förluster-** kolumnen helt.

1. Välj modulen **Välj kolumner i data uppsättning** .

1. I fönstret **Egenskaper** till höger om arbets ytan väljer du **Redigera kolumner**.

    * Lämna **med regler** och **alla kolumner** markerade.

    * I listrutorna väljer du **Exkludera** och **kolumnnamn** och klickar sedan i textrutan. Typ **normaliserad-förluster**.

    * Klicka på **OK** längst ned till höger för att stänga kolumn väljaren.

    ![Undanta en kolumn](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Nu visar egenskaps rutan för Välj kolumner i data uppsättningen att den kommer att gå igenom alla kolumner från data uppsättningen, förutom **normaliserade förluster**.
        
    Rutan egenskaper visar att kolumnen **normaliserade förluster** är exkluderad.
        
    ![Egenskaps fönster](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    Du kan lägga till en kommentar till en modul genom att dubbelklicka på modulen och skriva text. På så sätt kan du snabbt se vad modulen gör i experimentet. 

1. Dubbelklicka på modulen **Välj kolumner i data uppsättning** och Skriv kommentaren "exkludera normaliserade förluster". 
    
    När du har skrivit kommentaren klickar du utanför modulen. En nedåtpil visas för att visa att modulen innehåller en kommentar.

1. Klicka på nedåtpilen för att visa kommentaren.

    Modulen visar nu en UPPIL för att dölja kommentaren.
        
    ![Kommentar](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Rensa data som saknas

När du tränar en modell måste du göra något om de data som saknas. I det här fallet lägger du till en modul för att ta bort eventuella återstående rader som saknar data.

1. Skriv **Rensa** i sökrutan för att hitta modulen **Rensa data som saknas** .

1. Dra modulen **Rensa data som saknas** till experimentets arbets yta och Anslut den till modulen **Välj kolumner i data uppsättning** . 

1. I fönstret Egenskaper väljer du **ta bort hela raden** under **rensnings läge**.

1. Dubbelklicka på modulen och skriv kommentaren ”Ta bort rader med värden som saknas”.
 
    ![Ta bort rader](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    Experimentet bör nu se ut ungefär så här:
    
    ![Select-Column](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="train-the-model"></a>Träna modellen

Nu när data är klara kan du skapa en förutsägelse modell. Du ska använda dina data för att träna modellen. Sedan testar du modellen för att se hur nära den kan förutsäga priser.

**Klassificering** och **regression** är två typer av övervakade Machine Learning-algoritmer. **Klassificering** förutsäger ett svar från en definierad uppsättning kategorier, till exempel en färg (röd, blå eller grön). **Regression** används för att förutsäga ett tal.

Eftersom du vill förutsäga pris, vilket är ett tal, kan du använda en Regressions algoritm. I det här exemplet ska du använda en linjär Regressions modell.

Träna modellen genom att ge den en uppsättning data som inkluderar priset. Modellen söker igenom data och söker efter korrelationer mellan en bils funktioner och dess pris.

Använd dina data för att både träna modellen och testa den genom att dela upp data i separata data uppsättningar för utbildning och testning.

1. Skriv **dela data** i sökrutan för att hitta modulen **dela data** och Anslut den till den vänstra porten i modulen **Rensa data som saknas** .

1. Välj modulen **dela data** . I fönstret Egenskaper ställer du in bråk talet i den första data uppsättningen för utdata till 0,7. På så sätt kommer vi att använda 70 procent av data för att träna modellen och hålla 30 procent för testning.

    ![Skärm bild som visar rätt konfiguration för fönstret Egenskaper. Värdena för "dela data" ska vara "delade rader", 0,7, slumpmässig Split, 0, false.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Dubbelklicka på **dela data** och Skriv kommentaren "dela in data uppsättningen i Training set (0,7) och test uppsättning (0,3)"

1. Om du vill välja Learning-algoritmen rensar du sökrutan för modulens palett.

1. Expandera **Machine Learning** expandera sedan **initiera modell**. Nu visas flera kategorier av moduler som kan användas för att initiera algoritmer för Machine Learning.

1. För det här experimentet väljer du **regression** > **linjär regression** och drar den till experimentets arbets yta.

    ![Skärm bild som visar rätt konfiguration för fönstret Egenskaper. Värdena för "dela data" ska vara "delade rader", 0,7, slumpmässig Split, 0, false.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Leta upp och dra modulen **träna modell** till arbets ytan för experimentet. Anslut utdataporten för modulen linjär regression till vänster indata för modulen träna modell och Anslut utbildnings data utmatningen (den vänstra porten) för modulen **dela data** till rätt indata för modulen **träna modell** .

    ![Skärm bild som visar korrekt konfiguration av modulen träna modell. Modulen linjär regression ansluter till den vänstra porten för modulen träna modell och modulen dela data ansluts till rätt port för träna modell](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Välj modulen **träna modell** . I fönstret Egenskaper väljer du starta kolumn väljare och anger sedan **pris** bredvid **Inkludera kolumn namn**. Pris är det värde som din modell ska förutsäga

    ![Skärm bild som visar rätt konfiguration för modulen för kolumn väljare. Med regler > Inkludera kolumn namn > "pris"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Ditt experiment bör se ut så här:

    ![Skärm bild som visar rätt konfiguration av experimentet när du har lagt till modulen träna modell.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

## <a name="score-and-evaluate-the-model"></a>Poäng och utvärdera modellen

Nu när du har tränat modellen med 70 procent av dina data kan du använda den för att visa de andra 30 procenten av data för att se hur väl modellen fungerar.

1. Skriv **poängsättnings modell** i sökrutan för att hitta modulen **Poäng modell** och dra modulen till experimentets arbets yta. Anslut utdata från modulen **träna modell** till den vänstra Indataporten för **Poäng modell**. Anslut test data utmatningen (höger port) för modulen **dela data** till den högra Indataporten för **Poäng modellen**.

1. Skriv **utvärdera** i sökrutan för att hitta **utvärderings modellen** och dra modulen till experimentets arbets yta. Anslut utdata från modulen **Poäng modell** till den vänstra inmatningen av **utvärdera modell**. Det slutliga experimentet bör se ut ungefär så här:

    ![Skärm bild som visar den slutliga korrekta konfigurationen av experimentet.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Kör experimentet med beräknings resursen som du skapade tidigare.

1. Visa utdata från modulen **Poäng modell** genom att välja utdataporten för **Poäng modell** och välj **visualisera**. Utdata innehåller de förväntade värdena för pris och de kända värdena från testdata.

    ![Skärm bild av utmatnings visualiseringen som har markerat kolumnen "score etikett"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Om du vill visa utdata från modulen **utvärdera modell** väljer du utdataporten och väljer sedan **visualisera**.

    ![Skärm bild som visar utvärderings resultatet för det slutliga experimentet.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Följande statistik visas för din modell:

* **Medelvärde för absolut fel (Mae)** : Medelvärdet av absoluta fel (ett fel är skillnaden mellan det förväntade värdet och det faktiska värdet).
* **Rot genomsnitts fel (rmse)** : Kvadratroten av genomsnittet av kvadratfel i förutsägelser som görs mot testdatauppsättningen.
* **Relativt absolutfel**: Medelvärdet av absoluta fel i förhållande till den absoluta skillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
* **Relativa kvadratfel**: Medelvärdet av kvadratfel i förhållande till kvadratskillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
* **Bestämningskoefficient**: Det kallas även för R-kvadratvärdet, det här är ett statistiskt mått som anger hur väl en modell passar data.

För all felstatistik gäller att mindre är bättre. Ett mindre värde anger att förutsägelser bättre överensstämmer med de faktiska värdena. För att fastställa koefficienten är det närmare värdet för en (1,0), desto bättre förutsägelser.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Hantera experiment i Azure Machine Learning service-arbetsyta

Experiment som du skapar i det visuella gränssnittet kan hanteras från arbets ytan Azure Machine Learning tjänst. Använd arbets ytan om du vill se mer detaljerad information, till exempel enskilda personer experiment körningar, diagnostikloggar, körnings diagram och mycket annat.

1. Öppna din arbets yta i [Azure Portal](https://portal.azure.com/).  

1. Välj **experiment**i arbets ytan. Välj sedan experimentet som du skapade.

    ![Skärm bild som visar hur du navigerar till experiment i Azure Portal](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    På den här sidan ser du en översikt över experimentet och de senaste körningarna.

    ![Skärm bild som visar en översikt över experiment statistik i Azure Portal](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Välj ett körnings nummer om du vill visa mer information om en speciell körning.

    ![Detaljerad rapport om skärm bilds körning](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Körnings rapporten uppdateras i real tid. Om du använde ett **Kör Python-skript** eller **Kör R-skript** i experimentet kan du ange skript loggar som ska matas ut på fliken **loggar** .

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I del ett av de här självstudierna slutförde du följande steg:

* Skapat ett experiment
* Förbereda data
* Träna modellen
* Poäng och utvärdera modellen

I del två får du lära dig hur du distribuerar din modell som en Azure-webbtjänst.

> [!div class="nextstepaction"]
> [Fortsätt till att distribuera modeller](ui-tutorial-automobile-price-deploy.md)
