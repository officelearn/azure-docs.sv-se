---
title: 'Snabb start: skapa ett data vetenskaps experiment'
titleSuffix: ML Studio (classic) - Azure
description: Den här snabbstarten om maskininlärning vägleder dig genom ett enkelt dataexperiment. Vi kommer förutsäga priset för en bil med hjälp av en regressionsalgoritm.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: c4c50ba53bc40ad6ae6fc60b3992f9ab992eb268
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204570"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-azure-machine-learning-studio-classic"></a>Snabb start: skapa ditt första data vetenskaps experiment i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

I den här snabb starten skapar du ett Machine Learning-experiment i [Azure Machine Learning Studio (klassisk)](what-is-ml-studio.md) som förutsäger priset på en bil baserat på olika variabler, till exempel tekniska specifikationer.

Om maskininlärning är nytt för dig är videoserien [Datavetenskap för nybörjare](data-science-for-beginners-the-5-questions-data-science-answers.md) en bra introduktion som med enkelt språk förklarar vad maskininlärning är.

Den här snabbstarten följer standardarbetsflödet för ett experiment:

1. **Skapa en modell**
    - [Hämta data]
    - [Förbereda data]
    - [Definiera funktioner]
1. **Träna modellen**
    - [Välja och tillämpa en algoritm]
1. **Poängsätta och testa modellen**
    - [Förutsäga nya bilpriser]

[Hämta data]: #get-the-data
[Förbereda data]: #prepare-the-data
[Definiera funktioner]: #define-features
[Välja och tillämpa en algoritm]: #choose-and-apply-an-algorithm
[Förutsäga nya bilpriser]: #predict-new-automobile-prices

## <a name="get-the-data"></a>Hämta data

Det första du behöver för maskininlärning är data.
Det finns flera exempel data uppsättningar som ingår i Studio (klassisk) som du kan använda, eller så kan du importera data från många källor. I det här exemplet kommer vi att använda exempeluppsättningen **Automobile price data (Raw)** , som ingår i arbetsytan.
Den här datauppsättningen innehåller poster för ett antal olika bilar, inklusive uppgifter om modell, tekniska specifikationer och pris.

