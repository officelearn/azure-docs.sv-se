---
title: 'Utvärdera modellen: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig mer om att använda modulen utvärdera modell i Azure Machine Learning-tjänsten för att mäta det arbete du utfört en tränad modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 40a8247c22da1f7a057e222565ffb2ec4c6b7fb3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028747"
---
# <a name="evaluate-model-module"></a>Utvärdera modell

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Du kan använda den här modulen för att mäta det arbete du utfört en tränad modell. Du anger en datauppsättning som innehåller resultat som genereras från en modell och **utvärdera modell** modulen beräknar en uppsättning utvärderingsmått branschstandard.
  
 De mätvärden som returneras av **utvärdera modell** beror på vilken typ av modell som du utvärderar:  
  
-   **Klassificering modeller**    
-   **Regressionsmodeller**    



> [!TIP]
> Om du är nybörjare på modellen utvärderingen rekommenderar vi videoserien av Dr. Stephen Elston, som en del av den [datorn utbildningen](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) från EdX. 


Det finns tre sätt att använda den **utvärdera modell** modulen:

+ Generera poäng över dina utbildningsdata och utvärdera modellen baserat på dessa poäng
+ Generera poäng på modellen, men jämföra dessa resultat att poäng på en reserverad testmängden
+ Jämför resultat för två relaterade modeller med samma uppsättning data

## <a name="use-the-training-data"></a>Använd träningsdata

Om du vill utvärdera en modell, måste du ansluta en datauppsättning som innehåller en uppsättning indatakolumner och poäng.  Om inga andra data är tillgängliga, kan du använda din ursprungliga datauppsättning.

1. Ansluta den **poängsätts datauppsättning** utdata av de [Poängmodell](./score-model.md) till indata för **utvärdera modell**. 
2. Klicka på **utvärdera modell** modulen och kör experimentet att generera utvärdering poängen.

## <a name="use-testing-data"></a>Använda testdata

Ett vanligt scenario i machine learning är att dela din ursprungliga datauppsättningen i träning och testning datauppsättningar som använder den [dela](./split-data.md) modulen eller [partitionera och ta prover](./partition-and-sample.md) modulen. 

1. Ansluta den **poängsätts datauppsättning** utdata av de [Poängmodell](score-model.md) till indata för **utvärdera modell**. 
2. Anslut utdataporten för modulen dela Data som innehåller testdata till den högra indataporten för **utvärdera modell**.
2. Klicka på **utvärdera modell** modul och välj **kör valda** att generera utvärdering poängen.

## <a name="compare-scores-from-two-models"></a>Jämför resultatet från två modeller

Du kan också ansluta till en annan uppsättning resultat till **utvärdera modell**.  Poängen kan vara en delad utvärdering uppsättning som har visat resultaten eller en uppsättning resultat från en annan modell för samma data.

Den här funktionen är bra eftersom du enkelt kan jämföra resultaten från två olika modeller på samma data. Eller så kan du jämföra resultat från två olika körningar av samma data med olika parametrar.

1. Ansluta den **poängsätts datauppsättning** utdata av de [Poängmodell](score-model.md) till indata för **utvärdera modell**. 
2. Anslut utdataporten för modulen poängsätta modell för den andra modellen till den högra indataporten för **utvärdera modell**.
3. Högerklicka på **utvärdera modell**, och välj **kör valda** att generera utvärdering poängen.

## <a name="results"></a>Resultat

När du har kört **utvärdera modell**, högerklickar du på modulen och välj **utvärderingsresultat** att se resultaten. Du kan:

+ Spara resultatet som en datauppsättning för enklare analys med andra verktyg
+ Generera en visualisering i gränssnittet

Om du ansluter datauppsättningar till både indata för **utvärdera modell**, resultatet innehåller mått för båda uppsättning data eller båda modellerna.
Modellen eller data som bifogas till den vänstra porten visas först i rapporten, följt av mått för datauppsättningen eller modellen på rätt port.  

