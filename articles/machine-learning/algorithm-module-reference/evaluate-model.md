---
title: 'Utvärdera modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen utvärdera modell i Azure Machine Learning för att mäta noggrannheten i en utbildad modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: a665ee97f923620bb484243d5cd4904a647969e4
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917448"
---
# <a name="evaluate-model-module"></a>Utvärdera modell modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att mäta noggrannheten för en utbildad modell. Du anger en data uppsättning som innehåller resultat som genererats från en modell, och modulen **utvärdera modell** beräknar en uppsättning utvärderings mått som är bransch standard.
  
 Måtten som returneras av **evaluate-modellen** beror på vilken typ av modell du utvärderar:  
  
-   **Klassificerings modeller**    
-   **Regressions modeller**  
-   **Kluster modeller**  


> [!TIP]
> Om du inte har använt modell utvärderingen rekommenderar vi video serien av Dr. Stephen Elston som en del av [Machine Learning-kursen](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) från EDX. 


Det finns tre sätt att använda modulen **utvärdera modell** :

+ Generera poäng över dina utbildnings data och utvärdera modellen baserat på dessa Poäng
+ Generera poäng i modellen, men jämför dessa resultat med resultat från en reserverad test uppsättning
+ Jämför poängen för två olika men relaterade modeller med samma uppsättning data

## <a name="use-the-training-data"></a>Använd tränings data

Om du vill utvärdera en modell måste du ansluta en data uppsättning som innehåller en uppsättning kolumner och resultat.  Om inga andra data är tillgängliga kan du använda den ursprungliga data uppsättningen.

1. Anslut poängen för **data uppsättningen** i [Poäng modellen](./score-model.md) till indata för **utvärdera modell**. 
2. Klicka på **utvärdera modell** modul och kör pipelinen för att generera utvärderings poängen.

## <a name="use-testing-data"></a>Använd test data

Ett vanligt scenario i Machine Learning är att separera din ursprungliga data uppsättning till utbildning och testning av data uppsättningar, med hjälp av modulen [Split](./split-data.md) eller modulen [partition och exempel](./partition-and-sample.md) . 

1. Anslut poängen för **data uppsättningen** i [Poäng modellen](score-model.md) till indata för **utvärdera modell**. 
2. Anslut utdata från modulen dela data som innehåller test data till den högra inmatningen av **utvärdera modell**.
2. Klicka på **utvärdera modell** modul och välj **Kör valt** för att generera utvärderings poängen.

## <a name="compare-scores-from-two-models"></a>Jämför resultat från två modeller

Du kan också ansluta en andra uppsättning Poäng för att **utvärdera modellen**.  Poängen kan vara en delad utvärderings uppsättning som har kända resultat eller en uppsättning resultat från en annan modell för samma data.

Den här funktionen är användbar eftersom du enkelt kan jämföra resultat från två olika modeller på samma data. Eller så kan du jämföra resultat från två olika körningar över samma data med olika parametrar.

1. Anslut poängen för **data uppsättningen** i [Poäng modellen](score-model.md) till indata för **utvärdera modell**. 
2. Anslut utdata från modulen Poäng modell för den andra modellen till den högra inmatningen av **utvärdera modell**.
3. Köra en pipeline.

## <a name="results"></a>Resultat

När du har kört **utvärdera modell**högerklickar du på modulen och väljer **visualisera utvärderings resultat** för att se resultatet.

Om du ansluter data uppsättningar till båda indata för **utvärdera modell**kommer resultatet att innehålla mått för båda data uppsättningarna eller båda modellerna.
Modellen eller data som är kopplade till den vänstra porten visas först i rapporten följt av måtten för data uppsättningen eller modellen som är kopplad till rätt port.  

Följande bild visar till exempel en jämförelse av resultat från två kluster modeller som bygger på samma data, men med olika parametrar.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Eftersom det här är en kluster modell är utvärderings resultatet annorlunda än om du jämför resultat från två Regressions modeller eller jämför två klassificerings modeller. Den övergripande presentationen är dock densamma. 

## <a name="metrics"></a>Mått

I det här avsnittet beskrivs de mått som returneras för de olika typerna av modeller som stöds för användning med **utvärdera modell**:

