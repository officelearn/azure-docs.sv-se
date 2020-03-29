---
title: 'Utvärdera Recommender: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Utvärdera rekommenderare i Azure Machine Learning för att utvärdera noggrannheten i rekommenderarmodellprognoser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76312268"
---
# <a name="evaluate-recommender"></a>Utvärdera rekommenderare

I den här artikeln beskrivs hur du använder modulen Utvärdera rekommenderare i Azure Machine Learning designer (förhandsversion). Målet är att mäta riktigheten av förutsägelser som en rekommendationsmodell har gjort. Med den här modulen kan du utvärdera olika typer av rekommendationer:  
  
-   Betyg som förutses för en användare och ett objekt    
-   Objekt som rekommenderas för en användare  
  
När du skapar förutsägelser med hjälp av en rekommendationsmodell returneras något olika resultat för var och en av dessa förutsägelsetyper som stöds. Modulen Utvärdera recommender härleder den typ av förutsägelse från kolumnformatet för den poängsatta datauppsättningen. Den poängsatta datauppsättningen kan till exempel innehålla:

- Tredubblad klassificering för användare och objekt
- Användare och deras rekommenderade objekt

Modulen tillämpar också lämpliga prestandamått, baserat på vilken typ av förutsägelse som görs. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Konfigurera Utvärdera recommender

Modulen Utvärdera recommender jämför förutsägelseutdata med hjälp av en rekommendationsmodell med motsvarande "ground truth"-data. Modulen [Poäng SVD Recommender](score-svd-recommender.md) producerar till exempel poängdatauppsättningar som du kan analysera med hjälp av Utvärdera Rekommenderare.

### <a name="requirements"></a>Krav

Utvärdera Recommender kräver följande datauppsättningar som indata. 
  
#### <a name="test-dataset"></a>Testa datauppsättning

Testdatauppsättningen innehåller "ground truth"-data i form av tredubblats med användarpostklassificering.  

#### <a name="scored-dataset"></a>Poängsatt datauppsättning

Den poängsatta datauppsättningen innehåller förutsägelser som rekommendationsmodellen genererade.  
  
Kolumnerna i den här andra datauppsättningen beror på vilken typ av förutsägelse som du utförde under bedömningsprocessen. Den poängsatta datauppsättningen kan till exempel innehålla något av följande:

- Användare, objekt och de betyg som användaren sannolikt skulle ge för objektet
- En lista över användare och objekt som rekommenderas för dem 

### <a name="metrics"></a>Mått

Prestandamått för modellen genereras baserat på typen av indata. Följande avsnitt innehåller information.

## <a name="evaluate-predicted-ratings"></a>Utvärdera förväntade betyg  

När du utvärderar förväntade klassificeringar måste den poängsatta datauppsättningen (den andra ingången till Utvärdera Recommender) innehålla tre tripplar för användar-artikelklassificering som uppfyller dessa krav:
  
-   Den första kolumnen i datauppsättningen innehåller användaridentifierarna.    
-   Den andra kolumnen innehåller objektidentifierarna.  
-   Den tredje kolumnen innehåller motsvarande användarobjektklassificeringar.  
  
> [!IMPORTANT] 
> För att utvärderingen ska lyckas `User` `Item`måste `Rating`kolumnnamnen vara , och .  
  
Utvärdera Recommender jämför betygen i datauppsättningen "ground truth" med de förväntade klassificeringarna för den poängsatta datauppsättningen. Det beräknar sedan det genomsnittliga absoluta felet (MAE) och rotmedelvärdet kvadratfel (RMSE).



## <a name="evaluate-item-recommendations"></a>Utvärdera artikelrekommendationer

När du utvärderar artikelrekommendationer använder du en poängsatt datauppsättning som innehåller de rekommenderade objekten för varje användare:
  
-   Den första kolumnen i datauppsättningen måste innehålla användaridentifieraren.    
-   Alla efterföljande kolumner ska innehålla motsvarande rekommenderade artikelidentifierare, sorterade efter hur relevant ett objekt är för användaren. 

Innan du ansluter den här datauppsättningen rekommenderar vi att du sorterar datauppsättningen så att de mest relevanta objekten kommer först.  

> [!IMPORTANT] 
> För att Utvärdera rekommenderare ska fungera `User` `Item 1`måste `Item 2` `Item 3` kolumnnamnen vara , , och så vidare.  
  
Utvärdera Recommender beräknar den genomsnittliga normaliserade diskonterade kumulativa förstärkningen (NDCG) och returnerar den i utdatauppsättningen.  
  
Eftersom det är omöjligt att veta den faktiska "ground truth" för de rekommenderade objekten, utvärdera Recommender använder användaren-objekt betyg i testet datauppsättningen som vinster i beräkningen av NDCG. För att utvärdera får den rekommenderade poängsättningsmodulen endast ta fram rekommendationer för objekt med betyg för "grundsanning" (i testdatauppsättningen).  
  

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
