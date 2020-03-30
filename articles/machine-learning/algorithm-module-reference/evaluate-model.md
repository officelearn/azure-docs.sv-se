---
title: 'Utvärdera modell: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Utvärdera modell i Azure Machine Learning för att mäta noggrannheten hos en tränad modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: c1bcbb6a368c9c80f968c48c1a6e0bc6c95133d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456412"
---
# <a name="evaluate-model-module"></a>Utvärdera modellmodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att mäta noggrannheten hos en utbildad modell. Du anger en datauppsättning som innehåller poäng som genereras från en modell och modulen **Utvärdera modell** beräknar en uppsättning utvärderingsmått av branschstandard.
  
 Måtten som returneras av **Utvärdera modell** beror på vilken typ av modell du utvärderar:  
  
-   **Klassificeringsmodeller**    
-   **Regressionsmodeller**  
-   **Klustermodeller**  


> [!TIP]
> Om du är ny på modellutvärdering rekommenderar vi videoserien av Dr Stephen Elston, som en del av [maskininlärningskursen](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) från EdX. 


Det finns tre sätt att använda modulen **Utvärdera modell:**

+ Generera poäng över dina träningsdata och utvärdera modellen baserat på dessa poäng
+ Generera poäng på modellen, men jämför dessa poäng med poäng på en reserverad testuppsättning
+ Jämför poäng för två olika men relaterade modeller med samma uppsättning data

## <a name="use-the-training-data"></a>Använd träningsdata

Om du vill utvärdera en modell måste du ansluta en datauppsättning som innehåller en uppsättning indatakolumner och poäng.  Om det inte finns några andra data tillgängliga kan du använda den ursprungliga datauppsättningen.

1. Anslut **poängdatauppsättningsutdata** för [poängmodellen](./score-model.md) till indata från **Utvärdera modell**. 
2. Klicka på **Utvärdera modellmodul** och kör pipelinen för att generera utvärderingspoängen.

## <a name="use-testing-data"></a>Använda testdata

Ett vanligt scenario i maskininlärning är att separera den ursprungliga datauppsättningen i utbildnings- och testdatauppsättningar med hjälp av [modulen Dela](./split-data.md) eller [modulen Partition och Exempel.](./partition-and-sample.md) 

1. Anslut **poängdatauppsättningsutdata** för [poängmodellen](score-model.md) till indata från **Utvärdera modell**. 
2. Anslut utdata från modulen Dela data som innehåller testdata till den högra inmatningen av **Utvärdera modell**.
2. Klicka på **Utvärdera modellmodul** och välj **Kör markerad** för att generera utvärderingspoängen.

## <a name="compare-scores-from-two-models"></a>Jämför poäng från två modeller

Du kan också ansluta en andra uppsättning poäng för att **utvärdera modell**.  Poängen kan vara en delad utvärderingsuppsättning som har kända resultat, eller en uppsättning resultat från en annan modell för samma data.

Den här funktionen är användbar eftersom du enkelt kan jämföra resultat från två olika modeller på samma data. Du kan också jämföra poäng från två olika körningar över samma data med olika parametrar.

1. Anslut **poängdatauppsättningsutdata** för [poängmodellen](score-model.md) till indata från **Utvärdera modell**. 
2. Anslut utdata från poängmodellmodulen för den andra modellen till den högra ingången **av Utvärdera modell**.
3. Skicka pipelinen.

## <a name="results"></a>Resultat

När du har kört **Utvärdera modell**högerklickar du på modulen och väljer **Visualisera utvärderingsresultat** för att se resultaten.

Om du ansluter datauppsättningar till båda ingångarna **av Utvärdera modell**innehåller resultaten mått för både uppsättning data eller båda modellerna.
Modellen eller data som är kopplade till den vänstra porten visas först i rapporten, följt av måtten för datauppsättningen eller modellen som är kopplad på den högra porten.  

Följande bild representerar till exempel en jämförelse av resultat från två klustermodeller som har byggts på samma data, men med olika parametrar.  

![Jämföra2Models](media/module/evaluate-2-models.png)  

Eftersom det här är en klustermodell skiljer sig utvärderingsresultaten från om du jämförde poäng från två regressionsmodeller eller jämförde två klassificeringsmodeller. Den övergripande presentationen är dock densamma. 

## <a name="metrics"></a>Mått

I det här avsnittet beskrivs de mått som returneras för de specifika typer av modeller som stöds för användning med **Utvärdera modell:**

