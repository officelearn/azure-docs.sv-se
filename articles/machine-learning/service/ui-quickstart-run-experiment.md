---
title: 'Snabbstart: Förbereda och visualisera data utan att skriva kod'
titleSuffix: Azure Machine Learning service
description: Skapa en machine learning-experiment för att förbereda och visualisera dina data med ett användargränssnitt med dra och släpp.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 985ad59a1ffa5bd68f97824f7f50af604174b543
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026979"
---
# <a name="quickstart-prepare-and-visualize-data-without-writing-code"></a>Snabbstart: Förbereda och visualisera data utan att skriva kod

Förbered och visualisera dina data med ett användargränssnitt med dra och släpp. Du kommer att använda innehåller poster för olika bilar, inklusive uppgifter om märke, modell, tekniska specifikationer och pris.  

I den här snabbstarten du utforska och förbereda data:

- Skapa ditt första experiment för att lägga till och förhandsgranska data
- Förbereda data genom att ta bort värden som saknas
- Kör experimentet
- Visualisera resultatet

Om du är helt nya på machine learning, videoserien [datavetenskap för nybörjare](https://docs.microsoft.com/azure/machine-learning/studio/data-science-for-beginners-the-5-questions-data-science-answers) är en bra introduktion till machine learning.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) idag.

### <a name="create-a-workspace"></a>Skapa en arbetsyta

Om du har en arbetsyta för Azure Machine Learning-tjänsten kan gå vidare till den [nästa avsnitt](#start). Annars kan skapa en nu.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="start"></a> Öppna visuella gränssnittet-webbsida

1. Öppna din arbetsyta i den [Azure-portalen](https://portal.azure.com/).  

1. På arbetsytan, Välj **visuella gränssnittet**.  Välj sedan **starta visuella gränssnittet**.  
 
    ![Starta visuella gränssnittet](./media/ui-quickstart-run-experiment/launch-ui.png)

    Gränssnittet webbsida öppnas i en ny Webbläsarsida.  

## <a name="create-your-first-experiment"></a>Skapa ditt första experiment

Verktyget visuella gränssnittet innehåller en interaktiv, visuell plats för att enkelt bygga, testa och iterera på en prediktiv analysmodell. Du dra och släpp datauppsättningar och analysis-moduler till en interaktiv arbetsyta som kopplar samman dem att skapa en _experimentera_.  Skapa ditt första experiment nu.

1. I det nedre vänstra hörnet väljer **Lägg till ny**.
![Lägg till nytt experiment](./media/ui-quickstart-run-experiment/add-new.png)

1. Välj **tomt Experiment**.

1. Experimentet får ett standardnamn. Markera texten och Byt namn på den till ”Snabbstart utforska data”. Det här namnet behöver inte vara unika.

1. Den **Mini-kartan** längst ned på skärmen är användbart för att visa stora experiment.  Du behöver i den här snabbstarten så klickar du på pilen högst upp för att minimera den.  

    ![Byta namn på experimentet](./media/ui-quickstart-run-experiment/rename.png)

## <a name="add-data"></a>Lägg till data

Det första du behöver för machine learning är data. Det finns flera provdatauppsättningar som ingår i det här gränssnittet som du kan använda eller du kan importera data från flera källor. I det här exemplet ska du använda exempeldatauppsättningen **Automobile price data (Raw)**. 

1. Till vänster om arbetsytan för experimentet finns en palett med datauppsättningar och moduler. Välj **sparade datauppsättningar** därefter **exempel** att visa tillgängliga exempeldatauppsättningarna.

1. Välj sedan datamängden **Automobile price data (raw)**, och dra den till arbetsytan.

   ![Dra data på ytan](./media/ui-quickstart-run-experiment/drag-data.png)


## <a name="select-columns"></a>Välja kolumner

Välj vilka kolumner med data du arbetar med.  Konfigurera att börja med modulen för att visa alla tillgängliga kolumner.

> [!TIP]
> Om du känner till namnet på data eller modulen som du vill använda sökfältet överst på modulpaletten för att hitta det snabbt.  Resten av snabbstarten används den här genvägen.

1. Typ **Välj** i sökrutan för att hitta den **Välj kolumner i datauppsättning** modulen.

1. Klicka och dra den **Välj kolumner i datauppsättning** till arbetsytan. Ta bort modulen nedan datauppsättningen som du lade till tidigare.

1. Ansluta datauppsättningen till den **Välj kolumner i datauppsättning**: Klicka på utdataporten för datauppsättningen genom att dra till indataporten för **Välj kolumner i datauppsättning**, släpper musknappen. Datamängden och modulen förblir anslutna även om du flyttar runt endera av dem på arbetsytan.

    > [!TIP]
    > Datauppsättningar och moduler har ingångs- och utgångsportar som representeras av små cirklar – ingångsportar högst upp och utgångsportar längst ned. Du skapar ett flöde av data via ditt experiment när du ansluter utdataporten för en modul till en port på en annan.
    >
    > Om du har problem med att ansluta moduler kan du prova att dra hela vägen till den nod som du ansluter.

    ![Ansluta moduler](./media/ui-quickstart-run-experiment/connect-modules.gif) 

    Rött utropstecken anger att du inte ställer in egenskaperna för modulen ännu. Det gör du i nästa steg.
   
1. Välj den **Välj kolumner i datauppsättning** modulen.

1. I den **egenskaper** rutan till höger om arbetsytan, väljer **starta kolumnväljaren**.

    I den **Markera kolumner** dialogrutan Välj **alla kolumner** och inkludera **alla funktioner**. Dialogrutan bör se ut så här:

     ![column-selector](./media/ui-quickstart-run-experiment/select-all.png)

1. Klicka på bockmarkeringen (OK) längst ned till höger, att Stäng kolumnväljaren.

## <a name="run-the-experiment"></a>Kör experimentet

Klicka på utdataporten för en datauppsättning eller en modul för att se hur data ser ut vid den tidpunkten i dataflödet när som helst.  Om den **visualisera** alternativet är inaktiverat, måste du först kör experimentet.  Det gör du i nästa steg.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

När beräkningsmål är tillgängligt, kör experimentet. När körningen är klar visas en grön bockmarkering på varje modul.

![Visa status](./media/ui-quickstart-run-experiment/status1.png)

## <a name="preview-the-data"></a>Förhandsgranska data

Nu när du har kört ditt första experiment kan visualisera du data om du vill veta mer om den information du måste arbeta med.

1. Välj på utdataporten längst ned på den **Välj kolumner i datauppsättning** därefter **visualisera**.

1. Klicka på olika kolumner i datafönstret att visa information om den kolumnen.  

    I den här datamängden representerar varje rad en bil, och de variabler som är associerade med varje bil visas som kolumner.    Det finns 205 rader och 26 kolumner i den här datauppsättningen.

     Varje gång du klickar på en kolumn med data, den **statistik** information och **visualisering** bild av kolumnen visas till vänster.  Till exempel när du klickar på **num av dörrar** du se att den har 2 unika värden och 2 värden som saknas.  Rulla ned för att se värdena: två till fyra dörrar.

     ![Förhandsgranska data](./media/ui-quickstart-run-experiment/preview-data.gif)

1. Klicka på varje kolumn om du vill veta mer om din datauppsättning.

## <a name="prepare-data"></a>Förbereda data

En datauppsättning kräver vanligtvis viss bearbetning i förväg innan den kan analyseras. Du kanske har lagt märke till värdena som saknas i kolumnerna på olika rader. Dessa värden som saknas måste rensas bort så att modellen kan analysera informationen korrekt. Du tar bort alla rader som innehåller värden som saknas. Dessutom den **normalized-losses** kolumn har en stor del av saknade värden, så att du ska utesluta kolumnen från modellen helt och hållet.

> [!TIP]
> Du måste rensa värden som saknas i indata för att kunna använda de flesta moduler.  

### <a name="remove-column"></a>Ta bort kolumn

Ta först bort de **normalized-losses** kolumn helt.

1. Välj den **Välj kolumner i datauppsättning** modulen.

1. I den **egenskaper** rutan till höger om arbetsytan, väljer **starta kolumnväljaren**.

    * Lämna **med regler** och **alla kolumner** valda.

    * I listrutorna väljer du **Exkludera** och **kolumnnamn** och klickar sedan i textrutan. Typ **normalized-losses**.

    * Klicka på bockmarkeringen (OK) längst ned till höger, att Stäng kolumnväljaren.

    ![Undanta en kolumn](./media/ui-quickstart-run-experiment/exclude-column.png)
        
    Nu egenskapsfönstret för Välj kolumner i datauppsättning anger att den släpps igenom alla kolumner i datauppsättningen tas med utom **normalized-losses**.
        
    Egenskapsrutan visar att den **normalized-losses** utesluta kolumnen.
        
    ![Egenskapsfönster](./media/ui-quickstart-run-experiment/property-pane.png)
        
    Du kan lägga till en kommentar till en modul genom att dubbelklicka på modulen och skriva text. På så sätt kan du snabbt se vad modulen gör i experimentet. 

1. Dubbelklicka på den **Välj kolumner i datauppsättning** modulen och skriver kommentaren ”exkludera normaliserade förluster”. 
    
    När du skriver kommentaren, klickar du på utanför modulen.  En nedpil visas att modulen innehåller en kommentar.

1. Klicka på nedåt-pilen för att visa kommentaren.

    Modulen visas nu en upp-pilen för att dölja kommentaren.
        
    ![Kommentarer](./media/ui-quickstart-run-experiment/comments.png)

### <a name="clean-missing-data"></a>Rensa data som saknas

Nu ska du lägga till en annan modul som tar bort återstående rader där data saknas.

1. Typ **Rengör** i sökrutan för att hitta den **Rensa Data som saknas** modulen.

1. Dra den **Rensa Data som saknas** arbetsytan och koppla den till modulen till arbetsytan för experimentet den **Välj kolumner i datauppsättning** modulen. 

1. I fönstret Egenskaper väljer **ta bort hela raden** under **Rensningsläge**.

    Dessa alternativ direkt **Rensa Data som saknas** att rensa data genom att ta bort rader som har värden som saknas.

1. Dubbelklicka på modulen och skriv kommentaren ”Ta bort rader med värden som saknas”.
 
    ![Ta bort rader](./media/ui-quickstart-run-experiment/remove-rows.png)

    Experimentet bör nu se ut ungefär så här:
    
    ![Välj kolumner](./media/ui-quickstart-run-experiment/experiment-clean.png)

## <a name="visualize-the-results"></a>Visualisera resultat

Eftersom du har gjort ändringar i moduler i ditt experiment, har status ändrats till ”draft”.  Om du vill visualisera informationen som nya ren först du köra experimentet igen.

1. Välj **kör** längst ned för att köra experimentet.

    Nu kan du återanvända beräkningsmål som du skapade tidigare.  

1. Välj **kör** i dialogrutan.

   ![Kör experimentet](./media/ui-quickstart-run-experiment/select-compute.png)

1. När körningen är klar högerklickar du på den **Rensa Data som saknas** modul för att visualisera nya rensa data.  

    ![Visualisera rensa data](./media/ui-quickstart-run-experiment/visualize-cleaned.png)

1. Klicka på olika kolumner i fönstret rensade data för att se hur data har ändrats.  

    ![Visualisera Rensa Data](media/ui-quickstart-run-experiment/visualize-result.png)

    Det finns nu 193 rader och kolumner som är 25.

    När du klickar på **num av dörrar** du ser den fortfarande har 2 unika värden men nu har 0 värden som saknas.  

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

- Skapa ditt första experiment för att lägga till och förhandsgranska data
- Förbereda data genom att ta bort värden som saknas
- Visualisera resultatet

Vill du fortsätta till självstudien att använda dessa data för att förutsäga priset på en bil.

> [!div class="nextstepaction"]
> [Självstudie: Förutsäga bil pris med det visuella gränssnittet](ui-tutorial-automobile-price-train-score.md)
