---
title: 'Tåg SVD Recommender: Modul referens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Train SVD Recommender i Azure Machine Learning för att träna en Bayesiansk rekommenderare med hjälp av SVD-algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477416"
---
# <a name="train-svd-recommender"></a>Träna SVD-rekommenderare

I den här artikeln beskrivs hur du använder modulen Train SVD Recommender i Azure Machine Learning designer (förhandsversion). Använd den här modulen för att träna en rekommendationsmodell baserat på SVD-algoritmen (Single Value Decomposition).  

Modulen Train SVD Recommender läser en datauppsättning av tre tripplar för användarobjektklassificering. Den returnerar en utbildad SVD-rekommenderare. Du kan sedan använda den tränade modellen för att förutsäga klassificeringar eller generera rekommendationer med hjälp av modulen [Betygs-SVD Recommender.](score-svd-recommender.md)  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Mer om rekommendationsmodeller och SVD-rekommendationsbeklyss  

Huvudsyftet med ett rekommendationssystem är att rekommendera en eller flera *objekt* till *användare* av systemet. Exempel på ett objekt kan vara en film, restaurang, bok eller låt. En användare kan vara en person, en grupp personer eller en annan enhet med objektinställningar.  

Det finns två huvudsakliga metoder för att rekommendera system: 

+ En **innehållsbaserad** metod använder funktioner för både användare och objekt. Användare kan beskrivas med egenskaper som ålder och kön. Objekt kan beskrivas med egenskaper som författare och tillverkare. Du kan hitta typiska exempel på innehållsbaserade rekommendationssystem på sociala matchmaking webbplatser. 
+ **Gemensam filtrering** använder endast identifierare för användare och objekt. Den får implicit information om dessa entiteter från en (gles) matris med klassificeringar som ges av användarna till objekten. Vi kan lära oss om en användare från de objekt som de har betygsatt och från andra användare som har betygsatt samma objekt.  

SVD-användaren använder identifierare för användare och objekt, och en matris med klassificeringar som ges av användarna till objekten. Det är ett *samarbete recommender*. 

Mer information om SVD-rekommendationen finns i relevant forskningsrapport: [Matrix factorization techniques for recommender systems](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Så här konfigurerar du Train SVD Recommender  

### <a name="prepare-data"></a>Förbereda data

Innan du använder modulen måste indata vara i det format som rekommendationsmodellen förväntar sig. En utbildningsdatauppsättning med tre tripplar för användarpostklassificering krävs.

+ Den första kolumnen innehåller användaridentifierare.
+ Den andra kolumnen innehåller objektidentifierare.
+ Den tredje kolumnen innehåller klassificeringen för användarobjektsparet. Klassificeringsvärden måste vara numeriska.  

**Filmklassificeringsdatauppsättningen** i Azure Machine Learning designer (välj **Datauppsättningar** och sedan **Exempel)** visar det förväntade formatet:

![Film betyg](media/module/movie-ratings-dataset.png)

I det här exemplet kan du se att en enskild användare har betygsatt flera filmer. 

### <a name="train-the-model"></a>Träna modellen

1.  Lägg till modulen Train SVD Recommender i pipelinen i designern och anslut den till träningsdata.  
   
2.  För **Antal faktorer**anger du antalet faktorer som ska användas med den som rekommenderar.  
    
    Varje faktor mäter hur mycket användaren relaterar till artikeln. Antalet faktorer är också dimensionaliteten hos latent faktor utrymme. Med antalet användare och objekt ökar, är det bättre att ställa in ett större antal faktorer. Men om antalet är för stort kan prestandan sjunka.
    
3.  **Antal rekommendationsalgoritmiterationer** anger hur många gånger algoritmen ska bearbeta indata. Ju högre detta nummer är, desto mer exakt förutsägelser är. Ett högre antal innebär dock långsammare träning. Standardvärdet är 30.

4.  För **Utbildningsfrekvens**anger du ett tal mellan 0,0 och 2,0 som definierar stegstorleken för inlärning.

    Inlärningshastigheten bestämmer storleken på steget vid varje iteration. Om stegstorleken är för stor kan du överskrida den optimala lösningen. Om stegstorleken är för liten tar träningen längre tid att hitta den bästa lösningen. 
  
5.  Skicka pipelinen.  


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