+ [klassificeringsmodeller](#metrics-for-classification-models)
+ [regressionsmodeller](#metrics-for-regression-models)
+ [klustermodeller](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Mått för klassificeringsmodeller

Följande mått rapporteras vid utvärdering av klassificeringsmodeller.
  
-   **Noggrannhet** mäter godheten i en klassificeringsmodell som andelen verkliga resultat till totala fall.  
  
-   **Precision** är andelen verkliga resultat över alla positiva resultat.  
  
-   **Recall** är den del av alla korrekta resultat som returneras av modellen.  
  
-   **F-poäng** beräknas som det viktade medelvärdet av precision och återkallande mellan 0 och 1, där det ideala F-poängsvärdet är 1.  
  
-   **AUC** mäter området under kurvan ritad med sanna positiva på y-axeln och falska positiva på x-axeln. Det här måttet är användbart eftersom det ger ett enda nummer som låter dig jämföra modeller av olika typer.  
  
- **Genomsnittlig loggförlust** är en enda poäng som används för att uttrycka straffet för felaktiga resultat. Den beräknas som skillnaden mellan två sannolikhetsfördelningar – den sanna och den i modellen.  
  
- **Träningsloggförlust** är en enda poäng som representerar klasarens fördel över en slumpmässig förutsägelse. Loggförlusten mäter osäkerheten i din modell genom att jämföra sannolikheter den utdata till kända värden (grundsanning) i etiketterna. Du vill minimera loggförlusten för modellen som helhet.

### <a name="metrics-for-regression-models"></a>Mått för regressionsmodeller
 
Måtten som returneras för regressionsmodeller är utformade för att uppskatta mängden fel.  En modell anses passa data väl om skillnaden mellan observerade och förväntade värden är liten. Men om man tittar på mönstret av resterna (skillnaden mellan någon förutspådde punkt och dess motsvarande faktiska värde) kan berätta mycket om potentiella partiskhet i modellen.  
  
 Följande mått rapporteras för utvärdering av regressionsmodeller.
  
- **Genomsnittligt absolut fel (MAE)** mäter hur nära förutsägelserna är för de faktiska resultaten; därmed är en lägre poäng bättre.  
  
- **Root-medelvadrats (RMSE)** skapar ett enda värde som sammanfattar felet i modellen. Genom att squaring skillnaden, bortser måttet skillnaden mellan över-förutsägelse och under-förutsägelse.  
  
- **Relativt absolut fel (RAE)** är den relativa absoluta skillnaden mellan förväntade och faktiska värden. eftersom den genomsnittliga skillnaden divideras med det aritmetiska medelvärdet.  
  
- **Relativt fyrkantigt fel (RSE)** normaliserar på samma sätt det totala kvadratfelet för de förväntade värdena genom att dividera med det totala kvadratfelet för de faktiska värdena.  
  

  
- **Bestämningskoefficienten**, ofta kallad R<sup>2,</sup>representerar modellens prediktiva effekt som ett värde mellan 0 och 1. Noll betyder att modellen är slumpmässig (förklarar ingenting); 1 betyder att det finns en perfekt passform. Försiktighet bör dock iakttas vid tolkning av R<sup>2-värden,</sup> eftersom låga värden kan vara helt normala och höga värden kan misstänkas.

###  <a name="metrics-for-clustering-models"></a>Mått för klustermodeller

Eftersom klustermodeller skiljer sig avsevärt från klassificerings- och regressionsmodeller i många avseenden returnerar [utvärdera modell](evaluate-model.md) också en annan uppsättning statistik för klustermodeller.  
  
 Statistiken som returneras för en klustermodell beskriver hur många datapunkter som tilldelats varje kluster, mängden separation mellan kluster och hur tätt datapunkterna är grupperade i varje kluster.  
  
 Statistiken för klustermodellen beräknas i genomsnitt över hela datauppsättningen, med ytterligare rader som innehåller statistiken per kluster.  
  
Följande mått rapporteras för utvärdering av klustermodeller.
    
-   Poängen i kolumnen **Medelavstånd till annat center**, representerar hur nära, i genomsnitt, varje punkt i klustret är till centroider för alla andra kluster.   

-   Poängen i **kolumnen, Genomsnittligt avstånd till klustercenter**, representerar närheten mellan alla punkter i ett kluster till centroiden för det klustret.  
  
-   Kolumnen **Antal poäng** visar hur många datapunkter som tilldelats varje kluster, tillsammans med det totala totala antalet datapunkter i ett kluster.  
  
     Om antalet datapunkter som tilldelats kluster är mindre än det totala antalet tillgängliga datapunkter innebär det att datapunkterna inte kunde tilldelas ett kluster.  
  
-   Poängen i **kolumnen, Maximalt avstånd till Klustercenter**, representerar summan av avstånden mellan varje punkt och centroiden för den punktens kluster.  
  
     Om detta antal är högt kan det innebära att klustret är brett spritt. Du bör granska den här statistiken tillsammans med **medeldistans-till-klustercenter** för att fastställa klustrets spridning.   

-   I den **kombinerade utvärderingspoängen** längst ned i varje resultatavsnitt visas de genomsnittliga poängen för de kluster som skapats i just den modellen.  
  

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 