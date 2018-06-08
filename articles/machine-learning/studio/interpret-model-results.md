---
title: Tolka modellen resultaten i Machine Learning | Microsoft Docs
description: Så här väljer du den optimala parameter har angetts för en algoritm med och visualisera poängsätta modell utdata.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6230e5ab-a5c0-4c21-a061-47675ba3342c
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: e62f4716a95a6c1840afde27361eeb756abff335
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837012"
---
# <a name="interpret-model-results-in-azure-machine-learning"></a>Tolka modellen resultaten i Azure Machine Learning
Det här avsnittet beskriver hur du visualisera och tolka förutsägelse resultaten i Azure Machine Learning Studio. När du har en modell tränas och göra förutsägelser ovanpå det (”bedömas modellen”), måste du förstå och tolka resultatet förutsägelse.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Det finns fyra huvudsakliga typer av machine learning-modeller i Azure Machine Learning:

* Klassificering
* Klustring
* Regression
* Rekommenderare system

Moduler som används för förutsägelse ovanpå dessa modeller är:

* [Bedömningsmodell] [ score-model] -modulen för klassificering och regression
* [Tilldela kluster] [ assign-to-clusters] -modulen för kluster
* [Poängsätta Matchbox rekommenderare] [ score-matchbox-recommender] för rekommendation system

Det här dokumentet beskrivs hur du tolkar förutsägelse resultat för var och en av dessa moduler. En översikt av dessa moduler finns [hur du väljer parametrar för att optimera algoritmerna i Azure Machine Learning](algorithm-parameters-optimize.md).

Det här avsnittet tar förutsägelse tolkning men inte modellen utvärdering. Mer information om hur du utvärderar din modell finns [så att utvärdera modellen prestanda i Azure Machine Learning](evaluate-model-performance.md).

Om du har använt Azure Machine Learning och behöver hjälp med att skapa ett enkelt experiment att sätta igång, se [skapa ett enkelt experiment i Azure Machine Learning Studio](create-experiment.md) i Azure Machine Learning Studio.

## <a name="classification"></a>Klassificering
Det finns två underkategorier klassificering problem:

* Problem med bara två klasser (tvåklass eller binär klassificering)
* Problem med fler än två klasser (flera klassen klassificering)

Azure Machine Learning har olika moduler för att hantera de olika typerna av klassificering, men metoderna för att tolka resultaten förutsägelse är samma.

### <a name="two-class-classification"></a>Tvåklass klassificering
**Exempel experiment**

