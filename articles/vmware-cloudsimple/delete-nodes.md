---
title: Ta bort noder för VMware-lösning från CloudSimple - Azure
description: Lär dig hur du tar bort noder från din VMWare med CloudSimple-distribution
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024746"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Ta bort noder från Azure VMware-lösning från CloudSimple

CloudSimple-noder mäts när de har skapats.  Noder måste tas bort för att stoppa mätningen av noderna.  Du tar bort noderna som inte används från Azure-portalen.

## <a name="before-you-begin"></a>Innan du börjar

En nod kan endast tas bort under följande förhållanden:

* Ett privat moln som skapats med noderna tas bort.  Information om hur du tar bort ett privat moln finns i [Ta bort en Azure VMware-lösning av CloudSimple Private Cloud](delete-private-cloud.md).
* Noden har tagits bort från det privata molnet genom att krympa det privata molnet.  Information om hur du krymper ett privat moln finns i [Shrink Azure VMware Solution by CloudSimple Private Cloud](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Ta bort CloudSimple-nod

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple Noder**.

   ![Sök CloudSimple-noder](media/create-cloudsimple-node-search.png)

3. Välj **CloudSimple-noder**.

4. Välj noder som inte tillhör ett privat moln att ta bort.  **Kolumnen PRIVAT MOLNNAMN** visar det privata molnnamn som en nod tillhör.  Om en nod inte används av ett privat moln är värdet tomt. 

    ![Välj CloudSimple-noder](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Endast noder som inte är en del av det privata molnet kan tas bort.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Private Cloud](cloudsimple-private-cloud.md)
