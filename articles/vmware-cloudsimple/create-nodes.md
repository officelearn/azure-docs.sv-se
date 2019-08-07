---
title: Etablera noder för VMware-lösning av CloudSimple – Azure
description: Lär dig hur du lägger till noder i VMWare med CloudSimple-distribution
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9eb02f04b5873e5906782a27ce833a724ceecfe3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812375"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Etablera noder för VMware-lösning av CloudSimple – Azure

Etablera noder i Azure Portal. Sedan kan du konfigurera kapaciteten betala per användning för din CloudSimple privata moln miljö.

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>Lägg till en etablerad nod i ditt CloudSimple privata moln

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-noder**.

   ![Sök CloudSimple-noder](media/create-cloudsimple-node-search.png)

3. Välj **CloudSimple-noder**.
4. Klicka på **Lägg till** för att skapa noder.

    ![Lägg till CloudSimple-noder](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill etablera CloudSimple-noder.
6. Välj resurs grupp för noderna. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange prefixet för att identifiera noderna.
8. Välj platsen för nodens resurser.
9. Välj den dedikerade plats som ska vara värd för nodens resurser.
10. Välj nodtyp. Du kan välja [alternativet CS28 eller CS36](cloudsimple-node.md). Det senare alternativet inkluderar den maximala beräknings-och minnes kapaciteten.
11. Välj antalet noder som ska etableras.
12. Välj **Granska + Skapa**.
13. Granska inställningarna. Klicka på **föregående**om du vill ändra inställningarna.
14. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

* [Skapa privat moln](https://docs.azure.cloudsimple.com/create-private-cloud/)
