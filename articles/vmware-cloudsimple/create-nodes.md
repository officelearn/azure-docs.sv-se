---
title: Etablera noder för VMware-lösning genom CloudSimple - Azure
description: Lär dig hur du lägger till noder i din VMWare med CloudSimple distribution
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33de07663c91f12d4e10c4661b841cd2dbe5a162
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165258"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Etablera noder för VMware-lösning genom CloudSimple - Azure

Etablera noder i Azure-portalen. Du kan sedan konfigurera betala-som-du gå kapacitet för miljön CloudSimple privat moln.

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>Lägga till en etablerad nod i ditt privata moln CloudSimple

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple noder**.

   ![Sök efter CloudSimple noder](media/create-cloudsimple-node-search.png)

3. Välj **CloudSimple noder**.
4. Klicka på **Lägg till** att skapa noderna.

    ![Add CloudSimple Nodes](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill etablera CloudSimple noder.
6. Välj resursgrupp för noderna. Lägg till en ny resursgrupp genom att klicka på **Skapa ny**.
7. Ange prefix för att identifiera noderna.
8. Välj plats för nod-resurser.
9. Välj den dedikerade platsen som värd för nod-resurser.
10. Välj nodtyp av. Du kan välja den [CS28 eller CS36](cloudsimple-node.md). Det senare alternativet innehåller maximal kapacitet för beräkning och minne.
11. Välj antalet noder för att etablera.
12. Välj **Granska + Skapa**.
13. Granska inställningarna. Om du vill ändra inställningar klickar du på **föregående**.
14. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

* [Skapa privata moln](https://docs.azure.cloudsimple.com/create-private-cloud/)
