---
title: 'Utvärdera rekommendation: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen utvärdera Rekommenderadere i Azure Machine Learning-tjänsten för att utvärdera de exakta modell förutsägelserna.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dad11f0e03e55186dfee1e7a4f1f82fb44275693
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518000"
---
# <a name="evaluate-recommender"></a>Utvärdera rekommendation

Den här artikeln beskriver hur du använder modulen **utvärdera rekommenderadere** i Azure Machine Learning designer (för hands version) för att mäta precisionen för förutsägelser som gjorts av en rekommendations modell. Med den här modulen kan du utvärdera fyra olika typer av rekommendationer:  
  
-   Omdömen som förutsägs för en viss användare och objekt  
  
-   Objekt som rekommenderas för en specifik användare  
  
När du skapar förutsägelser med en rekommendations modell returneras något annorlunda resultat för var och en av de förutsägelse typer som stöds. Utvärderingen av bedömnings **rekommendationer** härleder typen av förutsägelse från kolumn formatet för den returnerade data uppsättningen. Till exempel kan den resulterande **data uppsättningen** innehålla:

- användar objekt – betygs ätter tredubbla
- användare och deras rekommenderade objekt

Modulen tillämpar också lämpliga prestanda mått baserat på vilken typ av förutsägelse som görs. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Så här konfigurerar du utvärderings rekommendationen

I modulen **utvärdera rekommenderar** vi jämför utdata från en rekommendations modell med motsvarande "sanningen"-data. Till exempel ger den [SVD-rekommenderade](score-svd-recommender.md) modulen poäng med Poäng data uppsättningar som kan analyseras med **utvärderings rekommendation**.

### <a name="requirements"></a>Krav

**Utvärderings rekommendation** kräver följande data uppsättningar som indata. 
  
#### <a name="test-dataset"></a>Test data uppsättning

**Test data uppsättningen** innehåller data för "mark sanningen" i form av **tredubbla användar objekt – omdöme**.  

#### <a name="scored-dataset"></a>Resultat data mängd

Den returnerade **data uppsättningen** innehåller de förutsägelser som genererades av rekommendations modellen.  
  
Kolumnerna i den här andra data uppsättningen är beroende av vilken typ av förutsägelse du utförde under poängsättningen. Den resulterande data uppsättningen kan till exempel innehålla något av följande:

- Användare, objekt och klassificeringar som användaren sannolikt skulle ge objektet
- En lista över användare och objekt som rekommenderas för dem 

### <a name="metrics"></a>Mått

Prestanda måtten för modellen genereras baserat på typen av Indatatyp. Mer information finns i följande avsnitt:

+ [Utvärdera förväntade värderingar](#evaluate-predicted-ratings)
+ [Utvärdera objekt rekommendationer](#evaluate-item-recommendations)

## <a name="evaluate-predicted-ratings"></a>Utvärdera förväntade värderingar  

När du utvärderar förväntade betyg måste den returnerade data uppsättningen (den andra indatan för att **utvärdera rekommendationen**) innehålla **tredubbla objekt för användar objekts klassificering**som uppfyller följande krav:
  
-   Den första kolumnen i data uppsättningen innehåller användar identifierare.  
  
-   Den andra kolumnen innehåller objekt identifierare.  
  
-   Den tredje kolumnen innehåller motsvarande användar objekts klassificeringar.  
  
> [!IMPORTANT] 
> För att utvärderingen ska lyckas måste kolumn namnen vara `User`, `Item`respektive `Rating`.  
  
**Utvärderings rekommendationen** jämför klassificeringarna i sanningen-datauppsättningen med de förväntade klassificeringarna för den returnerade data uppsättningen och beräknar **medelvärdet för det absoluta felet** (Mae) och **rotens genomsnitts fel** (rmse).



## <a name="evaluate-item-recommendations"></a>Utvärdera objekt rekommendationer

När du utvärderar objekt rekommendationer använder du en resultat uppsättning som innehåller de rekommenderade objekten för varje användare:
  
-   Den första kolumnen i data mängden måste innehålla användar-ID.  
  
-   Alla efterföljande kolumner ska innehålla motsvarande rekommenderade objekt identifierare, sorterade efter hur relevant ett objekt är för användaren. 

    Innan du ansluter den här data uppsättningen rekommenderar vi att du sorterar data uppsättningen så att de mest relevanta objekten kommer först.  



> [!IMPORTANT] 
> För att **utvärderings rekommendationen** ska fungera måste kolumn namnen vara `User`, `Item 1`, `Item 2`, `Item 3` och så vidare.  
  
**Utvärdera rekommendationer** beräknar den genomsnittliga normaliserade rabatten för den ackumulerade vinsten (**NDCG**) och returnerar den i data uppsättningen för utdata.  
  
Eftersom det är omöjligt att känna till det faktiska "mark sanningen" för rekommenderade objekt, använder **utvärderings** funktionen användar objekt klassificeringarna i test data uppsättningen som vinster vid beräkningen av NDCG. För att utvärdera måste du bara skapa rekommendationer för objekt med sanningen klassificeringar (i test data uppsättningen).  
  

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 
