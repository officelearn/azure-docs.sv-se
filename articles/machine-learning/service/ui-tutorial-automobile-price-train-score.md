---
title: 'Självstudier: Förutsäga bil pris med det visuella gränssnittet'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du tränar poäng, och distribuerar en maskininlärningsmodell med en dra och släpp visuella gränssnittet. Den här självstudien är del i en serie i två delar om att förutsäga bilpriser med hjälp av linjär regression.
author: peterlu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 1dc905b2ac89667dcb5800b0f539cb3e9f3acdcd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026874"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Självstudier: Förutsäga bil pris med det visuella gränssnittet

I den här självstudien får titta du närmare på att utveckla en förutsägande lösning i det visuella gränssnittet för Azure Machine Learning-tjänsten. I slutet av den här självstudien har du en lösning som kan förutsäga priset på en bil baserat på tekniska specifikationer som du skickar den.

Den här självstudien [fortsätter från snabbstarten](ui-quickstart-run-experiment.md) och är **del ett i en självstudieserie i två delar**. Dock har du inte Slutför Snabbstart innan du börjar.

I den första delen av självstudieserien lär du dig hur du:

> [!div class="checklist"]
> * Importera och rensa data (samma steg som snabbstarten)
> * Träna en modell för maskininlärning
> * Poängsätta och utvärdera en modell

I [del två](ui-tutorial-automobile-price-deploy.md) av den här självstudieserien du lär dig hur du distribuerar din förutsägelsemodell som en Azure-webbtjänst.

> [!NOTE]
> En fullständig version av den här självstudien är tillgänglig som en exempelexperimentet.
> Gå till sidan experiment **Lägg till ny** > **exempel 1 – Regression: Bil pris Prediction(Basic)**