> [!TIP]
> En arbetskopia av följande experiment finns i [Azure AI-galleriet](https://gallery.azure.ai). Gå till **[ditt första data vetenskaps experiment – pris förutsägelse för bilbilar](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** och klicka på **Öppna i Studio** för att ladda ned en kopia av experimentet i arbets ytan Machine Learning Studio (klassisk).

Så här gör du för att få datauppsättningen till experimentet.

1. Skapa ett nytt experiment genom att klicka på **+ ny** längst ned i fönstret Machine Learning Studio (klassisk). Välj **EXPERIMENT** >  **Tomt experiment**.

1. Experimentet får ett standardnamn som visas överst i arbetsytan. Markera texten och byt namn på den till ett mer beskrivande namn, exempelvis **Förutsägelse av bilpriser**. Namnet behöver inte vara unikt.

    ![Byt namn på experimentet](./media/create-experiment/rename-experiment.png)

1. Till vänster om arbetsytan för experimentet finns en palett med datauppsättningar och moduler. Skriv **automobile** i sökrutan överst på den här paletten för att leta upp datauppsättningen **Automobile price data (Raw)** . Dra datauppsättningen till experimentarbetsytan.

    ![Leta upp datamängden för bilar och dra den till experimentets arbetsyta](./media/create-experiment/type-automobile.png)

Om du vill se hur den här informationen ser ut klickar du på utdataporten längst ned i datamängden för bilar och väljer **Visualisera**.

![Klicka på utdataporten och välj ”Visualisera”](./media/create-experiment/select-visualize.png)

> [!TIP]
> Datauppsättningar och moduler har ingångs- och utgångsportar som representeras av små cirklar – ingångsportar högst upp och utgångsportar längst ned.
Om du vill skapa ett flöde av data via ditt experiment ansluter du en utgångsport från en modul till en port på en annan.
Du kan när som helst klicka på en datauppsättnings eller moduls utgångsport för att se hur dina data ser ut vid den tidpunkten i dataflödet.

I den här datamängden representerar varje rad en bil, och de variabler som är associerade med varje bil visas som kolumner. Vi förutsäger priset i kolumnen längst till höger (kolumn 26, ”price” (pris)) med hjälp av variablerna för en specifik bil.

![Visa bildata i datavisualiseringsfönstret](./media/create-experiment/visualize-auto-data.png)

Stäng visualiseringsfönstret genom att klicka på ”**x**” i det övre högra hörnet.

## <a name="prepare-the-data"></a>Förbereda data

En datauppsättning kräver vanligtvis viss bearbetning i förväg innan den kan analyseras. Du kanske har lagt märke till värdena som saknas i kolumnerna på olika rader. Dessa värden som saknas måste rensas bort så att modellen kan analysera informationen korrekt. Vi tar bort alla rader som har saknade värden. Dessutom har kolumnen **normalized-losses** många värden som saknas, så vi ska utesluta kolumnen från modellen helt och hållet.

> [!TIP]
> Du måste rensa värden som saknas i indata för att kunna använda de flesta moduler.

Först lägger vi till en modul som tar bort kolumnen **normalized-losses** helt. Sedan lägger vi till en till modul som tar bort rader där data saknas.

1. Skriv **Markera kolumner** i sökrutan längst upp i modulen modul för att hitta modulen [Välj kolumner i data uppsättning][select-columns] . Dra den sedan till experimentarbetsytan. Den här modulen gör att vi kan välja vilka kolumner med data som vi vill ta med eller utelämna i modellen.

1. Anslut utdataporten för datauppsättningen **Automobile price data (Raw)** till indataporten för Välj kolumner i datauppsättningen.

    ![Lägg till modulen "Välj kolumner i datamängd" till experimentarbetsytan och anslut den](./media/create-experiment/type-select-columns.png)

1. Klicka på modulen [Välj kolumner i data uppsättning][select-columns] och klicka på **Starta kolumn väljaren** i rutan **Egenskaper** .

   - Klicka på **Med regler** till vänster.
   - Under **Börjar med** klickar du på **Alla kolumner**. Dessa regler styr genom att [Markera kolumner i data uppsättningen][select-columns] för att gå igenom alla kolumner (utom de kolumner som vi håller på att undanta).
   - I listrutorna väljer du **Exkludera** och **kolumnnamn** och klickar sedan i textrutan. En lista med kolumner visas. Välj **normalized-losses** så läggs den till i textrutan.
   - Stäng kolumnväljaren genom att klicka på bockmarkeringen (OK) längst ned till höger.

     ![Starta kolumnväljaren och utelämna kolumnen ”normalized-losses”](./media/create-experiment/launch-column-selector.png)

     Egenskapsrutan för **Välj kolumner i datauppsättning** anger nu att alla kolumner i datauppsättningen tas med utom **normalized-losses**.

     ![Egenskapsrutan visar att kolumnen ”normalized-losses” är utelämnad](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > Du kan lägga till en kommentar till en modul genom att dubbelklicka på modulen och skriva text. På så sätt kan du snabbt se vad modulen gör i experimentet. I det här fallet dubbelklickar du på modulen [Välj kolumner i data uppsättning][select-columns] och skriver kommentaren "exkludera normaliserade förluster".

     ![Lägga till en kommentar genom att dubbelklicka på en modul](./media/create-experiment/add-comment.png)

1. Dra modulen [Rensa data som saknas][clean-missing-data] till experimentets arbets yta och Anslut den till modulen [Välj kolumner i data uppsättning][select-columns] . I fönstret **Egenskaper** väljer du **Ta bort hela raden** under **Rensningsläge**. Med de här alternativen rensas data som [saknas][clean-missing-data] för att rensa data genom att ta bort rader som innehåller värden som saknas. Dubbelklicka på modulen och skriv kommentaren ”Ta bort rader med värden som saknas”.

    ![Ställ in rengöringsläget på ”Ta bort hela raden” för modulen ”Rensa data som saknas”](./media/create-experiment/set-remove-entire-row.png)

1. Kör försöket genom att klicka på **KÖR** längst ned på sidan.

    När experimentet är klart visas alla moduler med en grön bockmarkering som bekräftar att de har slutförts. Notera också statusen **Har slutförts** i det övre högra hörnet.

    ![Efter körning bör experimentet se ut ungefär så här](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Varför kör vi experimentet nu? Genom att köra experimentet kan kolumn definitionerna för våra data skickas från data uppsättningen via modulen [Välj kolumner i data uppsättning][select-columns] och genom modulen [Rensa data som saknas][clean-missing-data] . Det innebär att alla moduler som vi ansluter till [rensa saknade data][clean-missing-data] också har samma information.

Nu har vi rena data. Om du vill visa den rensade data uppsättningen klickar du på den vänstra utdataporten för modulen [Rensa data som saknas][clean-missing-data] och väljer **visualisera**. Observera att kolumnen **normalized-losses** inte längre ingår och att det inte finns några värden som saknas.

Nu när vi har rensat bort data kan vi ange vilka funktioner som vi vill använda i förutsägelsemodellen.

## <a name="define-features"></a>Definiera funktioner

I Machine Learning är *funktioner* enskilda mätbara egenskaper av något du är intresse rad av. I vår datauppsättning representerar varje rad en bil och varje kolumn är en funktion i den bilen.

Det krävs en del experimenterande och kunskap om det problem som ska lösas för att hitta en bra uppsättning funktioner för att skapa en förutsägelsemodell. Vissa funktioner är bättre för att förutsäga målet än andra. Vissa funktioner har en stark korrelation med andra funktioner och kan tas bort. Till exempel är city-mpg och highway-mpg nära relaterade så att vi kan behålla en och ta bort den andra utan att förutsägelsen påverkas avsevärt.

Vi ska skapa en modell som använder en delmängd av funktionerna i vår datauppsättning. Du kan komma tillbaka senare och välja andra funktioner, köra experimentet igen och se om du får bättre resultat. Men om du vill starta ska du prova följande funktioner:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Dra en annan [Välj kolumner i data uppsättnings][select-columns] modulen till experimentets arbets yta. Anslut den vänstra utdataporten för modulen [Rensa data som saknas][clean-missing-data] till indata för modulen [Välj kolumner i data uppsättning][select-columns] .

    ![Ansluta modulen ”Välj kolumner i datamängd” till modulen ”Rensa data som saknas”](./media/create-experiment/connect-clean-to-select.png)

1. Dubbelklicka på modulen och skriv ”Välj funktioner för förutsägelse”.

1. Klicka på **Starta kolumnväljaren** i fönstret **Egenskaper**.

1. Klicka på **Med regler**.

1. Under **Börjar med** klickar du på **Inga kolumner**. I filterraden väljer du **Inkludera** och **kolumnnamn** och markerar listan över kolumnnamn i textrutan. Det här filtret instruerar modulen att bara ta med de kolumner (funktioner) som vi anger.

1. Klicka på bockmarkeringen (OK).

    ![Markera de kolumner (funktioner) som ska ingå i förutsägelsen](./media/create-experiment/select-columns-to-include.png)

Den här modulen skapar en filtrerad datamängd som enbart innehåller de funktioner vi vill skicka till den inlärningsalgoritm som vi använder i nästa steg. Du kan komma tillbaka senare och försöka igen med andra funktioner.

## <a name="choose-and-apply-an-algorithm"></a>Välja och tillämpa en algoritm

Nu när våra data är klara är det dags att gå vidare och träna och testa vår förutsägelsemodell. Vi ska använda våra data för att träna modellen och sedan testa den för att se hur väl den kan förutsäga priser.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Klassificering* och *regression* är två typer av övervakade Machine Learning-algoritmer. Klassificering förutsäger ett svar från en definierad uppsättning kategorier, till exempel en färg (röd, blå eller grön). Regression används för att förutsäga ett tal.

Eftersom vi vill förutsäga pris, vilket är ett tal, använder vi en regressionsalgoritm. I det här exemplet använder vi en *linjär regressionsmodell*.


Vi tränar modellen genom att ge den en uppsättning data som innehåller priset. Modellen söker efter data och letar efter samband mellan en bils funktioner och dess pris. Därefter testar vi modellen – den får en uppsättning funktioner för bilar vi känner till, så vi kan se hur nära modellen kommer att förutsäga det kända priset.

Vi använder våra data både för träning och testning av modellen genom att dela in data i separata tränings- och testningsdatauppsättningar.

1. Markera och dra modulen [dela data][split] till arbets ytan för experimentet och koppla den till den sista [Välj kolumner i modulen data uppsättning][select-columns] .

1. Klicka på modulen [dela data][split] för att markera den. Leta upp **Del av rader i den första utdatauppsättningen** (i fönstret **Egenskaper** på höger sida i arbetsytan) och ange 0,75. På så sätt kan vi använda 75 procent av våra data för att träna modellen, och lämna 25 procent för testning.

    ![Ställa in värdet för ”Dela data” på 0,75](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > Genom att ändra parametern **Slumptal** kan du generera olika slumpmässiga prov för träning och testning. Den här parametern styr den pseudoslumpmässiga talgeneratorns startvärden (seeding).

1. Kör experimentet. När experimentet körs skickar du kolumn definitionerna [Välj kolumner i data uppsättning][select-columns] och [dela data][split] till de moduler som vi ska lägga till härnäst.  

1. Välj inlärningsalgoritmen genom att expandera kategorin **Machine Learning** på modulpaletten till vänster om arbetsytan och expandera **Initiera modell**. Nu visas flera kategorier av moduler som kan användas för att initiera algoritmer för Machine Learning. För det här experimentet väljer du modulen [linjär regression][linear-regression] under **Regressions** kategorin och drar den till experimentets arbets yta. (Du kan också hitta modulen genom att skriva "linjär regression" i rutan Sök på paletten.)

1. Leta upp och dra modulen [träna modell][train-model] till arbets ytan för experimentet. Anslut utdataporten för modulen [linjär regression][linear-regression] till vänster indata för modulen [träna modell][train-model] och Anslut utbildnings data utmatningen (den vänstra porten) för modulen [dela data][split] till rätt indata för modulen [träna modell][train-model] .

    ![Anslut modulen ”Träna modell” till de båda modulerna ”Linjär regression” och ”Dela data”](./media/create-experiment/connect-train-model.png)

1. Klicka på modulen [träna modell][train-model] , klicka på **Starta kolumn väljaren** i rutan **Egenskaper** och välj kolumnen **pris** . **Pris** är det värde som vår modell ska förutsäga.

    Du väljer **pris**kolumnen i kolumnväljaren genom att dra den från listan över **tillgängliga kolumner** till listan över **markerade kolumner**.

    ![Välja priskolumnen för modulen ”Träna modell”](./media/create-experiment/select-price-column.png)

1. Kör experimentet.

Nu har vi en tränad regressionsmodell som kan användas för att poängsätta nya bildata för att göra prisförutsägelser.

![Efter körning bör experimentet se ut ungefär så här](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Förutsäga nya bilpriser

Nu när vi har tränat modellen med 75 procent av våra data kan vi använda den för att bedöma de övriga 25 procenten av dessa data och se hur bra vår modell fungerar.

1. Leta upp och dra modulen [Poäng modell][score-model] till experimentets arbets yta. Anslut utdata från modulen [träna modell][train-model] till den vänstra Indataporten för [Poäng modell][score-model]. Anslut test data utmatningen (höger port) för modulen [dela data][split] till den högra Indataporten för [Poäng modellen][score-model].

    ![Ansluta modulen ”Poängsätta modell” till de båda modulerna ”Träna modell” och ”Dela data”](./media/create-experiment/connect-score-model.png)

1. Kör experimentet och visa utdata från modulen [Poäng modell][score-model] genom att klicka på utdataporten för [Poäng modell][score-model] och välj **visualisera**. Utdata innehåller de förväntade värdena för pris och de kända värdena från testdata.  

    ![Utdata från modulen ”Poängsätta modell”](./media/create-experiment/score-model-output.png)

1. Slutligen testar vi resultatets kvalitet. Markera och dra modulen [utvärdera modell][evaluate-model] till arbets ytan för experimentet och Anslut utdata från modulen [Poäng modell][score-model] till den vänstra inmatningen för [utvärdera modell][evaluate-model]. Det slutliga experimentet bör se ut ungefär så här:

    ![Det slutliga experimentet](./media/create-experiment/complete-linear-regression-experiment.png)

1. Kör experimentet.

Du visar utdata från modulen [utvärdera modell][evaluate-model] genom att klicka på utdataporten och sedan välja **visualisera**.

![Utvärderingsresultat för experimentet](./media/create-experiment/evaluation-results.png)

För vår modell visas följande statistik:

- **Medelabsolutfel** (MAE): Medelvärdet av absoluta fel (ett *fel* är skillnaden mellan det förväntade och faktiska värdet).
- **Medelkvadratfel** (RMSE): Kvadratroten av genomsnittet av kvadratfel i förutsägelser som görs mot testdatauppsättningen.
- **Relativa absoluta fel**: Medelvärdet av absoluta fel i förhållande till den absoluta skillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
- **Relativa kvadratfel**: Medelvärdet av kvadratfel i förhållande till kvadratskillnaden mellan faktiska värden och medelvärdet av alla faktiska värden.
- **Bestämningskoefficient**: Kallas också för **R-kvadratvärdet** och är ett statistiskt mått som anger hur väl en modell passar data.

För all felstatistik gäller att mindre är bättre. Ett mindre värde anger att förutsägelser bättre överensstämmer med de faktiska värdena. För **bestämningskoefficient** är förutsägelserna bättre ju närmare värdet är ett (1,0).

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett enkelt experiment med hjälp av en exempeldatamängd. Fortsätt till självstudien om en förutsägande lösning för att utforska processen med att skapa och distribuera en modell i närmare detalj.

> [!div class="nextstepaction"]
> [Självstudie: utveckla en förutsägelse lösning i Studio (klassisk)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
