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
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467203"
---
# <a name="module-assign-data-to-clusters"></a>Modul: Tilldela Data till ett kluster

Den här artikeln beskriver hur du använder den *tilldela Data till ett kluster* modul i det visuella gränssnittet i Azure Machine Learning. Modulen genererar förutsägelser via en klustringsmodell som har tränats med den *K-means klustring* algoritmen.

Tilldela Data till kluster-modulen returnerar en datauppsättning som innehåller sannolika tilldelningar för varje ny datapunkt. 


## <a name="how-to-use-assign-data-to-clusters"></a>Hur du använder tilldela Data till ett kluster
  
1. Leta upp en tidigare tränade klustringsmodell i det visuella gränssnittet i Azure Machine Learning. Du kan skapa och träna en klustringsmodell med någon av följande metoder:  
  
    - Konfigurera algoritmen K-means kluster med hjälp av den [K-Means klustring](k-means-clustering.md) modulen och träna modellen med hjälp av en datauppsättning och modulen träna Clustering-modellen (den här artikeln).  
  
    - Du kan också lägga till en befintlig tränade klustringsmodell från den **sparade modeller** i din arbetsyta.

2. Koppla den tränade modellen till den vänstra indataporten för **tilldela Data till ett kluster**.  

3. Koppla en ny datauppsättning som indata. 

   I den här datauppsättningen är etiketter valfria. Klustring är oftast en oövervakad inlärning-metod. Du förväntas inte vet kategorierna i förväg. Indatakolumnerna måste dock vara samma som de kolumner som har använts i utbildning clustering-modell eller ett fel inträffar.

    > [!TIP]
    > För att minska antalet kolumner som har skrivits till gränssnitt från klustret förutsägelser kan använda [Välj kolumner i datauppsättningen](select-columns-in-dataset.md), och välj en delmängd av kolumnerna. 
    
4. Lämna den **söka efter Lägg till eller avmarkera endast resultat för** Markera kryssrutan om du vill att resultatet ska innehålla fullständiga datauppsättningen för indata, inklusive en kolumn som visar resultatet (kluster tilldelningar).
  
    Om du avmarkerar den här kryssrutan, returneras endast resultat. Det här alternativet kan vara användbart när du skapar förutsägelser som en del av en webbtjänst.
  
5.  Kör experimentet.  
  
### <a name="results"></a>Resultat

+  Om du vill visa värdena i datauppsättningen, högerklicka på modulen, Välj **resultera datauppsättningar**, och välj sedan **visualisera**.

