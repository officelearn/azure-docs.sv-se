---
title: 'Permutationsfunktionsbetydelse: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Permutationsfunktionsbetyer i Azure Machine Learning för att beräkna permutationsfunktionens betydelsepoäng för funktionsvariabler, med tanke på en tränad modell och en testdatauppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: e4511cf4393172e7d2b1ab8a985c76d8f98d4015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456071"
---
# <a name="permutation-feature-importance"></a>Permutationfunktionsprioritet

I den här artikeln beskrivs hur du använder modulen Betydelse för permutationsfunktionen i Azure Machine Learning designer (förhandsversion) för att beräkna en uppsättning prioritetspoäng för funktioner för din datauppsättning. Du använder dessa poäng för att avgöra vilka funktioner som är bäst att använda i en modell.

I den här modulen blandas funktionsvärden slumpmässigt, en kolumn i taget. Modellens prestanda mäts före och efter. Du kan välja ett av standardmåtten för att mäta prestanda.

Poängen som modulen returnerar representerar *förändringen* i prestanda för en tränad modell, efter permutation. Viktiga funktioner är oftast mer känsliga för blandningsprocessen, så de resulterar i högre prioritetspoäng. 

Den här artikeln innehåller en översikt över permutationsfunktionen, dess teoretiska grund och dess tillämpningar inom maskininlärning: [Permutationsfunktionsbetesvikt](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>Så här använder du permutationsfunktionens betydelse

Generera en uppsättning funktioner kräver att du har en redan tränad modell, samt en testdatauppsättning.  

1.  Lägg till modulen Permutationsfunktionsbetydelse i pipelinen. Du hittar den här modulen i kategorin **Funktionsval.** 

2.  Anslut en tränad modell till vänster ingång. Modellen måste vara en regressionsmodell eller en klassificeringsmodell.  

3.  Anslut en datauppsättning till höger. Välj helst en som skiljer sig från den datauppsättning som du använde för att träna modellen. Den här datauppsättningen används för bedömning baserat på den tränade modellen. Det används också för att utvärdera modellen efter att funktionsvärdena har ändrats.  

4.  För **Slumputsäde**anger du ett värde som ska användas som ett frö för randomisering. Om du anger 0 (standard) genereras ett nummer baserat på systemklockan.

     Ett frövärde är valfritt, men du bör ange ett värde om du vill ha reproducerbarhet över körningar av samma pipeline.  

5.  För **Mått för mätning av prestanda**väljer du ett enda mått som ska användas när du beräknar modellkvalitet efter permutation.  

     Azure Machine Learning-designern stöder följande mått, beroende på om du utvärderar en klassificerings- eller regressionsmodell:  

    -   **Klassificering**

        Noggrannhet, Precision, Återkallande  

    -   **Regression**

        Precision, återkallande, genomsnittligt absolut fel, rotmedelvärdet kvadratfel, relativt absolut fel, relativt fyrkantigt fel, bestämningskoefficient  

     En mer detaljerad beskrivning av dessa utvärderingsmått och hur de beräknas finns i [Utvärdera modell](evaluate-model.md).  

6.  Skicka pipelinen.  

7.  Modulen matar ut en lista med funktionskolumner och poängen som är associerade med dem. Listan rangordnas i fallande ordning av poängen.  


##  <a name="technical-notes"></a>Tekniska anmärkningar

Permutationsfunktionsbetesvikt fungerar genom att slumpmässigt ändra värdena för varje funktionskolumn, en kolumn i taget. Den utvärderar sedan modellen. 

De rankningar som modulen tillhandahåller skiljer sig ofta från de du får från [filterbaserat funktionsval](filter-based-feature-selection.md). Filterbaserat funktionsval beräknar poängen *innan* en modell skapas. 

Anledningen till skillnaden är att Permutationsfunktionsbetebetebetydelse inte mäter associationen mellan en funktion och ett målvärde. I stället fångar den hur mycket inflytande varje funktion har på förutsägelser från modellen.
  
## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
