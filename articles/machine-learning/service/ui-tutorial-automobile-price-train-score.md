---
title: 'Självstudier: Förutsäga bil pris med det visuella gränssnittet'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du tränar poäng, och distribuerar en maskininlärningsmodell med en dra och släpp visuella gränssnittet. Den här självstudien är del i en serie i två delar om att förutsäga bilpriser med hjälp av linjär regression.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 21f5a2d93b708e93f124bd44177bb7852dfbd86a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720525"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Självstudier: Förutsäga bil pris med det visuella gränssnittet

I den här självstudien får titta du närmare på att utveckla en förutsägande lösning i det visuella gränssnittet för Azure Machine Learning-tjänsten. I slutet av den här självstudien har du en lösning som kan förutsäga priset på en bil baserat på tekniska specifikationer som du skickar den.

I den första delen av kursen lär du dig hur du:

> [!div class="checklist"]
> * Import- och rensa data
> * Träna en modell för maskininlärning
> * Poängsätta och utvärdera en modell

I [del två](ui-tutorial-automobile-price-deploy.md) av självstudiekursen ska du lär dig hur du distribuerar din förutsägelsemodell som en Azure-webbtjänst.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

En fullständig version av den här självstudien är tillgänglig som en exempelexperimentet.

Du hittar det genom från den **experiment sidan**väljer **Lägg till ny**och välj sedan den **exempel 1 – Regression: Bil pris Prediction(Basic)** experimentera.

## <a name="create-a-workspace"></a>Skapa en arbetsyta

