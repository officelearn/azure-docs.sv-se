---
title: Tolka modellresultat
titleSuffix: ML Studio (classic) - Azure
description: Så här väljer du den optimala parameteruppsättningen för en algoritm med hjälp av och visualiserar poängmodellutdata.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: 9a0b855f48085138b28e02e0a5d01c5dd0f666be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218071"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Tolka modellresultat i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
I det här avsnittet beskrivs hur du visualiserar och tolkar förutsägelseresultat i Azure Machine Learning Studio (klassisk). När du har tränat en modell och gjort förutsägelser ovanpå den ("gjorde modellen"), måste du förstå och tolka förutsägelseresultatet.



Det finns fyra huvudtyper av maskininlärningsmodeller i Azure Machine Learning Studio (klassiskt):

* Klassificering
* Klustring
* Regression
* Recommender system

De moduler som används för förutsägelse ovanpå dessa modeller är:

* [Poängmodellmodul][score-model] för klassificering och regression
* [Tilldela klustermodul för klustring][assign-to-clusters]
* [Betyg Matchbox Recommender][score-matchbox-recommender] för rekommendationssystem

I det här dokumentet beskrivs hur förutsägelseresultat ska tolkas för var och en av dessa moduler. En översikt över dessa moduler finns i [Så här väljer du parametrar för att optimera dina algoritmer i Azure Machine Learning Studio (klassiskt).](algorithm-parameters-optimize.md)

Det här avsnittet tar upp förutsägelsetolkning men inte modellutvärdering. Mer information om hur du utvärderar din modell finns [i Så här utvärderar du modellprestanda i Azure Machine Learning Studio (klassiskt).](evaluate-model-performance.md)

Om du inte har något nytt för Azure Machine Learning Studio (klassisk) och behöver hjälp med att skapa ett enkelt experiment för att komma igång läser du [Skapa ett enkelt experiment i Azure Machine Learning Studio (klassiskt).](create-experiment.md)

## <a name="classification"></a>Klassificering
Det finns två underkategorier av klassificeringsproblem:

* Problem med endast två klasser (tvåklass eller binär klassificering)
* Problem med fler än två klasser (klassificering av flera klasser)

Azure Machine Learning Studio (klassisk) har olika moduler för att hantera var och en av dessa typer av klassificering, men metoderna för att tolka deras förutsägelseresultat är liknande.

### <a name="two-class-classification"></a>Klassificering i två klasser
**Exempelexperiment**