Klassificering av iris blommor är ett exempel på en två-klass klassificeringsproblem. Aktiviteten är att klassificera iris blommor baserat på deras funktioner. Iris datauppsättningen i Azure Machine Learning är en delmängd av den populära [Iris datauppsättning](http://en.wikipedia.org/wiki/Iris_flower_data_set) som innehåller instanser av endast två blomma art (klasser 0 och 1). Det finns fyra funktioner för varje blomma (sepal längd, sepal bredd, bladets längd och bladets bredd).

![Skärmbild av iris experiment](./media/interpret-model-results/1.png)

Bild 1. Iris tvåklass klassificering problemet experiment

Ett experiment har utförts för att lösa problemet, som visas i bild 1. En två-klass ökat beslut trädet modell har tränats och bedömas. Nu kan du visualisera förutsägelse resultaten från den [Poängmodell] [ score-model] modulen genom att klicka på utdataporten för den [Poängmodell] [ score-model] modulen och sedan klicka på **visualisera**.

![Modulen poängsätta modell](./media/interpret-model-results/1_1.png)

Detta öppnar bedömningsprofil resultat som visas i bild 2.

![Resultat av iris tvåklass klassificering experiment](./media/interpret-model-results/2.png)

Figur 2. Visualisera poängsätta modell resultatet i två klassen klassificering

**Tolkning av resultat**

Det finns sex kolumner i resultattabellen. De vänstra fyra kolumnerna är fyra funktioner. Höger två kolumner, poängsatta etiketter och bedömas sannolikhet finns förutsägelse resultat. Kolumnen bedömas sannolikhet visar sannolikheten som en blomma tillhör klassen positivt (klass 1). Det första numret i den kolumnen (0.028571) innebär att det inte är till exempel 0.028571 sannolikhet att första blomma tillhör klass 1. Poängsatta etiketter kolumnen visar förväntade klassen för varje blomma. Detta baseras på kolumnen bedömas sannolikhet. Om poängsatta sannolikheten för en blomma är större än 0,5 prognoser den som klass 1. Det är också förutsade som klass 0.

**Tjänstepublicering för webbprogram**

När resultatet förutsägelse har förstått och bedöms ljud, kan experimentet publiceras som en webbtjänst så att du kan distribuera i olika program och kalla den för att hämta klass förutsägelser på alla nya iris blommor. Information om hur du ändrar en träningsexperiment i ett bedömningsprofil experiment och publicera den som en webbtjänst finns [publicera Azure Machine Learning-webbtjänst](walkthrough-5-publish-web-service.md). Den här proceduren innehåller ett bedömningsprofil experiment som visas i bild 3.

![Skärmbild av bedömningen experiment](./media/interpret-model-results/3.png)

Bild 3. Bedömningen iris tvåklass klassificering problemet experiment

Nu måste du ange indata och utdata för webbtjänsten. Indata är den högra indataporten av [Poängmodell][score-model], vilket är Iris blomma funktioner indata. Valet av utdata beror på om du är intresserad av klassen förväntade (poängsatta etikett), poängsatt sannolikhet eller båda. I det här exemplet förutsätts att du är intresserad av båda. Markera önskad utdatakolumnerna genom att använda en [Välj kolumner i datauppsättning] [ select-columns] modul. Klicka på [Välj kolumner i datauppsättning][select-columns], klickar du på **starta kolumnväljaren**, och välj **poängsatta etiketter** och **Scored Troliga**. När du har angett utdataporten för [Välj kolumner i datauppsättning] [ select-columns] och kör igen, bör du vara redo att publicera bedömningsprofil experiment som en webbtjänst genom att klicka på **publicera WEB SERVICE** . Det slutliga experimentet ser ut som bild 4.

![Iris tvåklass klassificering experiment](./media/interpret-model-results/4.png)

Bild 4. Sista bedömningsprofil experiment för en iris tvåklass klassificeringsproblem

När du kör webbtjänsten och ange några värden för funktionen på en instans av test returnerar resultatet två tal. Den första siffran är poängsatta etikett och andra är poängsatt sannolikhet. Den här blomma prognoser som klass 1 med 0.9655 sannolikhet.

![Testa tolkar poängsätta modell](./media/interpret-model-results/4_1.png)

![Testresultat för bedömningsprofil](./media/interpret-model-results/5.png)

Bild 5. Web service resultatet av iris tvåklass klassificering

### <a name="multi-class-classification"></a>Flera klassen klassificering
**Exempel experiment**

I det här experimentet utför du en bokstav recognition uppgift är ett exempel på multiklass-baserad klassificering. Klassificeraren försöker att förutsäga en viss bokstav (klassen) baserat på vissa handskriven attributvärden som extraheras från handskriven bilder.

![Bokstav recognition exempel](./media/interpret-model-results/5_1.png)

Det finns 16 funktioner som extraheras från handskriven bokstav bilder i utbildning-data. 26 bokstäver utgör våra 26 klasser. Bild 6 visar ett experiment som tränar en modell för multiklass-baserad klassificering för bokstav recognition och förutsäga på samma funktioner på en datauppsättning för testet.

![Bokstav recognition multiklass-baserad klassificering experiment](./media/interpret-model-results/6.png)

Bild 6. Bokstav recognition multiklass-baserad klassificering problemet experiment

Visualisera resultaten från den [Poängmodell] [ score-model] modulen genom att klicka på utdataporten för [Poängmodell] [ score-model] modul och klicka sedan på **Visualisera**, bör du se innehåll som visas på bild 7.

![Modellen poängresultat](./media/interpret-model-results/7.png)

Bild 7. Visualisera poängresultat modell i en klass med flera klassificering

**Tolkning av resultat**

De vänstra 16 kolumnerna representerar funktionen värden för uppsättningen test. Kolumnerna med namn som bedömas sannolikhet för klassen ”XX” är precis som kolumnen bedömas sannolikhet i fallet med två-klass. De visar sannolikheten som motsvarande post hamnar i en viss klass. Till exempel för den första posten finns 0.003571 sannolikheten för att det är ett ”A” 0.000451 sannolikheten för att det är en ”B” och så vidare. Den sista kolumnen (poängsatta etiketter) är detsamma som poängsatta etiketter i fallet med två-klass. Klassen med största poängsatt sannolikhet väljs som motsvarande post förväntade klassen. Till exempel för den första posten poängsatta etiketten är ”F” eftersom den har största sannolikhet att vara en ”F” (0.916995).

**Tjänstepublicering för webbprogram**

Du kan också visa poängsatta etiketter för varje post och sannolikheten för poängsatta etiketter. Den grundläggande logiken är att hitta den största sannolikheten bland poängsatt sannolikhet. Om du vill göra detta måste du använda den [köra R-skriptet] [ execute-r-script] modul. R-kod visas i figur 8 och resultatet av experimentet illustreras i bild 9.

![R-kodexempel](./media/interpret-model-results/8.png)

Figur 8. R-koden för att extrahera poängsatta etiketter och associerade troliga etiketter

![Experiment resultat](./media/interpret-model-results/9.png)

Bild 9. Sista bedömningsprofil experiment problemets bokstav recognition multiklass-baserad klassificering

När du publicerar och kör webbtjänsten och ange vissa inkommande funktionen värden, returnerade resultatet ser ut som bild 10. Den här handskriven bokstav, med dess extraherade 16 funktioner prognoser ska ”D” med 0.9715 sannolikhet.

![Testa tolkar score-modul](./media/interpret-model-results/9_1.png)

![Testresultat](./media/interpret-model-results/10.png)

Bild 10. Web service resultatet av multiklass-baserad klassificering

## <a name="regression"></a>Regression
Regressionsproblem skiljer sig från klassificering problem. I en klassificering problemet försöker du förutsäga diskreta klasser, t.ex vilka klassen en iris blomma tillhör. Men som du ser i följande exempel visar en regression problemet du försöker att förutsäga ett kontinuerligt variabel, exempelvis priset på en bil.

**Exempel experiment**

Använda bil pris förutsägelse som ditt exempel för regression. Du försöker att förutsäga priset på en bil baserat på dess funktioner, inklusive se, bränsletyp, brödtexten och hjul för enheten. Experimentet visas i figur 11.

![Bil pris regression experiment](./media/interpret-model-results/11.png)

Figur 11. Bil pris regression problemet experiment

Visualisera de [Poängmodell] [ score-model] modulen resultatet ser ut som figur 12.

![Bedömningsprofil resultat för bil pris förutsägelse problem](./media/interpret-model-results/12.png)

Figur 12. Bedömningsprofil resultat för bil pris förutsägelse problemet

**Tolkning av resultat**

Poängsatta etiketter är resultatkolumnen i resultatet för den här bedömningsprofil. Siffrorna är det förväntade priset för varje bil.

**Tjänstepublicering för webbprogram**

Du kan publicera regression experimentet till en webbtjänst och anropa den för en bil pris prognos på samma sätt som i fallet för användning av tvåklass klassificering.

![Bedömningen experiment för bil pris regression problem](./media/interpret-model-results/13.png)

Figur 13. Bedömningen experiment på en bil pris regression problem

Kör webbtjänsten det returnerade resultatet ser ut som figur 14. Förväntade priset för den här bil är $15,085.52.

![Testa tolkar poängsättningsmodul](./media/interpret-model-results/13_1.png)

![Bedömningen modulen resultat](./media/interpret-model-results/14.png)

Figur 14. Web service-resultatet av en bil pris regression problem

## <a name="clustering"></a>Klustring
**Exempel experiment**

Nu ska vi använda igen datauppsättningen Iris för att skapa ett kluster experiment. Här kan du filtrera ut klassen etiketterna i datauppsättningen så att den bara innehåller funktioner och kan användas i kluster. I den här iris användningsfall genom att ange antalet kluster som ska vara två under processen utbildning, vilket innebär att du vill gruppera blommorna i två klasser. Experimentet visas i figur 15.

![Iris klustring problemet experiment](./media/interpret-model-results/15.png)

Figur 15. Iris klustring problemet experiment

Kluster till skillnad från klassificering av datauppsättning för träning saknar grunden sanningen etiketter ensamt. Klustring grupper utbildning datauppsättning instanser i olika kluster. Under processen utbildning märker modellen posterna genom att lära dig skillnaderna mellan deras funktioner. Efter att användas den tränade modellen för att ytterligare klassificera framtida transaktioner. Det finns två delar av resultatet vi är intresserade i ett kluster problem. Den första delen är etiketter datauppsättning för träning och andra klassificerar en ny datamängd med den tränade modellen.

Den första delen av resultatet kan vara visualiseras genom att klicka på den vänstra utdataporten för [Clustering Träningsmodell] [ train-clustering-model] och sedan klicka på **visualisera**. Visualiseringen illustreras i bild 16.

![Klustring resultat](./media/interpret-model-results/16.png)

Bild 16. Visualisera resultat för datauppsättning för träning-kluster

Resultatet av den andra delen, klustring nya poster med utbildade klustringsmodell illustreras i bild 17.

![Visualisera clustering resultat](./media/interpret-model-results/17.png)

Figur 17. Visualisera klustring resultat på en ny datauppsättning

**Tolkning av resultat**

Även om resultatet av två delar härrör från olika experiment faser, ser likadana ut och tolkas på samma sätt. De fyra första kolumnerna är funktioner. Den sista kolumnen tilldelningar är resultatet för förutsägelse. Poster som har tilldelats samma nummer bedöms vara i samma kluster, det vill säga de delar likheter på något sätt (experimentet använder Euclidean avstånd standardmått). Eftersom du har angett antalet kluster 2 är posterna i tilldelningar märkta 0 eller 1.

**Tjänstepublicering för webbprogram**

Du kan publicera klustring experimentet till en webbtjänst och anropa för klustring förutsägelser på samma sätt som klassificeringen tvåklass användningsfall.

![Bedömningen experiment för iris klustring problem](./media/interpret-model-results/18.png)

Bild 18. Bedömningen experiment för ett kluster iris-problem

När du har kört webbtjänsten det returnerade resultatet ser ut som bild 19. Den här blomma prognoser i klustret 0.

![Testa tolka poängsättningsmodul](./media/interpret-model-results/18_1.png)

![Bedömningen modulen resultat](./media/interpret-model-results/19.png)

Bild 19. Web service resultatet av iris tvåklass klassificering

## <a name="recommender-system"></a>Rekommenderare system
**Exempel experiment**

För rekommenderare system, du kan använda restaurang rekommendation problemet som ett exempel: du kan rekommendera hotell för kunder baserat på deras klassificering historik. Indata består av tre delar:

* Restaurang klassificeringar från kunder
* Funktionen kundinformation
* Restaurang funktionsdata

Det finns flera saker som vi kan göra med den [Train Matchbox rekommenderare] [ train-matchbox-recommender] modul i Azure Machine Learning:

* Förutsäga klassificering för en viss användare och objekt
* Rekommendera objekt till en viss användare
* Hitta användare som hör till en viss användare
* Sök efter objekt som är relaterade till ett visst objekt

Du kan välja vad du vill göra genom att välja från de fyra alternativen i den **rekommenderare förutsägelse typ** menyn. Här kan du gå igenom alla fyra scenarier.

![Matchbox rekommenderare](./media/interpret-model-results/19_1.png)

En typisk Azure Machine Learning-experiment för ett system för rekommenderare ser ut som bild 20. Information om hur du använder dessa moduler för rekommenderare system finns [Train matchbox rekommenderare] [ train-matchbox-recommender] och [poäng matchbox rekommenderare] [ score-matchbox-recommender].

![Rekommenderare system experiment](./media/interpret-model-results/20.png)

Figur 20. Rekommenderare system experiment

**Tolkning av resultat**

**Förutsäga klassificering för en viss användare och objekt**

Genom att välja **klassificering förutsägelse** under **rekommenderare förutsägelse typ**, ber du rekommenderare systemet att förutsäga klassificeringen för en viss användare och objekt. Visualisering av den [poäng Matchbox rekommenderare] [ score-matchbox-recommender] utdata ser ut som bild 21.

![Poängsätta resultatet av systemets rekommenderare--klassificeringen förutsägelse](./media/interpret-model-results/21.png)

Figur 21. Visualisera poäng resultatet av systemets rekommenderare--klassificeringen förutsägelse

De två första kolumnerna är användaren objekt-par som tillhandahålls av indata. Den tredje kolumnen är den förväntade klassificeringen av en användare för ett visst objekt. Till exempel på den första raden prognoser kunden U1048 till hastighet restaurang 135026 som 2.

**Rekommendera objekt till en viss användare**

Genom att välja **objektet rekommendation** under **rekommenderare förutsägelse typ**, begär du rekommenderare systemet att rekommendera objekt till en viss användare. Den sista parametern för att välja i det här scenariot är *rekommenderas val av återställningsobjekt*. Alternativet **från klassificerade av objekt (för utvärdering av modellen)** är främst avsedd för utvärdering av modellen under processen utbildning. För det här steget förutsägelse vi väljer **från alla objekt**. Visualisering av den [poäng Matchbox rekommenderare] [ score-matchbox-recommender] utdata ser ut som figur 22.

![Poäng resultatet av rekommenderare system--objektet rekommendation](./media/interpret-model-results/22.png)

Figur 22. Visualisera poäng resultatet av systemets rekommenderare--objektet rekommendation

Första sex kolumnerna representerar angivna användar-ID att rekommendera objekt, som tillhandahålls av indata. De fem kolumnerna representerar rekommenderas till användaren i fallande ordning relevanta objekt. I den första raden är till exempel rekommenderas restaurang för kund U1048 134986, följt av 135018, 134975, 135021 och 132862.

**Hitta användare som hör till en viss användare**

Genom att välja **relaterade användare** under **rekommenderare förutsägelse typ**, begär du rekommenderare systemet söker efter relaterade användare till en viss användare. Relaterade användare kan de användare som har liknande inställningar. Den sista parametern för att välja i det här scenariot är *relaterade användarens val*. Alternativet **från användare att klassificerade objekt (för utvärdering av modellen)** är främst avsedd för utvärdering av modellen under processen utbildning. Välj **från alla användare** för det här steget för förutsägelse. Visualisering av den [poäng Matchbox rekommenderare] [ score-matchbox-recommender] utdata ser ut som bild 23.

![Poäng resultatet av rekommenderare system--relaterade användare](./media/interpret-model-results/23.png)

Figur 23. Visualisera poängresultat rekommenderare systemets--relaterade användare

Första sex kolumner visas den angivna användaren ID krävs för att hitta relaterade användare som tillhandahålls av indata. De fem kolumnerna lagra förväntade relaterade användare av användaren i fallande ordning relevanta. I den första raden är mest relevant kunden för kunden U1048 exempelvis U1051, följt av U1066, U1044, U1017 och U1072.

**Sök efter objekt som är relaterade till ett visst objekt**

Genom att välja **relaterade objekt** under **rekommenderare förutsägelse typ**, ber du rekommenderare systemet att söka efter relaterade objekt till ett visst objekt. Relaterade objekt är sannolikt tyckte av samma användare artiklar. Den sista parametern för att välja i det här scenariot är *relaterade val av återställningsobjekt*. Alternativet **från klassificerade av objekt (för utvärdering av modellen)** är främst avsedd för utvärdering av modellen under processen utbildning. Vi väljer **från alla objekt** för det här steget för förutsägelse. Visualisering av den [poäng Matchbox rekommenderare] [ score-matchbox-recommender] utdata ser ut som bild 24.

![Poäng resultatet av rekommenderare system, relaterade objekt](./media/interpret-model-results/24.png)

Figur 24. Visualisera poängresultat rekommenderare systemets--relaterade objekt

Första sex kolumnerna representerar det angivna objektet-ID krävs för att söka efter relaterade objekt som indata. De fem kolumnerna lagra förväntade relaterade objekt för objekt i fallande ordning enligt betydelse. I den första raden är mest relevant artikel för objektet 135026 exempelvis 135074, följt av 135035, 132875, 135055 och 134992.

**Tjänstepublicering för webbprogram**

Processen för att publicera dessa experiment som webbtjänster som ska få förutsägelser är liknande för var och en av de fyra scenarierna. Vi ta här i det andra scenariot (rekommenderar objekt till en viss användare) som ett exempel. Du kan följa samma procedur med de övriga tre.

Spara utbildade rekommenderare system som en tränad modell och filtrering indata till en enskild användare ID-kolumnen enligt begäran, kan du koppla samman experiment som bild 25 och publicera den som en webbtjänst.

![Bedömningen experiment restaurang rekommendation problemet](./media/interpret-model-results/25.png)

Bild 25. Bedömningen experiment restaurang rekommendation problemet

Kör webbtjänsten det returnerade resultatet ser ut som bild 26. Fem rekommenderade hotell för användaren U1048 är 134986, 135018, 134975, 135021 och 132862.

![Exempel på rekommenderare systemtjänst](./media/interpret-model-results/25_1.png)

![Exempel på resultat experiment](./media/interpret-model-results/26.png)

Bild 26. Web service resultatet av restaurang rekommendation problem

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