> [!VIDEO https://www.youtube.com/embed/VDKpDNX96aA]


## <a name="create-a-workspace"></a>Skapa en arbetsyta

Om du har en arbetsyta för Azure Machine Learning-tjänsten kan gå vidare till den [nästa avsnitt](#open-the-visual-interface-webpage). Annars kan skapa en nu.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Öppna visuella gränssnittet-webbsida

1. Öppna din arbetsyta i den [Azure-portalen](https://portal.azure.com/).  

1. På arbetsytan, Välj **visuella gränssnittet**.  Välj sedan **starta visuella gränssnittet**.  

    ![Skärmbild av Azure-portalen som visar hur du kommer åt det visuella gränssnittet från en arbetsyta för Machine Learning-tjänsten](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    Gränssnittet webbsida öppnas i en ny Webbläsarsida.  

## <a name="import-and-clean-your-data"></a>Importera och rensa data

Det första du behöver är Rensa data. Om du har slutfört snabbstarten kan du återanvända data prep experimentet här. Om du inte har slutfört snabbstarten kan du hoppa över nästa avsnitt och [starta från ett nytt experiment](#start-from-a-new-experiment).

### <a name="reuse-the-quickstart-experiment"></a>Återanvända Snabbstart experimentet

1. Öppna experimentet Snabbstart.

1. Välj **Spara som** längst ned i fönstret.

1. Ge den ett nytt namn i den dialogruta som visas.

    ![Skärmbild som visar hur du byter namn på ett experiment att ”självstudien--förutsäga Automobile Price”](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. Nu bör experimentet se ut ungefär så här:

    ![Skärmbild som visar det förväntade tillståndet för experimentet. Bil datauppsättningen ansluter till modulen Välj kolumner som ansluter till den Rensa Data som saknas](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

Om du har återanvänds experimentet Snabbstart, hoppar du över nästa avsnitt för att börja [träna din modell](#train-the-model).

### <a name="start-from-a-new-experiment"></a>Starta från ett nytt experiment

Om du inte har slutfört snabbstarten, följer du stegen nedan för att snabbt skapa ett nytt experiment som importerar och rensar bil datauppsättningen.

1. Skapa ett nytt experiment genom att välja **+ ny** längst ned i fönstret visuella gränssnittet.

1. Välj **EXPERIMENT** >  **Tomt experiment**.

1. Välj det fördefinierade experimentnamnet **”redan experimenterat lite skapas på...** ”överst på arbetsytan och Byt namn på den till något beskrivande. Till exempel **bilpriser**. Namnet behöver inte vara unikt.

1. Till vänster om arbetsytan för experimentet finns en palett med datauppsättningar och moduler. Använd sökrutan överst på modulpaletten för att hitta moduler. Typ **bil** i sökrutan för att hitta datauppsättningen **Automobile price data (Raw)**. Dra datauppsättningen till experimentarbetsytan.

    ![Skärmbild hitta datauppsättningen bil pris](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    Nu när du har dina data kan du lägga till en modul som tar bort den **normalized-losses** kolumn helt. Lägg sedan till en annan modul som tar bort rader där data saknas.

1. Typ **Markera kolumner** i sökrutan för att hitta den **Välj kolumner i datauppsättning** modulen. Dra den sedan till experimentarbetsytan. Den här modulen kan du välja vilka kolumner med data som du vill inkludera eller exkludera i modellen.

1. Anslut utdataporten för datauppsättningen **Automobile price data (Raw)** till indataporten för Välj kolumner i datauppsättningen.

    ![Animerad gif som visar hur du ansluter modulen Automobile Price Data till modulen Välj kolumner](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Välj Välj kolumner i datauppsättning modulen och välj **starta kolumnväljaren** i den **egenskaper** fönstret.

   1. Till vänster, Välj **med regler**

   1. Bredvid **börjar med**väljer **alla kolumner**. Dessa regler som är direkt **Välj kolumner i datauppsättning** att gå igenom alla kolumner (utom de som vi ska utesluta).

   1. Listrutor, Välj **undanta** och **kolumnnamn**, och skriv sedan **normalized-losses** i textrutan.

   1. Välj knappen OK för att Stäng kolumnväljaren (på längst ned till höger).

     Egenskapsrutan för **Välj kolumner i datauppsättning** anger nu att alla kolumner i datauppsättningen tas med utom **normalized-losses**.

1. Lägga till en kommentar i **Välj kolumner i datauppsättning** modulen genom att dubbelklicka på modulen och ange ”exkludera normaliserade förluster”. Detta kan hjälpa dig en snabb översikt vad modulen gör i experimentet.

    ![Skärmbild som visar korrekt konfiguration av modulen Välj kolumner](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. Typ **Rengör** i sökrutan för att hitta den **Rensa Data som saknas** modulen. Dra den **Rensa Data som saknas** arbetsytan och koppla den till modulen till arbetsytan för experimentet den **Välj kolumner i datauppsättning** modulen.

1. I fönstret **Egenskaper** väljer du **Ta bort hela raden** under **Rensningsläge**. Dessa alternativ direkt **Rensa Data som saknas** att rensa data genom att ta bort rader som har värden som saknas. Dubbelklicka på modulen och skriv kommentaren ”Ta bort rader med värden som saknas”.

![Skärmbild som visar korrekt konfiguration av modulen Rensa Data som saknas](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

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

    Nu bör experimentet se ut:
    ![Skärmbild som visar experimentet när du lägger till träningsmodellmodulen är korrekt konfigurerat.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Kör träningsexperimentet

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Poängsätta och utvärdera modellen

Nu när du har tränat modellen med 70 procent av dina data, du kan använda den för att bedöma 30 procent av data och se hur bra modell fungerar.

1. Typ **poängmodell** i sökrutan för att hitta den **Poängmodell** modulen och dra modulen till experimentarbetsytan. Anslut utdataporten för den **Träningsmodell** modul till vänstra indataporten för **Poängmodell**. Anslut utdataporten för testning (den högra porten) för den **dela Data** indataporten för modulen till höger för **Poängmodell**.

1. Typ **utvärdera** i sökrutan för att hitta den **utvärdera modell** it dra modulen till experimentarbetsytan. Anslut utdataporten för den **Poängmodell** den vänstra indataporten för modulen **utvärdera modell**. Det slutliga experimentet bör se ut ungefär så här:

    ![Skärmbild som visar den slutliga korrekt konfigurationen av experimentet.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Kör experimentet med hjälp av samma beräkningsmål som använts tidigare.

1. Visa utdata från den **Poängmodell** modulen genom att välja utdataporten för **Poängmodell** och välj **visualisera**. Utdata innehåller de förväntade värdena för pris och de kända värdena från testdata.

    ![Skärmbild av utdata visualiseringen markering kolumnen ”poängsätts etikett”](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Om du vill visa utdata från modulen utvärdera modell, Välj på utdataporten och välj sedan visualisera.

    ![Skärmbild som visar utvärderingsresultatet för det slutliga experimentet.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

För din modell visas följande statistik:

* **Medelabsolutfel (MAE)**: Medelvärdet av absoluta fel (ett fel är skillnaden mellan det förväntade och faktiska värdet).
* **Rot medelvärdet cirkels fel (RMSE)**: Kvadratroten av genomsnittet av kvadratfel i förutsägelser som görs mot testdatauppsättningen.
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

* Återanvända experimentet skapade i snabbstarten
* Förbereda data
* Träna modellen
* Poängsätta och utvärdera modellen

I del två lär du dig att distribuera din modell som en Azure-webbtjänst.

> [!div class="nextstepaction"]
> [Fortsätta att distribuera modeller](ui-tutorial-automobile-price-deploy.md)
