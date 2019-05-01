---
title: Köpa noder för VMware-lösning genom CloudSimple - Azure
description: Lär dig hur du lägger till noder i din VMWare med CloudSimple distribution
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9f5108207c4f8debc65b9d1fe625ae7033ca4e70
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577187"
---
# <a name="purchase-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Köpa noder för VMware-lösning genom CloudSimple - Azure

Etablera noder i Azure-portalen. Du kan sedan konfigurera betala-som-du gå kapacitet för miljön CloudSimple privat moln.

## <a name="add-a-purchased-node-to-your-cloudsimple-private-cloud"></a>Lägga till en köpta nod i ditt privata moln CloudSimple

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple noder**.

   ![Sök efter CloudSimple noder](media/create-cloudsimple-node-search.png)

3. Välj **CloudSimple noder**.
4. Klicka på **Lägg till** att skapa noderna.

    ![Add CloudSimple Nodes](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill köpa CloudSimple noder.
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