Följande bild representerar till exempel en jämförelse av resultaten från två klustring modeller som skapats på samma data, men med olika parametrar.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Eftersom det här är en klustringsmodell skiljer utvärderingsresultaten sig från om jämfört med resultat från två regressionsmodeller, eller jämföra två modeller för klassificering. Den övergripande presentationen är dock desamma. 

## <a name="metrics"></a>Mått

Det här avsnittet beskrivs de mätvärden som returneras för specifika typer av modeller som stöds för användning med **utvärdera modell**:

+ [klassificering modeller](#bkmk_classification)
+ [regressionsmodeller](#bkmk_regression)

###  <a name="bkmk_classification"></a> Mått för klassificering modeller

Följande mått rapporteras vid utvärdering av modeller för klassificering. Om du jämför modeller är de rankade efter det mått som du väljer för utvärdering.  
  
-   **Precision** mäter godis av en klassificeringsmodellen som SANT resultaten till totalt antal fall.  
  
-   **Precision** är andelen SANT resultat över alla positiva resultat.  
  
-   **Kom ihåg** är andelen av alla korrekta resultat som returneras av modellen.  
  
-   **F-score** beräknas som ett viktat medelvärde av precision och återkallande mellan 0 och 1, där det perfekta F-score-värdet är 1.  
  
-   **AUC** mått området under kurvan ritas med true positiva identifieringar på y-axeln och FALSKT positiva på x-axeln. Det här måttet är användbart eftersom det ger ett enda tal som gör att du jämför modeller av olika typer.  
  
- **Genomsnittlig logg förlust** är ett enda värde som används för att uttrycka särskilda avgifter för felaktiga resultat. Det beräknas som skillnaden mellan två sannolikheten distributioner – det SANT och ett i modellen.  
  
- **Utbildning log förlust** är en enda riskpoäng som representerar utnyttja klassificeraren över en slumpmässig förutsägelse. Log förlusten mäter osäkerheten på om din modell genom att jämföra troliga den matar ut till kända värden (grunden sanningen) i etiketter. Du vill minimera logg för modellen som helhet.

##  <a name="bkmk_regression"></a> Mått för regressionsmodeller
 
De mätvärden som returneras för regressionsmodeller Allmänt utformade för att beräkna hur mycket av fel.  En modell anses data får plats bra om skillnaden mellan observerade och förväntade värden är liten. Dock ser tittar på mönstret för restbelopp (skillnaden mellan helst för en förväntad och dess motsvarande faktiskt värde) du mycket om potentiella bias i modellen.  
  
 Följande mått rapporteras för att utvärdera regressionsmodeller. När du jämför modeller är de rankade efter det mått som du väljer för utvärdering.  
  
- **Medelabsolutfel (MAE)** mäter hur nära förutsägelserna kommer de faktiska resultaten; därför en lägre poäng är bättre.  
  
- **Rot innebära kvadratfel (RMSE)** skapar ett enda värde som sammanfattar felet i modellen. Av squaring skillnaden ignorerar måttet skillnaden mellan över förutsägelse och under förutsägelse.  
  
- **Relativa absoluta fel (RAE)** är den relativa absoluta skillnaden mellan förväntade och faktiska värden; relativa eftersom medeldifferens divideras med det aritmetiska medelvärdet.  
  
- **Relativ cirkels fel (RSE)** på samma sätt normaliserar totala kvadratfel av de förväntade värdena genom att dividera med den totala kvadratfel över faktiska värden.  
  
- **Innebära noll ett fel (MZOE)** anger om förutsägelsen har rätt eller inte.  Med andra ord: `ZeroOneLoss(x,y) = 1` när `x!=y`, annars `0`.
  
- **Bestämningskoefficient**, vilket ofta kallas R<sup>2</sup>, representerar förutsägande modellen som ett värde mellan 0 och 1. Noll innebär att modellen är slumpmässig (förklarar ingenting); 1 innebär att det finns en perfekt passning. Dock försiktig vid tolkningen R<sup>2</sup> värden, som låga värden kan vara helt normal och hög värdena kan vara tveksamma.
  

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 