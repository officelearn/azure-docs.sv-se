---
title: Ta bort noder för VMware-lösningar (AVS) – Azure
description: Lär dig hur du tar bort noder från din VMWare med AVS-distribution
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024746"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Ta bort noder från Azure VMware-lösningen efter AVS

AVS-noder mäts när de väl har skapats. Noder måste tas bort för att stoppa datamätningen av noderna. Du tar bort de noder som inte används från Azure Portal.

## <a name="before-you-begin"></a>Innan du börjar

En nod kan bara tas bort under följande omständigheter:

* Ett privat moln moln som skapats med noderna tas bort. Om du vill ta bort ett moln privat moln kan du läsa [ta bort en Azure VMware-lösning per AVS-privat moln](delete-private-cloud.md).
* Noden har tagits bort från det privata AVS-molnet genom att minska det privata molnet i AVS-molnet. Information om hur du krymper ett privat moln moln finns i [minska Azure VMware-lösningen per AVS-baserat privat moln](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-avs-node"></a>Ta bort AVS-nod

1. Välj **Alla tjänster**.

2. Sök efter **AVS-noder**.

   ![Sök i AVS-noder](media/create-cloudsimple-node-search.png)

3. Välj **AVS-noder**.

4. Välj noder som inte tillhör ett moln privat moln att ta bort. I kolumnen **namn på molnets privata moln** visas det namn på det privata moln i AVS-molnet som en nod tillhör. Om en nod inte används av ett privat moln moln, är värdet tomt. 

    ![Välj AVS-noder](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Endast noder som inte är en del av det privata AVS-molnet kan tas bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [molnets privata moln](cloudsimple-private-cloud.md)
