---
title: 'Korsvalidorisk modell: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Korsvalifiera modell i Azure Machine Learning för att korsvalificera parameteruppskattningar för klassificerings- eller regressionsmodeller genom att partitionera data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 7550bb7c6bbf7602245f9a9f1ac006ce693b36a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477654"
---
# <a name="cross-validate-model"></a>Korsvalidera modell

I den här artikeln beskrivs hur du använder modulen Korsvalidorisk modell i Azure Machine Learning designer (förhandsversion). *Korsvalidering* är en teknik som ofta används vid maskininlärning för att bedöma både variationen av en datauppsättning och tillförlitligheten hos alla modeller som tränas genom dessa data.  

Modulen Korsvalidoring tar som indata en märkt datauppsättning, tillsammans med en otränad klassificering eller regressionsmodell. Den delar upp datauppsättningen i ett visst antal delmängder (*veck*), bygger en modell på varje vik och returnerar sedan en uppsättning noggrannhetsstatistik för varje vikning. Genom att jämföra noggrannhetsstatistiken för alla veck kan du tolka datauppsättningens kvalitet. Du kan sedan förstå om modellen är känslig för variationer i data.  

Cross Validate Model returnerar också förväntade resultat och sannolikheter för datauppsättningen, så att du kan bedöma tillförlitligheten i förutsägelserna.  

### <a name="how-cross-validation-works"></a>Så här fungerar korsvalidering

1. Korsvalidering delar slumpmässigt upp träningsdata i veck. 

   Algoritmen är som standard 10 veck om du inte tidigare har partitionerat datauppsättningen. Om du vill dela upp datauppsättningen i ett annat antal veck kan du använda [partitions- och exempelmodulen](partition-and-sample.md) och ange hur många veck som ska användas.  

2.  Modulen avsätter data i vikt 1 som ska användas för validering. (Detta kallas ibland *holdout-luckan*.) Modulen använder de återstående vecken för att träna en modell. 

    Om du till exempel skapar fem veck genererar modulen fem modeller under korsvalidering. Modulen tränar varje modell med hjälp av fyra femtedelar av data. Den testar varje modell på den återstående en femtedel.  

3.  Vid testning av modellen för varje vikning utvärderar modulen statistik med flera noggrannhetar. Vilken statistik som modulen använder beror på vilken typ av modell du utvärderar. Olika statistik används för att utvärdera klassificeringsmodeller jämfört med regressionsmodeller.  

4.  När bygg- och utvärderingsprocessen är klar för alla veck genererar Cross Validate Model en uppsättning prestandamått och poängsatt resultat för alla data. Granska dessa mått för att se om en enda vikning har hög eller låg noggrannhet. 

### <a name="advantages-of-cross-validation"></a>Fördelar med korsvalidering

Ett annat och vanligt sätt att utvärdera en modell är att dela upp data i en tränings- och testuppsättning med hjälp av [Split Data](split-data.md)och sedan validera modellen på träningsdata. Men korsvalidering erbjuder vissa fördelar:  

-   Korsvalidering använder fler testdata.

    Korsvalidering mäter modellens prestanda med de angivna parametrarna i ett större datautrymme. Det vill än, korsvalidering använder hela träningsdatauppsättningen för både utbildning och utvärdering, i stället för en del. Om du däremot validerar en modell med hjälp av data som genereras från en slumpmässig delning utvärderar du vanligtvis modellen på endast 30 procent eller mindre av tillgängliga data.  

    Men eftersom korsvalideringståg och validerar modellen flera gånger över en större datauppsättning är den mycket mer beräkningsmässigt intensiv. Det tar mycket längre tid än att validera på en slumpmässig delning.  

-   Korsvalidering utvärderar både datauppsättningen och modellen.

    Korsvalidering mäter inte bara en modells noggrannhet. Det ger dig också en uppfattning om hur representativ datauppsättningen är och hur känslig modellen kan vara för variationer i data.  

## <a name="how-to-use-cross-validate-model"></a>Så här använder du modell för korsvalning

Korsvalidering kan ta lång tid att köra om datauppsättningen är stor.  Så du kan använda Cross Validate Model i den inledande fasen av att bygga och testa din modell. I den fasen kan du utvärdera godheten av modellparametrarna (förutsatt att beräkningstiden är acceptabel). Du kan sedan träna och utvärdera din modell med hjälp av de etablerade parametrarna med modulerna [Tågmodell](train-model.md) och [Utvärdera modell.](evaluate-model.md)

