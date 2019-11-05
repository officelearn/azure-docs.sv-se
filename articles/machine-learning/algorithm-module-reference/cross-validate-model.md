---
title: 'Kors validerings modell: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen för kors validering i Azure Machine Learning tjänst för att korsa valideringen av parameter uppskattningar för klassificerings-eller Regressions modeller genom att partitionera data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5eea61ee8284010531e80e17bf1110ab470d04c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512852"
---
# <a name="cross-validate-model"></a>Kors validerings modell

Den här artikeln beskriver hur du använder modulen för att **validera modeller** i Azure Machine Learning designer (för hands version). *Kors validering* är en viktig teknik som ofta används i Machine Learning för att utvärdera både variabiliteten av en data uppsättning och tillförlitligheten för en modell som har tränats med dessa data.  

Modulen för att **validera modell** tar sig in som indata för en etikettad data uppsättning, tillsammans med en klass som inte är tränad eller Regressions modell. Den delar in data uppsättningen i ett antal del mängder (*vikning*), skapar en modell på varje vikning och returnerar sedan en uppsättning noggrannhets statistik för varje vikning. Genom att jämföra noggrannhets statistiken för alla vik objekt kan du tolka data uppsättningens kvalitet och förstå huruvida modellen är mottaglig för variationer i data.  

Kors validering returnerar också förväntade resultat och sannolikheter för data uppsättningen, så att du kan utvärdera förutsägelsens tillförlitlighet.  

### <a name="how-cross-validation-works"></a>Så här fungerar kors validering

1. Kors validering delar slumpmässigt upp utbildnings data i ett antal partitioner, även kallade *vikning*. 

    + Algoritmen är standardvärdet 10 vikning om du inte tidigare har partitionerat data uppsättningen. 
    + Om du vill dela upp data uppsättningen i ett annat antal vikningar kan du använda [partition och exempel](partition-and-sample.md) -modulen och ange hur många vikningar som ska användas.  

2.  Modulen sätter undan data i vikt 1 som ska användas för verifiering (Detta kallas ibland för Hold- *vikning*) och använder de återstående viken för att träna en modell. 

    Om du t. ex. skapar fem veck, genererar modulen fem modeller under kors validering, varje modell som har tränats med 4/5 av data och testats på återstående 1/5.  

3.  Vid test av modellen för varje vikning utvärderas flera noggrannhets statistik. Vilken statistik som används beror på vilken typ av modell du utvärderar. Olika statistik används för att utvärdera klassificerings modeller jämfört med Regressions modeller.  

4.  När utvecklings-och utvärderings processen är klar för alla veck genererar **kors validerings modellen** en uppsättning prestanda mått och resultat resultat för alla data. Du bör granska dessa mått för att se om en enskild vikning har särskilt hög eller låg precision 

### <a name="advantages-of-cross-validation"></a>Fördelar med kors validering

Ett annat och vanligt sätt att utvärdera en modell är att dela upp data i en utbildning och test uppsättning med hjälp av [dela data](split-data.md), och sedan validera modellen på tränings data. Kors valideringen erbjuder dock vissa fördelar:  

-   Kors valideringen använder fler test data.

     Kors validering mäter modellens prestanda med de angivna parametrarna i ett större data utrymme. Det vill säga kors valideringen använder hela inlärnings data uppsättningen för både utbildning och utvärdering, i stället för en del. Om du däremot validerar en modell genom att använda data som genererats från en slumpmässig delning, utvärderar du normalt bara modellen på 30% eller mindre av tillgängliga data.  

     Men eftersom kors validerings tåg och validerar modellen flera gånger över en större data uppsättning, är det mycket mer beräknings intensivt och tar mycket längre tid än att verifiera vid en slumpmässig delning.  

-   Kors valideringen utvärderar data uppsättningen och modellen.

     Kors valideringen mäter inte bara precisionen för en modell, men ger dig också en viss uppfattning om hur representativ data uppsättningen är och hur känslig modellen kan vara till skillnad i data.  

## <a name="how-to-use-cross-validate-model"></a>Använda kors validerings modell

Kors validering kan ta lång tid att köra om data uppsättningen är stor.  Därför kan du använda **kors validerings modell** i den inledande fasen av att skapa och testa din modell, för att utvärdera modell parametrarnas lämplighet (förutsatt att beräknings tiden är giltig) och sedan träna och utvärdera din modell med hjälp av etablerade parametrar med [träna modell](train-model.md) och [utvärdera modell](evaluate-model.md) moduler.

I det här scenariot tränar du och testar modellen med hjälp av **kors validerings modellen**.