+ [klassificerings modeller](#metrics-for-classification-models)
+ [Regressions modeller](#metrics-for-regression-models)
+ [kluster modeller](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Mått för klassificerings modeller

Följande mått rapporteras när klassificerings modeller utvärderas.
  
-   **Noggrannhet** mäter en klassificerings modells lämplighet som andel sanna resultat av totalt antal fall.  
  
-   **Precision** är förhållandet mellan faktiska resultat och alla positiva resultat.  
  
-   **Återkallande** är bråk delen av alla korrekta resultat som returneras av modellen.  
  
-   **F-score** beräknas som viktat medelvärde för precision och åter kallelse mellan 0 och 1, där det idealiska värdet F-Poäng är 1.  
  
-   **AUC** mäter ytan under kurvan som ritas med sant positiva resultat på y-axeln och falska positiva identifieringar på x-axeln. Det här måttet är användbart eftersom det innehåller ett enda nummer som gör det möjligt att jämföra modeller av olika typer.  
  
- **Genomsnittlig logg förlust** är ett enda poäng som används för att uttrycka sanktionen för fel resultat. Det beräknas som skillnaden mellan två sannolikhets fördelningar – det sanna, och den som finns i modellen.  
  
- **Inlärnings logg förlusten** är en enda poäng som representerar fördelen med klassificeraren över en slumpmässig förutsägelse. Logg förlusten mäter osäkerheten för din modell genom att jämföra sannolikheten att den matas till kända värden (mark sanningen) i etiketterna. Du vill minimera logg förlusten för modellen som helhet.

### <a name="metrics-for-regression-models"></a>Mät värden för Regressions modeller
 
Måtten som returneras för Regressions modeller är utformade för att uppskatta mängden fel.  En modell anses vara bra anpassad om skillnaden mellan observerade och förväntade värden är liten. Men om du tittar på resten av resten (skillnaden mellan en förutsägande punkt och dess motsvarande faktiska värde) kan du få mycket information om potentiell förskjutning i modellen.  
  
 Följande mått rapporteras för utvärdering av Regressions modeller.
  
- **Medelvärde för absolut fel (Mae)** hur nära förutsägelserna är till de faktiska resultatet; Därför är en lägre poäng bättre.  
  
- **Rot genomsnitts fel (rmse)** skapar ett enskilt värde som sammanfattar felet i modellen. Genom att squaring skillnaden ignorerar måttet skillnaden mellan överförutsägelse och under förutsägelse.  
  
- **Relativ absolut fel (RAE)** är den relativa absoluta skillnaden mellan förväntade och faktiska värden. relativ eftersom genomsnitts skillnaden divideras med det aritmetiska medelvärdet.  
  
- **Ett relativt kvadratvärde (RSE)** normaliserar på samma sätt det totala antalet fel i det förväntade värdet genom att dividera med det totala antalet fel i det faktiska värdet.  
  

  
- **Koefficienten för bestämning**, som ofta kallas R<sup>2</sup>, representerar modellens förutsägande effekt som ett värde mellan 0 och 1. Noll betyder att modellen är slumpmässig (förklarar ingenting). 1 betyder en perfekt anpassning. Vi bör dock använda försiktighet i tolkningen av R<sup>2</sup> -värden, eftersom låga värden kan vara helt normala och höga värden kan vara misstänkta.

###  <a name="metrics-for-clustering-models"></a>Mått för kluster modeller

Eftersom kluster modeller skiljer sig avsevärt från klassificerings-och Regressions modeller i många avseenden, returnerar [utvärdera modellen](evaluate-model.md) även en annan uppsättning statistik för kluster modeller.  
  
 Statistiken som returneras för en kluster modell beskriver hur många data punkter som har tilldelats varje kluster, mängden separering mellan kluster och hur nära data punkterna är sammanslagna i varje kluster.  
  
 Statistiken för kluster modellen beräknas över hela data uppsättningen, med ytterligare rader som innehåller statistik per kluster.  
  
Följande mått rapporteras för utvärdering av kluster modeller.
    
-   Poängen i kolumnen, det **genomsnittliga avståndet till andra centret**, visar hur nära, i genomsnitt, varje punkt i klustret är till centroids för alla andra kluster.   

-   Poängen i kolumnen, **Genomsnittligt avstånd till kluster Center**, motsvarar stängningen av alla punkter i ett kluster till centroid i klustret.  
  
-   I kolumnen **antal punkter** visas hur många data punkter som har tilldelats varje kluster, tillsammans med det totala antalet data punkter i alla kluster.  
  
     Om antalet data punkter som tilldelas till kluster är mindre än det totala antalet tillgängliga data punkter, innebär det att data punkterna inte kunde tilldelas till ett kluster.  
  
-   Poängen i kolumnen, **Maximalt avstånd till kluster Center**, motsvarar summan av avstånden mellan varje punkt och centroid för den punktens kluster.  
  
     Om det här värdet är högt, kan det betyda att klustret är allmänt utspridd. Du bör granska statistiken tillsammans med det **genomsnittliga avståndet till kluster Center** för att fastställa klustrets uppslag.   

-   Den **kombinerade utvärderings** poängen längst ned i varje del av resultaten visar genomsnitts poängen för de kluster som skapats i just den modellen.  
  

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 