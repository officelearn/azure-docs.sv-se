---
title: 'Träna SVD-rekommendation: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen träna SVD-rekommendation i Azure Machine Learning-tjänsten för att träna en Bayesian-rekommendation med hjälp av SVD-algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3b86d77470a4f3d4fe5b005e562a8adae21f8bc7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515673"
---
# <a name="train-svd-recommender"></a>Träna SVD-rekommendation

Den här artikeln beskriver hur du använder modulen för att **träna SVD-rekommendation** i Azure Machine Learning designer (för hands version). Använd den här modulen för att träna en rekommendations modell baserat på **SVD** -algoritm (Single Value diskomposition).  

Modulen för att **träna SVD-rekommendation** läser en data uppsättning av tredubbla användar objekts bedömningar. Den returnerar en utbildad SVD-rekommendation. Du kan sedan använda den tränade modellen för att förutsäga klassificeringar eller skapa rekommendationer med hjälp av funktionen [Scores i SvD-rekommenderat](score-svd-recommender.md) läge.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Mer om rekommendations modeller och SVD-rekommendation  

Det huvudsakliga syftet med ett rekommendations system är att rekommendera ett eller flera *objekt* till *användare* i systemet. Exempel på ett objekt kan vara en film, restaurang, bok eller låt. En användare kan vara en person, en grupp av personer eller en annan entitet med objekt inställningar.  

Det finns två huvudsakliga metoder för rekommenderade system. 

+ Den första är den **innehållsbaserade** metoden, som använder funktioner för både användare och objekt. Användare kan beskrivas av egenskaper som ålder och kön, och objekt kan beskrivas av egenskaper som författare och tillverkare. Vanliga exempel på innehållsbaserade rekommendations system finns på sociala matchmaking-webbplatser. 
+ Den andra metoden är samordnad **filtrering**, som endast använder identifierare för användarna och objekten och hämtar implicit information om dessa entiteter från en (sparse) mat ris av klassificeringar som ges av användarna till objekten. Vi kan lära dig om en användare från de objekt som de har klassificerat och från andra användare som har betygsatt samma objekt.  

SVD-rekommendationen använder identifierare för användare och objekt, samt en matris med klassificeringar som tilldelas av användarna till objekten. Det är en **samarbets rekommendation**. 

Mer information om SVD-rekommenderat finns i relevant Research Paper: [matris factorization-tekniker för rekommenderade system](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Så här konfigurerar du träna SVD-rekommenderat  

+ [Förbered tränings data](#prepare-data)
+ [Träna modellen](#train-the-model)

### <a name="prepare-data"></a>Förbereda data

Innan du försöker använda modulen är det viktigt att dina data har det format som förväntas av rekommendations modellen. Det krävs en tränings data uppsättning för **användar objekts bedömning av tredubbel** information.

#### <a name="required-dataset-of-user-item-ratings"></a>Obligatorisk data uppsättning för användar objekt – klassificeringar

Det är viktigt att de indata som används för träning innehåller rätt typ av data i rätt format: 

+ Den första kolumnen måste innehålla användar identifierare.
+ Den andra kolumnen måste innehålla objekt identifierare.
+ Den tredje kolumnen innehåller omdömet för paret User-Item. Klassificerings värden måste vara en numerisk typ.  

                                                                                                                                                                                                           

Data uppsättningen **restaurang klassificering** i Azure Machine Learning designer (klicka på **sparade data uppsättningar** och sedan på **exempel**) visar det förväntade formatet:

|UserID|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

Från det här exemplet kan du se att en enskild användare har klassificerat två olika restauranger. 

### <a name="train-the-model"></a>Träna modellen

1.  Lägg till modulen för att **träna SVD-rekommenderat** i din pipeline i designern och Anslut den till tränings data.  
   
2.  För **antal faktorer**anger du det tal som anger antalet faktorer som ska användas med en rekommenderad rekommendation.  
    
    Varje faktor mäter hur mycket användaren är relaterad till objektet. Antalet faktorer är också dimensionalitet för latent faktor utrymme. När antalet användare och objekt ökar är det bättre att ange ett större antal faktorer. Men om antalet är för stort kan prestandan minska.
    
3.  **Antalet iterationer av rekommendations algoritmen**anger hur många gånger algoritmen ska bearbeta indata. Ju högre siffra, desto mer exakta förutsägelserna. träningen är dock långsammare. Standardvärdet är 30.

4.  För **inlärnings hastighet**anger du ett tal mellan 0,0 och 2,0 som definierar steg storleken under inlärningen.

    Inlärnings frekvensen avgör hur stor det steg som tas vid varje iteration. Om steg storleken är för stor kan du överskrida den optimala lösningen. Om steg storleken är för liten tar inlärningen längre tid att konvergera i den bästa lösningen. 
  
5.  Köra en pipeline.  


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 