1. Lägg till modulen för att **validera modell** i din pipeline. Du hittar den i Azure Machine Learning designer i **utvärderings kategorin modell bedömnings &** . 

2. Anslut utdata från en **klassificerings** -eller **Regressions** modell. 

    Om du till exempel använder en **två Bayes punkt-dator** för klassificering, konfigurerar du modellen med de parametrar som du vill använda och drar sedan en koppling från klassificerarens **modell** port till den matchande porten för **kors validering Modell**. 

    > [!TIP] 
    > Modellen behöver inte tränas eftersom en **kors validerings modell** automatiskt tränar modellen som en del av utvärderingen.  
3.  På **data uppsättnings** porten för **kors validerings modellen**ansluter du till en etikettad utbildnings data uppsättning.  

4.  I fönstret **Egenskaper** för **kors validerings modell**klickar du på **Starta kolumn väljaren** och väljer den enda kolumn som innehåller klass etiketten eller det förutsägbara värdet. 

5. Ange ett värde för den **slumpmässiga Dirigerings** parametern om du vill kunna upprepa resultaten av kors valideringen mellan efterföljande körningar på samma data.  

6.  Köra en pipeline.

7. Se avsnittet [Results](#results) för en beskrivning av rapporterna.

    För att få en kopia av modellen för åter användning senare, högerklickar du på utdata från modulen som innehåller algoritmen (till exempel den **två klassen Bayes Point Machine**) och klickar på **Spara som utbildad modell**.

## <a name="results"></a>Resultat

När alla iterationer har slutförts skapar **kors validerings modellen** Poäng för hela data uppsättningen, samt prestanda mått som du kan använda för att utvärdera modellens kvalitet.

### <a name="scored-results"></a>Resultat

Den första utdata i modulen tillhandahåller källdata för varje rad, tillsammans med vissa förväntade värden och relaterade sannolikheter. 

Om du vill visa de här resultaten högerklickar du på modulen för att **validera modell** i pipeline, väljer **resultat**och klickar på **visualisera**.

| Nytt kolumn namn      | Beskrivning                              |
| -------------------- | ---------------------------------------- |
| Poäng etiketter        | Den här kolumnen läggs till i slutet av data uppsättningen och innehåller det förväntade värdet för varje rad |
| Resultat sannolikhet | Den här kolumnen läggs till i slutet av data uppsättningen och anger den uppskattade sannolikheten för värdet i **Poäng etiketter**. |
| Vikt nummer          | Anger det 0-baserade indexet för vikningen varje datarad som tilldelats vid kors validering. |

 ### <a name="evaluation-results"></a>Utvärderingsresultat

Den andra rapporten är grupperad efter vikning. Kom ihåg att när en **kors validerings modell** under körningen delar upp utbildnings data slumpmässigt i *n* -vikning (som standard 10). I varje iteration över data uppsättningen använder **kors validerings modellen** en vikning som en verifierings data uppsättning och använder de återstående *n-1-* vikningen för att träna en modell. Var och en av de *n* modellerna testas mot data i alla andra vik.

I den här rapporten visas vikningarna efter index värde i stigande ordning.  Om du vill sortera efter andra kolumner kan du spara resultaten som en data uppsättning.

Om du vill visa dessa resultat går du till pipelinen, högerklickar på modulen för att **validera modell** , väljer **utvärderings resultat efter vikning**och klickar på **visualisera**.


|Kolumn namn| Beskrivning|
|----|----|
|Vikt nummer| En identifierare för varje vikning. Om du har skapat fem vikningar skulle det finnas 5 del mängder av data, numrerade 0 till 4.
|Antal exempel i vikning|Antalet rader tilldelade till varje vikning. De bör vara ungefär lika. |


Dessutom ingår följande mått för varje vikning, beroende på vilken typ av modell som du utvärderar. 

+ **Klassificerings modeller**: precision, återkallande, F-score, AUC, precision  

+ **Regressions modeller**: Genomsnittligt absolut fel, rot genomsnitts fel, relativt absolut fel, relativt kvadratvärde och koefficient för bestämning


## <a name="technical-notes"></a>Tekniska anteckningar  

+ Det är en bra idé att normalisera data uppsättningar innan du använder dem för kors validering. 

+ Eftersom **kors validering av modell** tågen och validerar modellen flera gånger, är det mycket mer beräknings intensivt och det tar längre tid att slutföra än om du validerade modellen med hjälp av en slumpvis delad data uppsättning. 

+ Du behöver inte dela upp data uppsättningen i inlärnings-och test uppsättningar när du använder kors validering för att mäta modellens noggrannhet. 


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 

