---
title: Tolka modellresultat i Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Hur du väljer den optimala parameteruppsättning för en algoritm med och visualisera poängsätta modell utdata.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: 778f713fb1046d3c1987b065c9dc25c4ad9825cb
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822556"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio"></a>Tolka modellresultat i Azure Machine Learning Studio
Det här avsnittet förklarar hur du visualiserar och tolka förutsagda resultaten i Azure Machine Learning Studio. När du har tränats en modell och göra förutsägelser ovanpå det. (”poängsätts modellen”), måste du förstå och tolka resultatet förutsägelse.



Det finns fyra viktiga typer av machine learning-modeller i Azure Machine Learning Studio:

* Klassificering
* Klustring
* Regression
* Rekommenderare system

Moduler som används för förutsägelse ovanpå dessa modeller är:

* [Bedömningsmodell] [ score-model] -modulen för klassificering och regression
* [Tilldela till kluster] [ assign-to-clusters] -modulen för kluster
* [Bedöma Matchbox rekommenderare] [ score-matchbox-recommender] för recommendation-system

Det här dokumentet beskriver hur du tolkar förutsägelser för var och en av dessa moduler. En översikt över dessa moduler finns i [välja parametrar för att optimera algoritmerna i Azure Machine Learning Studio](algorithm-parameters-optimize.md).

Det här avsnittet behandlar förutsägelse tolkning men inte modellen utvärderingsversioner. Läs mer om hur du utvärderar din modell, [hur du utvärderar du modellens prestanda i Azure Machine Learning Studio](evaluate-model-performance.md).

Om du inte har använt Azure Machine Learning Studio och behöver hjälp med att skapa ett enkelt experiment du kommer igång finns i [skapar ett enkelt experiment i Azure Machine Learning Studio](create-experiment.md) i Azure Machine Learning Studio.

## <a name="classification"></a>Klassificering
Det finns två underkategorier klassificering problem:

* Problem med bara två klasser (tvåklassförhöjt eller binär klassificering)
* Problem med fler än två klasser (flera klassificering)

Azure Machine Learning Studio har olika moduler behöver bry dig om de olika typerna av klassificering, men metoder för att tolka resultaten förutsägelse är liknande.

### <a name="two-class-classification"></a>Två klassificering
**Exempelexperiment**

