---
title: 'Utvärdera modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen utvärdera modell i Azure Machine Learning för att mäta noggrannheten i en utbildad modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: b6a019fbea2ebfed67db4f7c2a9b0f8085438aa8
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311656"
---
# <a name="evaluate-model-module"></a>Utvärdera modell modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att mäta noggrannheten för en utbildad modell. Du anger en data uppsättning som innehåller resultat som genererats från en modell, och modulen **utvärdera modell** beräknar en uppsättning utvärderings mått som är bransch standard.
  
 Måtten som returneras av **evaluate-modellen** beror på vilken typ av modell du utvärderar:  
  
-   **Klassificerings modeller**    
-   **Regressions modeller**    


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

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Eftersom det här är en kluster modell är utvärderings resultatet annorlunda än om du jämför resultat från två Regressions modeller eller jämför två klassificerings modeller. Den övergripande presentationen är dock densamma. 

## <a name="metrics"></a>Mått

I det här avsnittet beskrivs de mått som returneras för de olika typerna av modeller som stöds för användning med **utvärdera modell**:

+ [klassificerings modeller](#bkmk_classification)
+ [Regressions modeller](#bkmk_regression)

###  <a name="bkmk_classification"></a>Mått för klassificerings modeller

Följande mått rapporteras när klassificerings modeller utvärderas. Om du jämför modeller rangordnas de efter det mått som du väljer för utvärdering.  
  
-   **Noggrannhet** mäter en klassificerings modells lämplighet som andel sanna resultat av totalt antal fall.  
  
-   **Precision** är förhållandet mellan faktiska resultat och alla positiva resultat.  
  
-   **Återkallande** är bråk delen av alla korrekta resultat som returneras av modellen.  
  
-   **F-score** beräknas som viktat medelvärde för precision och åter kallelse mellan 0 och 1, där det idealiska värdet F-Poäng är 1.  
  
-   **AUC** mäter ytan under kurvan som ritas med sant positiva resultat på y-axeln och falska positiva identifieringar på x-axeln. Det här måttet är användbart eftersom det innehåller ett enda nummer som gör det möjligt att jämföra modeller av olika typer.  
  
- **Genomsnittlig logg förlust** är ett enda poäng som används för att uttrycka sanktionen för fel resultat. Det beräknas som skillnaden mellan två sannolikhets fördelningar – det sanna, och den som finns i modellen.  
  
- **Inlärnings logg förlusten** är en enda poäng som representerar fördelen med klassificeraren över en slumpmässig förutsägelse. Logg förlusten mäter osäkerheten för din modell genom att jämföra sannolikheten att den matas till kända värden (mark sanningen) i etiketterna. Du vill minimera logg förlusten för modellen som helhet.

##  <a name="bkmk_regression"></a>Mät värden för Regressions modeller
 
Måtten som returneras för Regressions modeller är utformade för att uppskatta mängden fel.  En modell anses vara bra anpassad om skillnaden mellan observerade och förväntade värden är liten. Men om du tittar på resten av resten (skillnaden mellan en förutsägande punkt och dess motsvarande faktiska värde) kan du få mycket information om potentiell förskjutning i modellen.  
  
 Följande mått rapporteras för utvärdering av Regressions modeller. När du jämför modeller rangordnas de efter det mått som du väljer för utvärdering.  
  
- **Medelvärde för absolut fel (Mae)** hur nära förutsägelserna är till de faktiska resultatet; Därför är en lägre poäng bättre.  
  
- **Rot genomsnitts fel (rmse)** skapar ett enskilt värde som sammanfattar felet i modellen. Genom att squaring skillnaden ignorerar måttet skillnaden mellan överförutsägelse och under förutsägelse.  
  
- **Relativ absolut fel (RAE)** är den relativa absoluta skillnaden mellan förväntade och faktiska värden. relativ eftersom genomsnitts skillnaden divideras med det aritmetiska medelvärdet.  
  
- **Ett relativt kvadratvärde (RSE)** normaliserar på samma sätt det totala antalet fel i det förväntade värdet genom att dividera med det totala antalet fel i det faktiska värdet.  
  
- **Genomsnitt noll ett fel (MZOE)** anger om förutsägelsen var korrekt eller inte.  Med andra ord: `ZeroOneLoss(x,y) = 1` när `x!=y`; annars `0`.
  
- **Koefficienten för bestämning**, som ofta kallas R<sup>2</sup>, representerar modellens förutsägande effekt som ett värde mellan 0 och 1. Noll betyder att modellen är slumpmässig (förklarar ingenting). 1 betyder en perfekt anpassning. Vi bör dock använda försiktighet i tolkningen av R<sup>2</sup> -värden, eftersom låga värden kan vara helt normala och höga värden kan vara misstänkta.
  

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 