Om du har en arbetsyta för Azure Machine Learning-tjänsten kan gå vidare till den [nästa avsnitt](#open-the-visual-interface-webpage).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Öppna visuella gränssnittet-webbsida

1. Öppna din arbetsyta i den [Azure-portalen](https://portal.azure.com/).

1. På arbetsytan, Välj **visuella gränssnittet**. Välj sedan **starta visuella gränssnittet**. 

    ![Skärmbild av Azure-portalen som visar hur du kommer åt det visuella gränssnittet från en arbetsyta för Machine Learning-tjänsten](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

## <a name="create-your-first-experiment"></a>Skapa ditt första experiment

Verktyget visuella gränssnittet innehåller en interaktiv, visuell plats för att skapa förutsägande analysmodeller. Dra och släpp datauppsättningar och analys moduler till en interaktiv och koppla dem för att skapa en *experimentera*.

1. Skapa ett nytt experiment genom att välja **+ ny** längst ned i fönstret visuella gränssnittet.

    ![Lägg till nytt experiment](./media/ui-tutorial-automobile-price-train-score/add-new.png)

1. Välj **tomt Experiment**.

1. Välj det fördefinierade experimentnamnet **”redan experimenterat lite skapas på...** ”överst på arbetsytan och Byt namn på den till något beskrivande. Till exempel **bilpriser**. Namnet behöver inte vara unikt.

## <a name="add-data"></a>Lägg till data

Det första du behöver för machine learning är data. Det finns flera provdatauppsättningar som ingår i det här gränssnittet som du kan använda. Du kan även importera data från befintliga källor. Den här självstudien använder du exempeldatauppsättningen **Automobile price data (Raw)** . 

1. Till vänster om arbetsytan för experimentet finns en palett med datauppsättningar och moduler. Välj **sparade datauppsättningar** därefter **exempel** att visa tillgängliga exempeldatauppsättningarna.

1. Välj sedan datamängden **Automobile price data (raw)** , och dra den till arbetsytan.

   ![Dra data på ytan](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

## <a name="select-columns"></a>Välja kolumner

Välj vilka kolumner med data du arbetar med. Konfigurera att börja med modulen för att visa alla tillgängliga kolumner.

> [!TIP]
> Om du känner till namnet på data eller modulen som du vill använda sökfältet överst på modulpaletten för att hitta det snabbt. Resten av kursen kommer att använda den här genvägen.


1. Typ **Välj** i sökrutan för att hitta den **Välj kolumner i datauppsättning** modulen.

1. Klicka och dra den **Välj kolumner i datauppsättning** till arbetsytan. Ta bort modulen nedan datauppsättningen som du lade till tidigare.

1. Ansluta datauppsättningen till den **Välj kolumner i datauppsättning**: Klicka på utdataporten för datauppsättningen genom att dra till indataporten för **Välj kolumner i datauppsättning**, släpper musknappen. Datamängden och modulen förblir anslutna även om du flyttar runt endera av dem på arbetsytan.

    > [!TIP]
    > Datauppsättningar och moduler har ingångs- och utgångsportar som representeras av små cirklar – ingångsportar högst upp och utgångsportar längst ned. Du skapar ett flöde av data via ditt experiment när du ansluter utdataporten för en modul till en port på en annan.
    >

    ![Ansluta moduler](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    Rött utropstecken anger att du inte ställer in egenskaperna för modulen ännu.

1. Välj den **Välj kolumner i datauppsättning** modulen.

1. I den **egenskaper** rutan till höger om arbetsytan, väljer **Redigera kolumner**.

    I den **Markera kolumner** dialogrutan Välj **alla kolumner** och inkludera **alla funktioner**. Dialogrutan bör se ut så här:

     ![column-selector](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. Längst ned till höger, Välj **OK** att Stäng kolumnväljaren.

## <a name="run-the-experiment"></a>Kör experimentet

Klicka på utdataporten för en datauppsättning eller en modul för att se hur data ser ut vid den tidpunkten i dataflödet när som helst. Om den **visualisera** alternativet är inaktiverat, måste du först kör experimentet.

Ett experiment körs på ett beräkningsmål, vilket är en beräkningsresurs som är kopplad till din arbetsyta. När du skapar ett beräkningsmål kan återanvända du den för framtida körningar.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

När beräkningsmål är tillgängligt, kör experimentet. När körningen är klar visas en grön bock på varje modul.


## <a name="preview-the-data"></a>Förhandsgranska data

Nu när du har kört ditt första experiment kan visualisera du data om du vill veta mer om datauppsättningen som du måste arbeta med.

1. Välj på utdataporten längst ned på den **Välj kolumner i datauppsättning** därefter **visualisera**.

1. Klicka på olika kolumner i datafönstret att visa information om den kolumnen.

    I den här datamängden representerar varje rad en bil, och de variabler som är associerade med varje bil visas som kolumner. Det finns 205 rader och 26 kolumner i den här datauppsättningen.

     Varje gång du klickar på en kolumn med data, den **statistik** information och **visualisering** bild av kolumnen visas till vänster. Till exempel när du klickar på **num av dörrar** du se att den har 2 unika värden och 2 värden som saknas. Rulla ned för att se värdena: två till fyra dörrar.

     ![Förhandsgranska data](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. Klicka på varje kolumn för att förstå mer om din datauppsättning och tänka på om de här kolumnerna ska vara användbar för att förutsäga priset på en bil.

## <a name="prepare-data"></a>Förbereda data

En datauppsättning kräver vanligtvis viss bearbetning i förväg innan den kan analyseras. Du kanske har märkt några värden som saknas när visualisera datauppsättningen. Dessa värden som saknas måste rensas bort så att modellen kan analysera informationen korrekt. Du tar bort alla rader som innehåller värden som saknas. Dessutom den **normalized-losses** kolumn har en stor del av saknade värden, så att du ska utesluta kolumnen från modellen helt och hållet.

> [!TIP]
> Du måste rensa värden som saknas i indata för att kunna använda de flesta moduler.

### <a name="remove-column"></a>Ta bort kolumn

Ta först bort de **normalized-losses** kolumn helt.

1. Välj den **Välj kolumner i datauppsättning** modulen.

1. I den **egenskaper** rutan till höger om arbetsytan, väljer **Redigera kolumner**.

    * Lämna **med regler** och **alla kolumner** valda.

    * I listrutorna väljer du **Exkludera** och **kolumnnamn** och klickar sedan i textrutan. Typ **normalized-losses**.

    * Längst ned till höger, Välj **OK** att Stäng kolumnväljaren.

    ![Undanta en kolumn](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Nu egenskapsfönstret för Välj kolumner i datauppsättning anger att den släpps igenom alla kolumner i datauppsättningen tas med utom **normalized-losses**.
        
    Egenskapsrutan visar att den **normalized-losses** utesluta kolumnen.
        
    ![Egenskapsfönster](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    Du kan lägga till en kommentar till en modul genom att dubbelklicka på modulen och skriva text. På så sätt kan du snabbt se vad modulen gör i experimentet. 

1. Dubbelklicka på den **Välj kolumner i datauppsättning** modulen och skriver kommentaren ”exkludera normaliserade förluster”. 
    
    När du skriver kommentaren, klickar du på utanför modulen. En nedpil visas att modulen innehåller en kommentar.

1. Klicka på nedåt-pilen för att visa kommentaren.

    Modulen visas nu en upp-pilen för att dölja kommentaren.
        
    ![Kommentar](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Rensa data som saknas

När du tränar en modell som du behöver göra något om de data som saknas. I det här fallet lägger du till en modul för att ta bort återstående rader där data saknas.

1. Typ **Rengör** i sökrutan för att hitta den **Rensa Data som saknas** modulen.

1. Dra den **Rensa Data som saknas** arbetsytan och koppla den till modulen till arbetsytan för experimentet den **Välj kolumner i datauppsättning** modulen. 

1. I fönstret Egenskaper väljer **ta bort hela raden** under **Rensningsläge**.

    Dessa alternativ direkt **Rensa Data som saknas** att rensa data genom att ta bort rader som har värden som saknas.

1. Dubbelklicka på modulen och skriv kommentaren ”Ta bort rader med värden som saknas”.
 
    ![Ta bort rader](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    Experimentet bör nu se ut ungefär så här:
    
    ![Välj kolumner](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="visualize-the-results"></a>Visualisera resultat

Eftersom du har gjort ändringar i moduler i ditt experiment, har status ändrats till ”draft”.  Om du vill visualisera informationen som nya ren först du köra experimentet igen.

1. Välj **kör** längst ned för att köra experimentet.

    Nu kan du återanvända beräkningsmål som du skapade tidigare.

1. Välj **kör** i dialogrutan.

   ![Kör experimentet](./media/ui-tutorial-automobile-price-train-score/select-compute.png)

1. När körningen är klar högerklickar du på den **Rensa Data som saknas** modul för att visualisera nya rensa data.

    ![Visualisera rensa data](./media/ui-tutorial-automobile-price-train-score/visualize-cleaned.png)

1. Klicka på olika kolumner i fönstret rensade data för att se hur data har ändrats.

    ![Visualisera Rensa Data](media/ui-tutorial-automobile-price-train-score/visualize-result.png)

    Det finns nu 193 rader och kolumner som är 25.

    När du klickar på **num av dörrar** du ser den fortfarande har 2 unika värden men nu har 0 värden som saknas. Klicka dig igenom resten av kolumnerna för att se att det inte finns några värden som saknas kvar i datauppsättningen. 

## <a name="train-the-model"></a>Träna modellen

Nu när data är klar, kan du skapa en förutsägelsemodell. Du använder dina data för att träna modellen. Du kan sedan testa modellen för att se hur väl den kan förutsäga priser.

**Klassificering** och **regression** är två typer av övervakade Machine Learning-algoritmer. **Klassificering** förutsäger ett svar från en definierad uppsättning kategorier, till exempel en färg (röd, blå eller grön). **Regression** används för att förutsäga ett tal.

Eftersom du vill förutsäga pris, vilket är ett tal, kan du använda en regressionsalgoritm. I det här exemplet ska du använda en linjär regressionsmodell.

Träna modellen genom att ge den en uppsättning data som innehåller priset. Modellen söker igenom data och letar efter samband mellan en bils funktioner och dess pris. Sedan testa modellen genom att ge den en uppsättning funktioner för bilar som det är bekant med och se hur nära modellen kommer att förutsäga det kända priset.

Använd dina data för både träna modellen och testa det genom att dela data i separata tränings- och testningsdatauppsättningar.

1. Typ **dela data** i sökrutan för att hitta den **dela Data** modulen och ansluter den till den vänstra porten för den **Rensa Data som saknas** modulen.

1. Välj den **dela Data** modulen du just anslutit för att markera den. Ange andelen av rader i den första utdatauppsättningen till 0,7 i fönstret Egenskaper. På så sätt kan vi använda 70 procent av data för att träna modellen och lämna 30 procent för testning.

    ![Skärmbild som visar egenskapsfönstret är korrekt konfigurerat. Värdena för ”dela Data” ska vara ”dela rader”, 0,7, slumpmässig dela, 0, False.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Dubbelklicka på den **dela Data** och skriver kommentaren ”dela upp datauppsättningen i utbildning set(0.3) set(0.7) och testning”

1. Välj Inlärningsalgoritmen genom att avmarkera modulpaletten Sök-rutan.

1. Expandera den **Maskininlärning** Expandera **initiera modell**. Nu visas flera kategorier av moduler som kan användas för att initiera algoritmer för Machine Learning.

1. Det här experimentet väljer **Regression** > **linjär Regression** och drar den till arbetsytan för experimentet.

    ![Skärmbild som visar egenskapsfönstret är korrekt konfigurerat. Värdena för ”dela Data” ska vara ”dela rader”, 0,7, slumpmässig dela, 0, False.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Leta upp och dra den **Träningsmodell** modulen till experimentarbetsytan. Anslut utdataporten för modulen linjär Regression till den vänstra indataporten för modulen träna modell och Anslut träningsresultatet (vänster port) för den **dela Data** modul till den högra indataporten för den **Träningsmodell**modulen.

    ![Skärmbild som visar träningsmodellmodulen är korrekt konfigurerat. Linjär Regression modulen ansluter till vänster port av träningsmodellmodulen och dela Data modulen ansluter till rätt port för träna modell](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Välj den **Träningsmodell** modulen. I fönstret Egenskaper väljer Starta kolumnväljaren och skriv sedan **pris** bredvid **innehålla kolumnnamn**. Priset är det värde som din modell ska förutsäga

    ![Skärmbild som visar korrekt konfiguration för modulen kolumnen väljare. Med regler > Inkludera kolumnnamn > ”price”](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Experimentet bör se ut så här:

    ![Skärmbild som visar experimentet när du lägger till träningsmodellmodulen är korrekt konfigurerat.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Kör träningsexperimentet

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Poängsätta och utvärdera modellen

Nu när du har tränat modellen med 70 procent av dina data, du kan använda den för att bedöma 30 procent av data och se hur bra modell fungerar.

1. Typ **poängmodell** i sökrutan för att hitta den **Poängmodell** modulen och dra modulen till experimentarbetsytan. Anslut utdataporten för den **Träningsmodell** modul till vänstra indataporten för **Poängmodell**. Anslut utdataporten för testning (den högra porten) för den **dela Data** indataporten för modulen till höger för **Poängmodell**.

1. Typ **utvärdera** i sökrutan för att hitta den **utvärdera modell** och dra modulen till experimentarbetsytan. Anslut utdataporten för den **Poängmodell** den vänstra indataporten för modulen **utvärdera modell**. Det slutliga experimentet bör se ut ungefär så här:

    ![Skärmbild som visar den slutliga korrekt konfigurationen av experimentet.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Kör experimentet med hjälp av samma beräkningsmål som använts tidigare.

1. Visa utdata från den **Poängmodell** modulen genom att välja utdataporten för **Poängmodell** och välj **visualisera**. Utdata innehåller de förväntade värdena för pris och de kända värdena från testdata.

    ![Skärmbild av utdata visualiseringen markering kolumnen ”poängsätts etikett”](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Om du vill visa utdata från modulen utvärdera modell, Välj på utdataporten och välj sedan visualisera.

    ![Skärmbild som visar utvärderingsresultatet för det slutliga experimentet.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

För din modell visas följande statistik:

* **Medelabsolutfel (MAE)** : Medelvärdet av absoluta fel (ett fel är skillnaden mellan det förväntade och faktiska värdet).
* **Rot medelvärdet cirkels fel (RMSE)** : Kvadratroten av genomsnittet av kvadratfel i förutsägelser som görs mot testdatauppsättningen.
* **Relativt absolutfel**: Medelvärdet av absoluta fel i förhållande till den absoluta skillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
* **Relativa kvadratfel**: Medelvärdet av kvadratfel i förhållande till kvadratskillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
* **Bestämningskoefficient**: Även känt som R-kvadratvärdet, det här är ett statistiskt mått som anger hur väl en modell passar data.

För all felstatistik gäller att mindre är bättre. Ett mindre värde anger att förutsägelser bättre överensstämmer med de faktiska värdena. För Bestämningskoefficient, ju närmare värdet är ett (1,0), desto bättre förutsägelser.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Hantera experiment i Azure Machine Learning-tjänstens arbetsyta

Experiment som du skapar i det visuella gränssnittet kan hanteras från arbetsytan Azure Machine Learning-tjänsten. Använda arbetsytan för att se mer detaljerad information, t.ex. individer experiment körs, diagnostikloggar och diagram för körning.

1. Öppna din arbetsyta i den [Azure-portalen](https://portal.azure.com/).  

1. På arbetsytan, Välj **experiment**. Välj sedan experimentet som du skapade.

    ![Skärmbild som visar hur du navigerar till experiment i Azure portal](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    På den här sidan visas en översikt över experimentet och dess senaste körningar.

    ![Skärmbild som visar översikt över experiment i Azure portal](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Välj en körning nummer vill ha mer information om specifika körningen.

    ![Skärmbild detaljerad kör rapporten](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Kör rapporten uppdateras i realtid. Om du har använt en **kör Python-skript** modul i ditt experiment som du kan ange skript loggar till utdata i den **loggar** fliken.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I del ett av den här självstudien får slutfört du de här stegen:

* Skapat ett experiment
* Förbereda data
* Träna modellen
* Poängsätta och utvärdera modellen

I del två lär du dig att distribuera din modell som en Azure-webbtjänst.

> [!div class="nextstepaction"]
> [Fortsätta att distribuera modeller](ui-tutorial-automobile-price-deploy.md)
