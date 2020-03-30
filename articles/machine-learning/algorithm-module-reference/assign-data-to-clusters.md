---
title: 'Tilldela data till kluster: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Tilldela data till kluster i Azure Machine Learning för att få klustermodell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 207172f10277589af2b22ae2f41b07234a0925b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477722"
---
# <a name="module-assign-data-to-clusters"></a>Modul: Tilldela data till kluster

I den här artikeln beskrivs hur du använder modulen *Tilldela data till kluster* i Azure Machine Learning designer (förhandsversion). Modulen genererar förutsägelser via en klustermodell som har tränats med *K-betyder klusteralgoritm.*

Modulen Tilldela data till kluster returnerar en datauppsättning som innehåller de troliga tilldelningarna för varje ny datapunkt. 

## <a name="how-to-use-assign-data-to-clusters"></a>Så här använder du Tilldela data till kluster
  
1. I Azure Machine Learning-designern letar du reda på en tidigare tränad klustermodell. Du kan skapa och träna en klustermodell med någon av följande metoder:  
  
    - Konfigurera K-medels klustringsalgoritmen med hjälp av [K-Means Clustering-modulen](k-means-clustering.md) och träna modellen med hjälp av en datauppsättning och modulen Train Clustering Model (den här artikeln).  
  
    - Du kan också lägga till en befintlig tränad klustermodell från gruppen **Sparade modeller** på arbetsytan.

2. Koppla den tränade modellen till den vänstra indataporten **i Tilldela data till kluster**.  

3. Koppla en ny datauppsättning som indata. 

   I den här datauppsättningen är etiketter valfria. I allmänhet är klustring en oövervakad inlärningsmetod. Du förväntas inte känna till kategorierna i förväg. Indatakolumnerna måste dock vara samma som de kolumner som användes för att träna klustermodellen eller ett fel inträffar.

    > [!TIP]
    > Om du vill minska antalet kolumner som skrivs till designern från klusterprognoserna använder [du Markera kolumner i datauppsättningen](select-columns-in-dataset.md)och väljer en delmängd av kolumnerna. 
    
4. Låt kryssrutan Kontrollera om det läggs **till eller avmarkera endast för resultat markeras** om du vill att resultaten ska innehålla den fullständiga indatauppsättningen, inklusive en kolumn som visar resultaten (klustertilldelningar).
  
    Om du avmarkerar den här kryssrutan returneras bara resultaten. Det här alternativet kan vara användbart när du skapar förutsägelser som en del av en webbtjänst.
  
5.  Skicka pipelinen.  
  
### <a name="results"></a>Resultat

+  Om du vill visa värdena i datauppsättningen högerklickar du på modulen och väljer sedan **Visualisera**. Eller Välj modul och växla till fliken **Utdata** på den högra panelen klickar du på histogramikonen i **portutgångarna** för att visualisera resultatet.

