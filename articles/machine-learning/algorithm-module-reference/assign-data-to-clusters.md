---
title: 'Tilldela Data till klustret: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder tilldela Data till kluster-modulen i Azure Machine Learning-tjänsten för att bedöma klustringsmodell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028777"
---
# <a name="assign-data-to-clusters"></a>Tilldela Data till ett kluster

Den här artikeln beskriver hur du använder den [tilldela Data till ett kluster](assign-data-to-clusters.md) modul i visuella gränssnittet att generera förutsägelser med en klustringsmodell som har tränats med K-Means klustring algoritmen.

Modulen returnerar en datauppsättning som innehåller sannolika tilldelningar för varje ny datapunkt. 


## <a name="how-to-use-assign-data-to-clusters"></a>Hur du använder tilldela Data till ett kluster
  
1.  Leta upp en tidigare tränade klustringsmodell i visuella gränssnittet. Du kan skapa och träna en klustringsmodell genom att använda någon av följande metoder:  
  
    - Konfigurera en K-means algoritmen med hjälp av den [K-Means klustring](k-means-clustering.md) modulen och sedan träna modellen med en datauppsättning och [träna Clustering-modellen](train-clustering-model.md) modulen.  
  
  
    Du kan också lägga till en befintlig tränade klustringsmodell från den **sparade modeller** i din arbetsyta.

2. Koppla den tränade modellen till den vänstra indataporten för [tilldela Data till ett kluster](assign-data-to-clusters.md).  

3. Koppla en ny datauppsättning som indata. I den här datauppsättningen är etiketter valfria. I allmänhet är clustering en oövervakad learning metod så inte är det troligt att du vet kategorier i förväg.

    Indatakolumnerna måste dock vara samma som de kolumner som har använts i utbildning clustering-modell eller ett fel inträffar.

    > [!TIP]
    > För att minska antalet kolumner utdata från klustret förutsägelser kan använda [Välj kolumner i datauppsättning](select-columns-in-dataset.md), och välj en delmängd av kolumnerna. 
    
4. Lämna alternativet **söka efter Lägg till eller avmarkera endast resultat för** markerad om du vill att resultatet ska innehålla den fullständiga datauppsättningen för indata, tillsammans med en kolumn som visar resultatet (kluster tilldelningar).
  
    Om du avmarkerar det här alternativet får du tillbaka resultaten. Detta kan vara användbart när du skapar förutsägelser som en del av en webbtjänst.
  
5.  Kör experimentet.  
  
### <a name="results"></a>Resultat

 
+  Om du vill visa värdena i datauppsättningen, högerklicka på modulen, Välj **resultera datauppsättningar**, och klicka på **visualisera**.

