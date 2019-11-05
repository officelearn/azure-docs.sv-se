---
title: 'Permutations funktions prioritet: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder funktionen permutations prioritet i Azure Machine Learning-tjänsten för att beräkna permutations funktionens prioritets resultat för funktions variabler som har fått en utbildad modell och en test data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 8a2ddb92101957a3dcadebb17dffa39113825e4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517948"
---
# <a name="permutation-feature-importance"></a>Viktighets funktion för permutation

I den här artikeln beskrivs hur du använder [funktionen permutations prioritet](permutation-feature-importance.md) i Azure Machine Learning designer (för hands version) för att beräkna en uppsättning funktions resultat för din data uppsättning. Du kan använda de här poängen för att avgöra vilka funktioner som ska användas i en modell.

I den här modulen är funktions värden slumpmässigt blandade, en kolumn i taget och modellens prestanda mäts före och efter. Du kan välja ett av de standard mått som tillhandahålls för att mäta prestanda.

Poängen som modulen returnerar representerar **ändringen** av prestandan för en utbildad modell efter permutation. Viktiga funktioner är ofta mer känsliga för blandning-processen och kommer därför att resultera i högre prioritets poäng. 

Den här artikeln innehåller en välgrundad översikt över permutations funktionens betydelse, dess teoretiska bas och dess program i Machine Learning: [permutations-funktions prioritet](http://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)  

## <a name="how-to-use-permutation-feature-importance"></a>Så här använder du permutations funktions prioritet

Om du vill generera en uppsättning funktions resultat måste du ha en redan utbildad modell, samt en test data uppsättning.  

1.  Lägg till modulen **permutations prioritet** i din pipeline. Du hittar den här modulen i kategorin **funktions val** . 

2.  Anslut en tränad modell till den vänstra Indataporten. Modellen måste vara en Regressions modell eller klassificerings modell.  

3.  På den högra indatan ansluter du en data uppsättning, helst en som skiljer sig från den data uppsättning som används för att träna modellen. Den här data uppsättningen används för resultat som baseras på den tränade modellen och för utvärdering av modellen efter att funktions värden har ändrats.  

4.  För **slumpmässigt utsäde**anger du ett värde som ska användas som Seed för slumpmässighet. Om du anger 0 (standard) genereras en siffra baserat på system klockan.

     Ett Seed-värde är valfritt, men du bör ange ett värde om du vill ha reproducerbarhet i samma pipeline.  

5.  För Mät **värden för att mäta prestanda**väljer du ett mått som ska användas när du beräknar modell kvalitet efter permutation.  

     Azure Machine Learning Designer stöder följande mått, beroende på om du utvärderar en klassificerings-eller Regressions modell:  

    -   **Klassificering**

        Precision, precision, återkallande, genomsnittlig logg förlust  

    -   **Regression**

        Precision, återkalla, medels absolut fel, rot genomsnitts fel, relativt absolut fel, relativt kvadratvärde, koefficient för bestämning  

     En mer detaljerad beskrivning av dessa utvärderings mått och hur de beräknas finns i [utvärdera modell](evaluate-model.md).  

6.  Köra en pipeline.  

7.  Modulen visar en lista över funktions kolumner och poängen som är kopplade till dem, rankade i ordning i poängen, fallande.  


##  <a name="technical-notes"></a>Tekniska anteckningar

Det här avsnittet innehåller implementerings information, tips och svar på vanliga frågor.

### <a name="how-does-this-compare-to-other-feature-selection-methods"></a>Hur jämförs detta med andra funktions urvals metoder?

Prioritet för permutations funktionen fungerar genom att slumpmässigt ändra värdena för varje funktions kolumn, en kolumn i taget, och sedan utvärdera modellen. 

Rangordningarna som tillhandahålls av permutationen är ofta annorlunda än de som du får från [filtrering baserat funktions val](filter-based-feature-selection.md), vilket beräknar poängen **innan** en modell skapas. 

Detta beror på att permutationen i permutationen inte mäter associationen mellan en funktion och ett målvärde, utan fångar i stället in hur mycket som påverkar varje funktion på förutsägelser från modellen.
  
## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 
