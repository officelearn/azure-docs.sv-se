---
title: 'Träna SVD-rekommendation: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen för att träna SVD-rekommenderar i Azure Machine Learning för att träna en Bayesian-rekommendation med hjälp av SVD-algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dc78322b09548e4e9c2f666e0c8315ce688d6541
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428425"
---
# <a name="train-svd-recommender"></a>Träna SVD-rekommenderare

Den här artikeln beskriver hur du använder modulen för att träna SVD-rekommendation i Azure Machine Learning designer (för hands version). Använd den här modulen för att träna en rekommendations modell baserat på SVD-algoritmen (Single Value diskomposition).  

Modulen för att träna SVD-rekommendation läser en data uppsättning av tredubbla användar objekts bedömningar. Den returnerar en utbildad SVD-rekommendation. Du kan sedan använda den tränade modellen för att förutsäga klassificeringar eller skapa rekommendationer med hjälp av funktionen [Scores i SvD-rekommenderat](score-svd-recommender.md) läge.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Mer om rekommendations modeller och SVD-rekommendation  

Det huvudsakliga syftet med ett rekommendations system är att rekommendera ett eller flera *objekt* till *användare* i systemet. Exempel på ett objekt kan vara en film, restaurang, bok eller låt. En användare kan vara en person, en grupp personer eller en annan entitet med objekt inställningar.  

Det finns två huvudsakliga metoder för rekommenderade system: 

+ En **innehålls baserad** metod utnyttjar funktioner för både användare och objekt. Användare kan beskrivas av egenskaper som ålder och kön. Objekt kan beskrivas av egenskaper som författare och tillverkare. Du kan hitta typiska exempel på innehållsbaserade rekommendations system på sociala matchmaking-webbplatser. 
+ Vid **gemensam filtrering** används bara identifierare för användarna och objekten. Den hämtar implicit information om dessa entiteter från en (sparse) mat ris av klassificeringar som tilldelas av användarna till objekten. Vi kan lära dig om en användare från de objekt som de har betygsatt och från andra användare som har betygsatt samma objekt.  

SVD-rekommendationen använder identifierare för användare och objekt, samt en matris med klassificeringar som tilldelas av användarna till objekten. Det är en *samarbets rekommendation*. 

Mer information om SVD-rekommenderat finns i relevant Research Paper: [matris factorization-tekniker för rekommenderade system](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Så här konfigurerar du träna SVD-rekommenderat  

### <a name="prepare-data"></a>Förbered data

Innan du använder modulen måste dina indata vara i det format som rekommendations modellen förväntar sig. Det krävs en tränings data uppsättning för användar objekts bedömning av tredubbel information.

+ Den första kolumnen innehåller användar identifierare.
+ Den andra kolumnen innehåller objekt identifierare.
+ Den tredje kolumnen innehåller omdömet för paret User-Item. Klassificerings värden måste vara en numerisk typ.  

Data uppsättningen **restaurang klassificering** i Azure Machine Learning designer (Välj **sparade data uppsättningar** och sedan **exempel**) visar det förväntade formatet:

|userID|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

Från det här exemplet kan du se att en enskild användare har klassificerat två olika restauranger. 

### <a name="train-the-model"></a>Träna modellen

1.  Lägg till modulen för att träna SVD-rekommenderat i din pipeline i designern och Anslut den till tränings data.  
   
2.  För **antal faktorer**anger du antalet faktorer som ska användas med rekommendationen.  
    
    Varje faktor mäter hur mycket användaren är relaterad till objektet. Antalet faktorer är också dimensionalitet för latent faktor utrymme. När antalet användare och objekt ökar är det bättre att ange ett större antal faktorer. Men om antalet är för stort kan prestanda släppas.
    
3.  **Antalet iterationer av rekommendations algoritm** anger hur många gånger algoritmen ska bearbeta indata. Ju högre siffra är, desto mer exakta förutsägelserna. Ett högre antal innebär dock lägre utbildning. Standardvärdet är 30.

4.  För **inlärnings hastighet**anger du ett tal mellan 0,0 och 2,0 som definierar steg storleken för inlärningen.

    Inlärnings frekvensen avgör storleken på steget vid varje iteration. Om steg storleken är för stor kan du överskrida den optimala lösningen. Om steg storleken är för liten tar det längre tid att hitta den bästa lösningen genom inlärningen. 
  
5.  Köra en pipeline.  


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