Ett exempel på ett klassificeringsproblem i två klasser är klassificeringen av irisblommor. Uppgiften är att klassificera iris blommor baserat på deras funktioner. Iris-datauppsättningen som tillhandahålls i Azure Machine Learning Studio (klassisk) är en delmängd av den populära [Iris-datauppsättningen](https://en.wikipedia.org/wiki/Iris_flower_data_set) som innehåller instanser av endast två blomarter (klasserna 0 och 1). Det finns fyra funktioner för varje blomma (sepal längd, foderbredd, kronblad längd och kronblad bredd).

![Skärmbild av irisexperiment](./media/interpret-model-results/1.png)

Bild 1. Iris tvåklass klassificering problem experiment

Ett experiment har utförts för att lösa detta problem, vilket visas i figur 1. En två-klass förstärkt beslut träd modell har tränats och poäng. Nu kan du visualisera förutsägelseresultaten från modulen [Poängmodell][score-model] genom att klicka på utdataporten för modulen [Poängmodell][score-model] och sedan klicka på **Visualisera**.

![Poängmodellmodul](./media/interpret-model-results/1_1.png)

Detta tar upp poängresultaten enligt figur 2.

![Resultat av iris tvåklassificeringsexperiment](./media/interpret-model-results/2.png)

Figur 2. Visualisera en poängmodell resultera i tvåklassklassificering

**Tolkning av resultat**

Det finns sex kolumner i resultattabellen. De vänstra fyra kolumnerna är de fyra funktionerna. De två högra kolumnerna, Poängsatta etiketter och poängsatta sannolikheter, är förutsägelseresultaten. Kolumnen Poängsatta sannolikheter visar sannolikheten för att en blomma tillhör den positiva klassen (klass 1). Det första talet i kolumnen (0,028571) betyder till exempel att sannolikheten är 0,028571 för att den första blomman tillhör klass 1. Kolumnen Poängsatta etiketter visar den förväntade klassen för varje blomma. Detta baseras på kolumnen Poängsatta sannolikheter. Om den poängsatta sannolikheten för en blomma är större än 0,5, förutspås den som klass 1. Annars förutspås det som klass 0.

**Webbtjänstpublikation**

När förutsägelseresultaten har förståtts och bedömts vara ljud kan experimentet publiceras som en webbtjänst så att du kan distribuera den i olika program och anropa den för att få klassprognoser på en ny irisblomma. Mer information om hur du ändrar ett utbildningsexperiment till ett bedömningsexperiment och publicerar det som en webbtjänst finns i [Självstudiekurs 3: Distribuera kreditriskmodell](tutorial-part3-credit-risk-deploy.md). Den här proceduren ger dig ett bedömningsexperiment som visas i figur 3.

![Skärmbild av bedömningsexperiment](./media/interpret-model-results/3.png)

Bild 3. Bedömning av iris tvåklassklassificeringsproblemexperiment

Nu måste du ställa in in- och utdata för webbtjänsten. Ingången är rätt ingångsport [av Score Model][score-model], som är Iris blomma funktioner ingång. Valet av utdata beror på om du är intresserad av den förväntade klassen (poängskade etikett), den poängsatta sannolikheten eller båda. I det här exemplet antas att du är intresserad av båda. Om du vill välja önskade utdatakolumner använder du en [Välj kolumner i datauppsättningsmodulen.][select-columns] Klicka på [Välj kolumner i datauppsättning,][select-columns]klicka på **Starta kolumnväljare**och välj **Poängsatta etiketter** och **Poängsatta sannolikheter**. När du har ställt in utdataporten [för Select Columns in Data set][select-columns] och kört den igen bör du vara redo att publicera bedömningsexperimentet som en webbtjänst genom att klicka på PUBLICERA **WEBBTJÄNST**. Det sista experimentet ser ut som figur 4.

![Iris klass klassificeringsexperiment](./media/interpret-model-results/4.png)

Bild 4. Slutlig scoring experiment av en iris två-klass klassificering problem

När du har kört webbtjänsten och angett vissa funktionsvärden för en testinstans returnerar resultatet två tal. Det första numret är den poängsatta etiketten, och den andra är den poängsatta sannolikheten. Denna blomma förutspås som klass 1 med 0,9655 sannolikhet.

![Testtolkningspoängmodell](./media/interpret-model-results/4_1.png)

![Poängsättningstestresultat](./media/interpret-model-results/5.png)

Bild 5. Webbtjänst resultat av iris tvåklass klassificering

### <a name="multi-class-classification"></a>Klassificering i flera klasser
**Exempelexperiment**

I det här experimentet utför du en kortigenkänningsuppgift som ett exempel på klassificering av fleraklasser. Klassificeraren försöker förutsäga en viss bokstav (klass) baserat på vissa handskrivna attributvärden som extraherats från de handskrivna bilderna.

![Exempel på brevigenkänning](./media/interpret-model-results/5_1.png)

I träningsdata finns det 16 funktioner som extraherats från handskrivna brevbilder. De 26 bokstäverna bildar våra 26 klasser. Figur 6 visar ett experiment som ska träna en klassificeringsmodell med flera klass för brevigenkänning och förutsäga på samma funktionsuppsättning på en testdatauppsättning.

![Experiment med klassificering av klassificering av brevigenkänning](./media/interpret-model-results/6.png)

Bild 6. Experiment med problemexperiment för klassificering av brevigenkänning

Visualisera resultaten från modulen [Poängmodell][score-model] genom att klicka på utdataporten för [scoremodellmodulen][score-model] och sedan klicka på **Visualisera,** du bör se innehållet som visas i figur 7.

![Poängmodellresultat](./media/interpret-model-results/7.png)

Bild 7. Visualisera poängmodell resulterar i en klassificering i flera klasser

**Tolkning av resultat**

De vänstra 16 kolumnerna representerar testuppsättningens funktionsvärden. Kolumnerna med namn som Poängsatta sannolikheter för klass "XX" är precis som kolumnen Poängsatta sannolikheter i tvåklassfallet. De visar sannolikheten för att motsvarande inträde faller i en viss klass. För den första posten är det till exempel 0,003571 sannolikhet att det är ett "A", 0,000451 sannolikhet att det är ett "B" och så vidare. Den sista kolumnen (Poängsatta etiketter) är samma som poängsatta etiketter i tvåklassfallet. Den väljer den klass med största poängsatta sannolikhet som den förväntade klassen för motsvarande post. För den första posten är till exempel den poängsatta etiketten "F" eftersom den har den största sannolikheten att vara ett "F" (0,916995).

**Webbtjänstpublikation**

Du kan också få den poängsatta etiketten för varje post och sannolikheten för den poängsatta etiketten. Den grundläggande logiken är att hitta den största sannolikheten bland alla poängsatta sannolikheter. För att göra detta måste du använda modulen [Kör R-skript.][execute-r-script] R-koden visas i figur 8 och resultatet av experimentet visas i figur 9.

![Exempel på R-kod](./media/interpret-model-results/8.png)

Figur 8. R-kod för att extrahera poängsatta etiketter och tillhörande sannolikheter för etiketterna

![Experimentresultat](./media/interpret-model-results/9.png)

Figur 9. Slutligt bedömningsexperiment av klassificeringsproblemet för flerklassklassificering av bokstäver

När du har publicerat och kört webbtjänsten och angett några indatafunktionsvärden ser det returnerade resultatet ut som bild 10. Detta handskrivna brev, med dess extraherade 16 funktioner, förutspås vara ett "T" med 0,9715 sannolikhet.

![Testtolkningspoängmodul](./media/interpret-model-results/9_1.png)

![Testresultat](./media/interpret-model-results/10.png)

Figur 10. Webbtjänst resultat av multiklass klassificering

## <a name="regression"></a>Regression
Regressionsproblem skiljer sig från klassificeringsproblem. I ett klassificeringsproblem försöker du förutsäga diskreta klasser, till exempel vilken klass en irisblomma tillhör. Men som du kan se i följande exempel på ett regressionsproblem försöker du förutsäga en kontinuerlig variabel, till exempel priset på en bil.

**Exempelexperiment**

Använd bil pris förutsägelse som ditt exempel för regression. Du försöker förutsäga priset på en bil baserat på dess funktioner, inklusive märke, bränsletyp, kroppstyp och drivhjul. Experimentet visas i figur 11.

![Bil pris regression experiment](./media/interpret-model-results/11.png)

Figur 11. Bil pris regression problem experiment

Visualisera [modulen Poängmodell,][score-model] resultatet ser ut som figur 12.

![Poängresultat för bilprisprognosproblem](./media/interpret-model-results/12.png)

Figur 12. Poängsättningsresultat för problemet med bilprisprognos

**Tolkning av resultat**

Poängsatta etiketter är resultatkolumnen i det här poängresultatet. Siffrorna är det förväntade priset för varje bil.

**Webbtjänstpublikation**

Du kan publicera regressionsexperimentet i en webbtjänst och anropa det för bilprisförutsägels på samma sätt som i användningsfallet för tvåklasser.

![Scoring experiment för bil pris regression problem](./media/interpret-model-results/13.png)

Figur 13. Scoring experiment av en bil pris regression problem

Köra webbtjänsten, det returnerade resultatet ser ut som figur 14. Det förväntade priset för denna bil är $ 15,085.52.

![Testtolkning av poängsättningsmodul](./media/interpret-model-results/13_1.png)

![Resultat av bedömningsmodul](./media/interpret-model-results/14.png)

Figur 14. Webbtjänst resultatet av ett problem med tillbakagång av bilpriser

## <a name="clustering"></a>Klustring
**Exempelexperiment**

Låt oss använda Iris-datauppsättningen igen för att skapa ett klusterexperiment. Här kan du filtrera bort klassetiketterna i datauppsättningen så att den bara har funktioner och kan användas för klustring. I det här iris användningsfallet anger du antalet kluster som ska vara två under utbildningsprocessen, vilket innebär att du skulle gruppera blommorna i två klasser. Experimentet visas i figur 15.

![Iris klustring problem experiment](./media/interpret-model-results/15.png)

Figur 15. Iris klustring problem experiment

Klustring skiljer sig från klassificeringen genom att utbildningsdatauppsättningen inte har markanningsetiketter av sig själv. Kluster grupperar utbildningsdatauppsättningsinstanserna i olika kluster. Under utbildningsprocessen etiketterar modellen posterna genom att lära sig skillnaderna mellan deras funktioner. Därefter kan den tränade modellen användas för att ytterligare klassificera framtida poster. Det finns två delar av resultatet som vi är intresserade av inom ett klusterproblem. Den första delen är att märka träningsdatauppsättningen och den andra klassificerar en ny datauppsättning med den tränade modellen.

Den första delen av resultatet kan visualiseras genom att klicka på den vänstra utdataporten [i Train Clustering Model][train-clustering-model] och sedan klicka på **Visualisera**. Visualiseringen visas i figur 16.

![Klusterresultat](./media/interpret-model-results/16.png)

Figur 16. Visualisera klusterresultat för träningsdatauppsättningen

Resultatet av den andra delen, kluster av nya poster med den tränade klustermodellen, visas i figur 17.

![Visualisera klusterresultat](./media/interpret-model-results/17.png)

Figur 17. Visualisera klustringsresultat på en ny datauppsättning

**Tolkning av resultat**

Även om resultaten av de två delarna härrör från olika experiment stadier, de ser likadana ut och tolkas på samma sätt. De första fyra kolumnerna är funktioner. Den sista kolumnen, Tilldelningar, är förutsägelseresultatet. De transaktioner som tilldelas samma nummer förutspås finnas i samma kluster, det vill än att de delar likheter på något sätt (det här experimentet använder standardmåttet för euklidiska avstånd). Eftersom du har angett antalet kluster som ska vara 2, är posterna i Tilldelningar märkt antingen 0 eller 1.

**Webbtjänstpublikation**

Du kan publicera klusterexperimentet i en webbtjänst och anropa det för klustring förutsägelser på samma sätt som i två klassens klassificering användningsfall.

![Bedömningsexperiment för iris-klusterproblem](./media/interpret-model-results/18.png)

Figur 18. Bedömningsexperiment av ett iris-klusterproblem

När du har kört webbtjänsten ser det returnerade resultatet ut som bild 19. Denna blomma förutspås vara i kluster 0.

![Testtolkningsmodul](./media/interpret-model-results/18_1.png)

![Resultat för bedömningsmodul](./media/interpret-model-results/19.png)

Figur 19. Webbtjänst resultat av iris tvåklass klassificering

## <a name="recommender-system"></a>Recommender system
**Exempelexperiment**

För rekommenderar system kan du använda problemet med restaurangrekommendation som ett exempel: du kan rekommendera restauranger för kunder baserat på deras betygshistorik. Ingångsdata består av tre delar:

* Restaurangbetyg från kunder
* Kundfunktionsdata
* Funktionersdata för restaurang

Det finns flera saker vi kan göra med modulen [Train Matchbox Recommender][train-matchbox-recommender] i Azure Machine Learning Studio (klassiskt):

* Förutsäga klassificeringar för en viss användare och ett visst objekt
* Rekommendera objekt till en viss användare
* Hitta användare som är relaterade till en viss användare
* Söka efter artiklar som är relaterade till en viss artikel

Du kan välja vad du vill göra genom att välja från de fyra alternativen i **menyn Rekommendera förutsägelsesnat.** Här kan du gå igenom alla fyra scenarier.

![Matchbox recommender](./media/interpret-model-results/19_1.png)

Ett typiskt Azure Machine Learning Studio -experiment (klassiskt) för ett rekommenderade system ser ut som figur 20. Information om hur du använder dessa rekommenderade systemmoduler finns i Rekommenderare för [tågmatchbox][train-matchbox-recommender] och [Score matchbox recommender][score-matchbox-recommender].

![Experiment med rekommenderan system](./media/interpret-model-results/20.png)

Figur 20. Experiment med rekommenderan system

**Tolkning av resultat**

**Förutsäga klassificeringar för en viss användare och ett visst objekt**

Genom att välja **Klassificeringsprediktion** under **Rekommenderae förutsägelsesort**ber du rekommendationssystemet att förutsäga klassificeringen för en viss användare och ett visst objekt. Visualiseringen av [resultatmatchningskomstseringsutdata][score-matchbox-recommender] ser ut som bild 21.

![Poängresultat för rekommendationssystemet – betygsförutsägels](./media/interpret-model-results/21.png)

Figur 21. Visualisera poängresultatet för rekommendationssystemet - prognos för

De två första kolumnerna är användarobjektparen som tillhandahålls av indata. Den tredje kolumnen är den förväntade klassificeringen av en användare för ett visst objekt. I den första raden beräknas till exempel kunden U1048 betygsätta restaurang 135026 som 2.

**Rekommendera objekt till en viss användare**

Genom att välja **Artikelrekommendation** under **Rekommenderae förutsägelsesort**ber du rekommendationssystemet att rekommendera objekt till en viss användare. Den sista parametern att välja i det här scenariot är *Rekommenderad artikelval*. Alternativet **Från nominella objekt (för modellutvärdering)** är främst för modellutvärdering under utbildningsprocessen. För det här förutsägelsestadiet väljer vi **Från alla objekt**. Visualiseringen av [score matchbox recommenderutdata][score-matchbox-recommender] ser ut som bild 22.

![Poängresultat för rekommenderasystemet – artikelrekommendation](./media/interpret-model-results/22.png)

Figur 22. Visualisera poängresultatet för rekommendationen för rekommenderade systemet - artikel

Den första av de sex kolumnerna representerar de angivna användar-ID:na att rekommendera objekt för, enligt indata. De övriga fem kolumnerna representerar de objekt som rekommenderas för användaren i fallande ordning av relevans. I den första raden är till exempel den mest rekommenderade restaurangen för kunden U1048 134986, följt av 135018, 134975, 135021 och 132862.

**Hitta användare som är relaterade till en viss användare**

Genom att välja **Relaterade användare** under **Rekommenderare förutsägelse typ**, du ber recommender systemet för att hitta relaterade användare till en viss användare. Relaterade användare är de användare som har liknande inställningar. Den sista parametern att välja i det här scenariot är *Relaterat användarval*. Alternativet **Från användare som betygsatt objekt (för modellutvärdering)** är främst för modellutvärdering under utbildningsprocessen. Välj **Från alla användare** för det här förutsägelsestadiet. Visualiseringen av [score matchbox recommenderutdata][score-matchbox-recommender] ser ut som bild 23.

![Poängresultat för rekommenderade system - relaterade användare](./media/interpret-model-results/23.png)

Figur 23. Visualisera poängresultat för de rekommenderade systemrelaterade användarna

Den första av de sex kolumnerna visar de givna användar-ID:erna som behövs för att hitta relaterade användare, enligt indata. De andra fem kolumnerna lagrar de förväntade relaterade användarna av användaren i fallande ordning av relevans. I den första raden är till exempel den mest relevanta kunden för kunden U1048 U1051, följt av U1066, U1044, U1017 och U1072.

**Söka efter artiklar som är relaterade till en viss artikel**

Genom att välja **Relaterade objekt** under **Rekommenderae förutsägelse slag,** du ber recommender systemet för att hitta relaterade objekt till ett visst objekt. Relaterade objekt är de objekt som mest sannolikt kommer att gillas av samma användare. Den sista parametern som du vill välja i det här scenariot är *Relaterad artikelval*. Alternativet **Från nominella objekt (för modellutvärdering)** är främst för modellutvärdering under utbildningsprocessen. Vi väljer **Från alla objekt** för den här förutsägelsefasen. Visualiseringen av [score matchbox recommenderutdata][score-matchbox-recommender] ser ut som bild 24.

![Poängresultat för rekommenderade system --relaterade objekt](./media/interpret-model-results/24.png)

Figur 24. Visualisera poängresultat för de rekommenderade systemrelaterade objekten

Den första av de sex kolumnerna representerar de angivna artikel-ID:na som behövs för att hitta relaterade objekt, enligt indata. De övriga fem kolumnerna lagrar de förväntade relaterade artiklarna för artikeln i fallande ordning när det gäller relevans. På den första raden är till exempel det mest relevanta objektet för artikel 135026 135074, följt av 135035, 132875, 135055 och 134992.

**Webbtjänstpublikation**

Processen att publicera dessa experiment som webbtjänster för att få förutsägelser är liknande för vart och ett av de fyra scenarierna. Här tar vi det andra scenariot (rekommendera objekt till en viss användare) som ett exempel. Du kan följa samma procedur med de andra tre.

Spara det tränade recommendersystemet som en utbildad modell och filtrera indata till en enda användar-ID-kolumn som begärts, du kan koppla in experimentet som i figur 25 och publicera det som en webbtjänst.

![Poängsexperiment av restaurangrekommendationsproblemet](./media/interpret-model-results/25.png)

Figur 25. Poängsexperiment av restaurangrekommendationsproblemet

Köra webbtjänsten, det returnerade resultatet ser ut som figur 26. De fem rekommenderade restaurangerna för användare U1048 är 134986, 135018, 134975, 135021 och 132862.

![Exempel på rekommenderade systemservice](./media/interpret-model-results/25_1.png)

![Exempel på experimentresultat](./media/interpret-model-results/26.png)

Figur 26. Webbtjänst resultat av restaurang rekommendation problem

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
