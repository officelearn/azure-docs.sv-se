---
title: Etablera noder för VMware-lösningar (AVS) – Azure
description: Lär dig hur du lägger till noder i din VMWare med AVS-distribution
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024814"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Etablera noder för Azure VMware-lösningar (AVS)

Etablera noder i Azure Portal. Sedan kan du ställa in "betala per användning"-kapacitet för din AVS-miljö för privata moln.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-avs-private-cloud"></a>Lägg till en nod i ditt moln privata moln

1. Välj **Alla tjänster**.
2. Sök efter **AVS-noder**.

   ![Sök i AVS-noder](media/create-cloudsimple-node-search.png)

3. Välj **AVS-noder**.
4. Klicka på **Lägg till** för att skapa noder.

    ![Lägg till AVS-noder](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill etablera AVS-noder.
6. Välj resurs grupp för noderna. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange prefixet för att identifiera noderna.
8. Välj platsen för nodens resurser.
9. Välj den dedikerade plats som ska vara värd för nodens resurser.
10. Välj [nodtyp](cloudsimple-node.md).
11. Välj antalet noder som ska etableras.
12. Välj **Granska + Skapa**.
13. Granska inställningarna. Klicka på **föregående**om du vill ändra inställningarna.
14. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett privat AVS-moln](create-private-cloud.md)