I det här fallet kan du både träna och testa modellen med hjälp av Kors validera modell.

1. Lägg till modulen Korsvala modell i pipelinen. Du hittar den i Azure Machine Learning designer, i kategorin **Modellbedömning & utvärdering.** 

2. Anslut utdata för någon klassificerings- eller regressionsmodell. 

    Om du till exempel använder **Två klassförstärkt beslutsträd** för klassificering konfigurerar du modellen med de parametrar du vill använda. Dra sedan en koppling från **klassificerarens otränade modellport** till den matchande porten för korsvalningsmodell. 

    > [!TIP] 
    > Du behöver inte träna modellen, eftersom Cross-Validate Model automatiskt tränar modellen som en del av utvärderingen.  
3.  Anslut alla märkta träningsdatauppsättning på **datauppsättningsporten** för Korsvalningsmodellen.  

4.  Klicka på **Redigera kolumn**på den högra panelen för korsvalningsmodell . Markera den enda kolumn som innehåller klassetiketten eller det förutsägbara värdet. 

5. Ange ett värde för parametern **Random seed** om du vill upprepa resultatet av korsvalidering över på varandra följande körningar på samma data.  

6. Skicka pipelinen.

7. Se avsnittet [Resultat](#results) för en beskrivning av rapporterna.

## <a name="results"></a>Resultat

När alla iterationer är klara skapar Korsval validera modellen poäng för hela datauppsättningen. Det skapar också prestandamått som du kan använda för att bedöma modellens kvalitet.

### <a name="scored-results"></a>Resultat som gjorts

Modulens första utdata tillhandahåller källdata för varje rad, tillsammans med några förväntade värden och relaterade sannolikheter. 

Om du vill visa resultaten högerklickar du på modulen Korsvala modell på pipelinen. Välj **Visualisera resultat med poäng**.

| Nytt kolumnnamn      | Beskrivning                              |
| -------------------- | ---------------------------------------- |
| Poängsatta etiketter        | Den här kolumnen läggs till i slutet av datauppsättningen. Den innehåller det förväntade värdet för varje rad. |
| Poängsatta sannolikheter | Den här kolumnen läggs till i slutet av datauppsättningen. Den anger den uppskattade sannolikheten för värdet i **Poängsatta etiketter**. |
| Vik nummer          | Anger det nollbaserade indexet för den vik som varje datarad tilldelades under korsvalning. |

 ### <a name="evaluation-results"></a>Utvärderingsresultat

Den andra rapporten är grupperad efter veck. Kom ihåg att cross validate-modellen delas upp *n* slumpmässigt i träningsdata i n-veck (som standard 10). I varje iteration över datauppsättningen använder Cross Validate Model en vik som en valideringsdatauppsättning. Den använder de återstående *n-1* veck för att träna en modell. Var och en av *n-modellerna* testas mot data i alla andra veck.

I den här rapporten visas vecken efter indexvärde i stigande ordning.  Om du vill beställa i en annan kolumn kan du spara resultaten som en datauppsättning.

Om du vill visa resultaten högerklickar du på modulen Korsvala modell på pipelinen. Välj **Visualisera utvärderingsresultat efter vikning**.


|Kolumnnamn| Beskrivning|
|----|----|
|Vik nummer| En identifierare för varje vikning. Om du skapade fem gånger, skulle det finnas fem delmängder av data, numrerade 0 till 4.
|Antal exempel i vikt|Antalet rader som tilldelats varje vik. De bör vara ungefär lika. |


Modulen innehåller även följande mått för varje vikning, beroende på vilken typ av modell du utvärderar: 

+ **Klassificeringsmodeller**: Precision, återkallande, F-poäng, AUC, noggrannhet  

+ **Regressionsmodeller**: Genomsnittligt absolut fel, rotmedelvärdets kvadratfel, relativt absolut fel, relativt kvadratfel och bestämningskoefficient


## <a name="technical-notes"></a>Tekniska anmärkningar  

+ Det är en bra idé att normalisera datauppsättningar innan du använder dem för korsvalidering. 

+ Cross Validate Model är mycket mer beräkningsintensiv och tar längre tid att slutföra än om du validerade modellen med hjälp av en slumpmässigt uppdelad datauppsättning. Anledningen är att Cross Validate Model tränar och validerar modellen flera gånger.

+ Du behöver inte dela upp datauppsättningen i utbildnings- och testuppsättningar när du använder korsvalidering för att mäta modellens noggrannhet. 


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