Ett exempel på en tvåklassförhöjt klassifikationsproblem är klassificeringen av iris blommor. Uppgiften är att klassificera iris blommor baserat på deras funktioner. Iris-datauppsättningen som anges i Azure Machine Learning Studio är en delmängd av de populära [Iris-datauppsättningen](http://en.wikipedia.org/wiki/Iris_flower_data_set) som innehåller instanser av endast två blomma species (klasser 0 och 1). Det finns fyra funktioner för varje blommor (sepal length, sepal width, petal längd och petal width).

![Skärmbild av iris experiment](./media/interpret-model-results/1.png)

Figur 1. Iris tvåklassförhöjt klassificering problemet experiment

Ett experiment har utförts för att lösa problemet, som visas i bild 1. En tvåklassförhöjt beslutsträd trädet modell har tränats och poängsätts. Nu kan du visualisera resultatet från den [Poängmodell] [ score-model] modulen genom att klicka på utdataporten för den [Poängmodell] [ score-model] modulen och sedan klicka på **visualisera**.

![Modulen poängsätta modell](./media/interpret-model-results/1_1.png)

Detta öppnar bedömnings resultaten som visas i bild 2.

![Resultat av iris tvåklassförhöjt klassificering experiment](./media/interpret-model-results/2.png)

Figur 2. Visualisera ett poängmodell resultat i två klassificering

**Resultatet tolkning**

Det finns sex kolumner i resultattabellen. De vänstra fyra kolumnerna är fyra funktioner. Höger två kolumner, poängsatta etiketter och poängsätts sannolikhet är resultatet. Kolumnen poängsätts sannolikhet visar sannolikheten som en blommor tillhör klassen positivt (klass 1). Den första siffran i kolumnen (0.028571) gör att det är till exempel 0.028571 sannolikheten att den första blomman tillhör klass 1. Kolumnen poängsatta etiketter visas den förväntade klassen för varje blommor. Detta baseras på kolumnen poängsätts sannolikhet. Om poängsatta sannolikheten för en blommor är större än 0,5, förväntas som klass 1. I annat fall förväntas som klass 0.

**Web tjänstepublicering**

När resultatet har förstått och bedömas ljud, kan experimentet publiceras som en webbtjänst så att du kan distribuera i olika program och kalla den för att hämta klass förutsägelser om alla nya iris blommor. Läs hur du ändrar ett träningsexperiment till ett bedömningsskript experiment och publicera den som en webbtjänst i [självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md). Den här proceduren innehåller en arbetsflödesbaserad experiment som visas i bild 3.

![Skärmbild av bedömning experiment](./media/interpret-model-results/3.png)

Figur 3. Bedömning iris tvåklassförhöjt klassificering problemet experimentet

Du måste nu ange indata och utdata för webbtjänsten. Indata är rätt indataporten för [Poängmodell][score-model], vilket är Iris blommor funktioner indata. Valet av utdata beror på om du är intresserad av den förväntade klassen (poängsatta etikett), poängsatta sannolikheten eller båda. I det här exemplet förutsätts att du är intresserad av båda. Välj önskad utdatakolumnerna genom att använda en [Välj kolumner i datauppsättning] [ select-columns] modulen. Klicka på [Välj kolumner i datauppsättning][select-columns], klickar du på **starta kolumnväljaren**, och välj **poängsatta etiketter** och **Scored Sannolikhet**. När du har angett utdataporten för [Välj kolumner i datauppsättning] [ select-columns] och kör den igen, bör du vara redo att publicera bedömnings experiment som en webbtjänst genom att klicka på **publicera WEBBTJÄNSTEN** . Det slutliga experimentet ser ut som bild 4.

![Iris tvåklassförhöjt klassificering experimentet](./media/interpret-model-results/4.png)

Figur 4. Slutliga bedömnings experimentet av ett problem för tvåklassförhöjt klassificering av iris

När du kör webbtjänsten och ange egna värden för funktionen för en test-instans, returnerar resultatet två tal. Den första siffran är poängsatta etiketten och andra är poängsatta sannolikheten. Den här blommor förväntas som klass 1 med 0.9655 sannolikhet.

![Testa tolkar poängsätta modell](./media/interpret-model-results/4_1.png)

![Bedömning av testresultat](./media/interpret-model-results/5.png)

Figur 5. Web service resultatet av iris tvåklassförhöjt klassificering

### <a name="multi-class-classification"></a>Flera klassificering
**Exempelexperiment**

I det här experimentet utför du en bokstav erkännande uppgift som ett exempel på multiklass-baserad klassificering. Klassificeraren försöker förutse vissa bokstäver (klassen) baserat på vissa handskrivna attributvärden som extraheras från handskrivna-avbildningar.

![Bokstav erkännande exempel](./media/interpret-model-results/5_1.png)

Det finns 16 funktioner som extraheras från handskrivna bokstav bilder i utbildningsdata. 26 bokstäver utgör vårt 26 klasser. Bild 6 visar ett experiment som tränar en klassificeringsmodell för multiklass-baserad för bokstav erkännande och förutsäga på samma funktioner på en datauppsättning för testning.

![Bokstav erkännande inom klassificering experiment](./media/interpret-model-results/6.png)

Figur 6. Bokstav erkännande inom klassificering problemet experiment

Visualisera resultaten från den [Poängmodell] [ score-model] modulen genom att klicka på utdataporten för [Poängmodell] [ score-model] modulen och sedan klicka på **Visualisera**, bör du se innehåll som visas på bild 7.

![Modellen poängresultat](./media/interpret-model-results/7.png)

Figur 7. Visualisera poängresultat modellen i en flera klassificering

**Resultatet tolkning**

De vänstra 16 kolumnerna representerar funktionen värden för test-uppsättningen. Kolumner med namn som poängsätts sannolikhet för klassen ”XX” är bara som poängsätts sannolikhet kolumnen i två fall. De visar hjälp som sedan motsvarande post hamnar i en viss klass. Till exempel för den första posten finns 0.003571 sannolikheten att den är en ”A” 0.000451 sannolikheten att det är ett ”B” och så vidare. Den sista kolumnen (poängsatta etiketter) är samma som poängsatta etiketter i två fall. Klassen med största poängsatta sannolikheten väljs som den förväntade klassen på motsvarande transaktion. Till exempel för den första posten, poängsatta etiketten är ”F” eftersom den har en största sannolikhet att vara ett ”F” (0.916995).

**Web tjänstepublicering**

Du kan också visa poängsatta etiketten för varje post och sannolikheten för poängsatta etiketten. Den grundläggande logiken är att hitta den största sannolikheten bland alla poängsatt sannolikhet. Om du vill göra detta måste du använda den [kör R-skript] [ execute-r-script] modulen. R-kod visas i figur 8 och resultatet av experimentet illustreras i bild 9.

![R-kodexempel](./media/interpret-model-results/8.png)

Figur 8. R-kod för att extrahera poängsatta etiketter och sannolikheter för etiketter

![Experiment resultat](./media/interpret-model-results/9.png)

Figur 9. Slutliga bedömnings experimentet problemets bokstav erkännande inom klassificering

När du publicerar och köra webbtjänsten och ange egna värden för inkommande funktion, returnerade resultatet ser ut som bild 10. Den här handskrivna bokstaven med extraherade 16 funktionerna, förväntas vara ett ”T” med 0.9715 sannolikhet.

![Testa tolkar poäng-modul](./media/interpret-model-results/9_1.png)

![Testresultat](./media/interpret-model-results/10.png)

Figur 10. Web service resultatet av multiklass-baserad klassificering

## <a name="regression"></a>Regression
Regressionsproblem skiljer sig från klassificering problem. I ett klassificeringsproblem försöker du förutse diskreta klasser, till exempel vilka klass en iris-blommor tillhör. Men som du ser i följande exempel visar ett regressionsproblem du försöker att förutsäga en kontinuerlig variabel, till exempel priset på en bil.

**Exempelexperiment**

Använda bil pris förutsägelse som ditt exempel för regression. Du försöker att förutsäga priset på en bil baserat på dess funktioner, inklusive märke, bränsle, strängtypen och enheten hjul. Experimentet illustreras i bild 11.

![Bil pris regression experiment](./media/interpret-model-results/11.png)

Figur 11. Bil pris regression problemet experiment

Visualisera den [Poängmodell] [ score-model] modulen resultatet ser ut som figur 12.

![Bedömning resultat för bil pris förutsägelse problemet](./media/interpret-model-results/12.png)

Figur 12. Bedömning resultatet för bil pris förutsägelse problemet

**Resultatet tolkning**

Poängsatta etiketter är resultatkolumn i den här bedömnings resultat. Talen är förväntade priset för varje bil.

**Web tjänstepublicering**

Du kan publicera regression experimentet till en webbtjänst och anropar det för bil pris förutsägelse i på samma sätt som användningsfall tvåklassförhöjt klassificering.

![Bedömning experiment för bil pris regressionsproblem](./media/interpret-model-results/13.png)

Figur 13. Bedömning experiment av en bil pris regressionsproblem

Köra webbtjänsten returnerade resultatet ser ut som figur 14. Förväntade priset för den här bil är $15,085.52.

![Testa tolkar poängsättningsmodul](./media/interpret-model-results/13_1.png)

![Bedömning modulen resultat](./media/interpret-model-results/14.png)

Figur 14. Web service-resultatet av en bil pris regressionsproblem

## <a name="clustering"></a>Klustring
**Exempelexperiment**

Nu ska vi använda igen Iris-datauppsättningen för att skapa ett kluster experiment. Här kan du filtrera ut klass etiketterna i datauppsättningen så att den endast har funktioner och kan användas i kluster. I den här iris användningsfall, ange antalet kluster som ska vara två under utbildning, vilket innebär att du vill gruppera blommor i två klasser. Experimentet visas i figur 15.

![Experimentera iris klustring problem](./media/interpret-model-results/15.png)

Figur 15. Experimentera iris klustring problem

Klustring skiljer sig från klassificering i att data träningsmängden inte redan har grunden sanningen etiketter som själva. Klustring grupper utbildning datauppsättning instanser i olika kluster. Under processen utbildning etiketten modellen posterna genom att lära skillnaderna mellan deras funktioner. Efter det kan den tränade modellen användas för att ytterligare klassificera framtida transaktioner. Det finns två delar om resultatet vi är intresserade i ett kluster problem. Den första delen är märkning datauppsättning för träning och andra klassificera en ny datauppsättning med den tränade modellen.

Den första delen av resultatet kan visualiseras genom att klicka på den vänstra utdataporten för [klustring Träningsmodell] [ train-clustering-model] och sedan klicka på **visualisera**. Visualiseringen illustreras i bild 16.

![Klustring resultat](./media/interpret-model-results/16.png)

Bild 16. Visualisera kluster resultatet för datauppsättning för träning

Resultatet av den andra delen, klustring nya poster med tränade klustringsmodell illustreras i bild 17.

![Visualisera kluster resultat](./media/interpret-model-results/17.png)

Bild 17. Visualisera klustring resultat på en ny datauppsättning

**Resultatet tolkning**

Även om resultaten av de två delarna härrör från olika experiment faser, de ser likadana ut och tolkas på samma sätt. De första fyra kolumnerna är funktioner. Den sista kolumnen tilldelningar är resultatet för förutsägelse. Poster som har tilldelats samma antal bedöms vara i samma kluster, det vill säga de delar likheter på något sätt (det här experimentet använder Euclidean avståndet Standardmåttet). Eftersom du har angett antalet kluster vara 2 märks posterna i tilldelningar 0 eller 1.

**Web tjänstepublicering**

Du kan publicera klustring experimentet till en webbtjänst och anropa för klustring förutsägelser på samma sätt som klassificeringen två användningsfall.

![Bedömning experiment för iris klustring problemet](./media/interpret-model-results/18.png)

Bild 18. Bedömning av experimentet till ett kluster iris-problem

När du har kört webbtjänsten returnerade resultat som ser ut som bild 19. Den här blommor förväntas finnas i klustret 0.

![Testa tolka poängsättningsmodul](./media/interpret-model-results/18_1.png)

![Bedömning modulen resultat](./media/interpret-model-results/19.png)

Bild 19. Web service resultatet av iris tvåklassförhöjt klassificering

## <a name="recommender-system"></a>Rekommenderare system
**Exempelexperiment**

För rekommenderare system, du kan använda restaurang rekommendation problemet som ett exempel: du kan rekommendera restauranger för kunder som är baserat på deras klassificering historik. Indata består av tre delar:

* Restaurang klassificeringar från kunder
* Funktionen kunddata
* Restaurang funktionsdata

Det finns flera saker som vi kan göra med den [träna Matchbox rekommenderare] [ train-matchbox-recommender] modul i Azure Machine Learning Studio:

* Förutsäga klassificeringar för en viss användare och objekt
* Rekommendera föremål för en viss användare
* Hitta användare som rör en viss användare
* Hitta objekt som är relaterade till ett visst objekt

Du kan välja vad du vill göra genom att välja bland de fyra alternativen i den **rekommenderare förutsägelse typ** menyn. Här kan du gå igenom alla fyra scenarier.

![Matchbox rekommenderare](./media/interpret-model-results/19_1.png)

Ett typiskt Azure Machine Learning Studio-experiment för ett system för rekommenderare ser ut som bild 20. Information om hur du använder dessa moduler för rekommenderare system finns i [träna matchbox rekommenderare] [ train-matchbox-recommender] och [poäng matchbox rekommenderare] [ score-matchbox-recommender].

![Rekommenderare system experiment](./media/interpret-model-results/20.png)

Bild 20. Rekommenderare system experiment

**Resultatet tolkning**

**Förutsäga klassificeringar för en viss användare och objekt**

Genom att välja **klassificering förutsägelse** under **rekommenderare förutsägelse typ**, du ber rekommenderare systemet att förutsäga betyget för en viss användare och objekt. Visualisering av den [poäng Matchbox rekommenderare] [ score-matchbox-recommender] utdata som returneras liknar bild 21.

![Rangordna resultat av systemet rekommenderare--omdöme förutsägelse](./media/interpret-model-results/21.png)

Bild 21. Visualisera poäng resultatet av systemet rekommenderare--omdöme förutsägelse

De första två kolumnerna är användaren-item-par som tillhandahålls av indata. Den tredje kolumnen är den förväntade klassificeringen av en användare för en viss artikel. Till exempel på den första raden förväntas kunden U1048 rate restaurang 135026 som 2.

**Rekommendera föremål för en viss användare**

Genom att välja **objekt rekommendation** under **rekommenderare förutsägelse typ**, begär du rekommenderare systemet att rekommendera objekt till en viss användare. Den sista parametern för att välja i det här scenariot är *rekommenderas val av*. Alternativet **från görs av objekt (för utvärdering av modellen)** är främst avsedd för utvärdering av modellen under utbildning. För det här steget förutsägelse vi välja **från alla objekt**. Visualisering av den [poäng Matchbox rekommenderare] [ score-matchbox-recommender] utdata som returneras liknar bild 22.

![Poäng resultatet av rekommenderare system, objekt-rekommendation](./media/interpret-model-results/22.png)

Bild 22. Visualisera poäng resultatet av rekommenderare system, objekt-rekommendation

Första sex kolumner representerar den angivna användar-ID att rekommendera objekt, som tillhandahålls av indata. De fem kolumnerna representerar de objekt som rekommenderas för användaren i fallande relevansordning. I den första raden är exempelvis rekommenderas restaurang för kund U1048 134986, följt av 135018, 134975, 135021 och 132862.

**Hitta användare som rör en viss användare**

Genom att välja **relaterade användare** under **rekommenderare förutsägelse typ**, begär du rekommenderare systemet efter relaterade användare till en viss användare. Relaterade användare är de användare som har liknande inställningar. Den sista parametern för att välja i det här scenariot är *relaterade användarens val*. Alternativet **från användare att ingen återbetalning görs objekt (för utvärdering av modellen)** är främst avsedd för utvärdering av modellen under utbildning. Välj **från alla användare** för det här steget för förutsägelse. Visualisering av den [poäng Matchbox rekommenderare] [ score-matchbox-recommender] utdata som returneras liknar bild 23.

![Poäng resultatet av rekommenderare system, relaterade användare](./media/interpret-model-results/23.png)

Bild 23. Visualisera poängresultat rekommenderare systemets--relaterade användare

Första sex kolumner visas den angivna användaren ID krävs för att hitta relaterade användare, som tillhandahålls av indata. De fem kolumnerna lagra förväntade relaterade användarna av användaren i fallande relevansordning. I den första raden är till exempel mest relevanta kunden för kund U1048 U1051, följt av U1066, U1044, U1017 och U1072.

**Hitta objekt som är relaterade till ett visst objekt**

Genom att välja **relaterade objekt** under **rekommenderare förutsägelse typ**, du ber rekommenderare-systemet för att få relaterade objekt till ett visst objekt. Relaterade objekt är objekt som är störst sannolikhet att vara gillade av samma användare. Den sista parametern för att välja i det här scenariot är *relaterade val av*. Alternativet **från görs av objekt (för utvärdering av modellen)** är främst avsedd för utvärdering av modellen under utbildning. Vi väljer **från alla objekt** för det här steget för förutsägelse. Visualisering av den [poäng Matchbox rekommenderare] [ score-matchbox-recommender] utdata som returneras liknar bild 24.

![Poäng resultatet av rekommenderare system, relaterade objekt](./media/interpret-model-results/24.png)

Bild 24. Visualisera poängresultat rekommenderare systemets--relaterade objekt

Första sex kolumner representerar objektet ID: N som behövs för att hitta relaterade objekt, som tillhandahålls av indata. De fem kolumnerna lagra förväntade relaterade objekt för objekt i fallande ordning när det gäller relevans. I den första raden är det mest relevanta objektet för objektet 135026 exempelvis 135074, följt av 135035, 132875, 135055 och 134992.

**Web tjänstepublicering**

Processen för att publicera dessa experiment som webbtjänster som ska få förutsägelser liknar för var och en av de fyra scenarierna. Här kan vi dra i det andra scenariot (det rekommenderas att objekt till en viss användare), som exempel. Du kan följa samma procedur med de andra tre.

Spara tränade rekommenderare systemet som en tränad modell och filtrera inkommande data till en enskild användare ID-kolumn som begärs, kan du koppla samman experiment som bild 25 och publicera den som en webbtjänst.

![Bedömning experiment problemets restaurang rekommendation](./media/interpret-model-results/25.png)

Bild 25. Bedömning experiment problemets restaurang rekommendation

Köra webbtjänsten returnerade resultatet ser ut som bild 26. Fem rekommenderade restauranger för användaren U1048 är 134986, 135018, 134975, 135021 och 132862.

![Exempel på rekommenderare systemtjänst](./media/interpret-model-results/25_1.png)

![Exempelresultat för experiment](./media/interpret-model-results/26.png)

Bild 26. Web service resultatet av restaurang rekommendation problem

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
