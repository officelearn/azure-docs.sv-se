---
title: Azure Resource Manager vCPU kvot öka begäranden
description: Azure Resource Manager vCPU kvot öka begäranden
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843675"
---
# <a name="quota-increase-requests"></a>Begäranden om att öka kvoten

Resurshanterarens vCPU-kvoter för virtuella datorer och skaluppsättningar för virtuella datorer tillämpas på två nivåer för varje prenumeration i varje region.

Den första nivån är gränsen för totalt regionalt vCPUs i alla VM-serier. Den andra nivån är gränsen för vcpus-serien per VM-serie, till exempel virtuella processorer i D-serien. När som helst får en ny virtuell dator distribueras, får summan av ny och befintlig vCPUs-användning för den VM-serien inte överstiga den vCPU-kvot som godkänts för just den VM-serien. Dessutom bör det totala antalet nya och befintliga vCPU som distribueras över alla VM-serier inte överstiga den totala regionala vCPUs-kvoten som godkänts för prenumerationen. Om någon av dessa kvoter överskrids tillåts inte vm-distributionen.
Du kan begära en ökning av kvotgränsen för vCPUs för VM-serien från Azure-portalen. En ökning av vm-seriens kvot ökar automatiskt gränsen för totalt regionalt vCPUs med samma belopp.

När en ny prenumeration skapas kanske standardvärdet för totalt regionalt vCP:er inte är lika med summan av vCPU-standardkvoter för alla enskilda virtuella datorer. Detta faktum kan resultera i en prenumeration med tillräckligt med kvot för varje enskild VM-serie som du vill distribuera. Det kan sakna tillräckligt med kvot för totala regionala virtuella processorer för alla distributioner. I det här fallet måste du skicka en begäran om att öka gränsen För totalt regionalt vCPUs uttryckligen. Gränsen för totalt regionalt vCPUs kan inte överstiga summan av den godkända kvoten i alla VM-serier för regionen.

Mer information om kvoter finns i [virtuella dator vCPU-kvoter](../../virtual-machines/windows/quotas.md) och [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

