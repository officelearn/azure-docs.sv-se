---
title: 'Tilldela data till kluster: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen tilldela data till kluster i Azure Machine Learning tjänst för att Visa kluster modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 56f608044716ea3655576c11aa7a62343215f508
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128995"
---
# <a name="module-assign-data-to-clusters"></a>Modulen Tilldela data till kluster

Den här artikeln beskriver hur du använder modulen *tilldela data till kluster* i Azure Machine Learning Visual Interface. Modulen genererar förutsägelser genom en kluster modell som har tränats med algoritmen *K-metod* .

Modulen tilldela data till kluster returnerar en data uppsättning som innehåller de sannolika tilldelningarna för varje ny data punkt. 


## <a name="how-to-use-assign-data-to-clusters"></a>Så här använder du tilldela data till kluster
  
1. Leta upp en tidigare tränad kluster modell i Azure Machine Learning Visual Interface. Du kan skapa och träna en kluster modell genom att använda någon av följande metoder:  
  
    - Konfigurera den N:te klustrade algoritmen med hjälp av modulen [K-betyder-kluster](k-means-clustering.md) och träna modellen med hjälp av en data uppsättning och modulen träna kluster modell (den här artikeln).  
  
    - Du kan också lägga till en befintlig tränad kluster modell från gruppen för **sparade modeller** i din arbets yta.

2. Koppla den tränade modellen till den vänstra Indataporten för att **tilldela data till kluster**.  

3. Bifoga en ny data uppsättning som indata. 

   I den här data uppsättningen är etiketter valfria. I allmänhet är klustring en oövervakad inlärnings metod. Du förväntas inte känna till kategorierna i förväg. Inmatade kolumner måste dock vara samma som de kolumner som användes i träna kluster modellen, eller ett fel uppstår.

    > [!TIP]
    > Om du vill minska antalet kolumner som skrivs till gränssnittet från kluster förutsägelserna använder du [Välj kolumner i data uppsättningen](select-columns-in-dataset.md)och väljer en delmängd av kolumnerna. 
    
4. Lämna kryss rutan **Markera för Lägg till eller ta bort kontroll för endast resultat** markerad om du vill att resultatet ska innehålla den fullständiga data uppsättningen, inklusive en kolumn som visar resultatet (kluster tilldelningar).
  
    Om du avmarkerar den här kryss rutan returneras bara resultatet. Det här alternativet kan vara användbart när du skapar förutsägelser som en del av en webb tjänst.
  
5.  Kör experimentet.  
  
### <a name="results"></a>Resultat

+  Om du vill visa värdena i data uppsättningen högerklickar du på modulen, väljer **resultat data uppsättningar**och väljer sedan **visualisera**.

