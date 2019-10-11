---
title: Azure Resource Manager vCPU-begäran om kvot ökning | Microsoft Docs
description: Azure Resource Manager vCPU-begäran om kvot ökning
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248656"
---
# <a name="quota-increase-requests"></a>Begäranden om att öka kvoten

Resource Manager-vCPU kvoter för virtuella datorer och skalnings uppsättningar för virtuella datorer tillämpas på två nivåer för varje prenumeration i varje region. 

Den första nivån är den totala regionala virtuella processorer-gränsen (i alla VM-serien) och den andra nivån är virtuella processorer-gränsen för VM-serien (till exempel D-serien virtuella processorer). När du vill distribuera en ny virtuell dator måste summan av den nya och befintliga virtuella processorer-användningen för den virtuella dator serien inte överskrida vCPU-kvoten som har godkänts för den aktuella VM-serien. Dessutom bör det totala antalet nya och befintliga vCPU som distribueras över alla VM-serien inte överskrida den totala regionala virtuella processorer-kvoten som har godkänts för prenumerationen. Om någon av dessa kvoter överskrids, kommer distributionen av virtuella datorer inte att tillåtas.
Du kan begära en ökning av virtuella processorer kvot gräns för VM-serien från Azure Portal. En ökning av kvoten för VM-serien ökar automatiskt den totala regionala virtuella processorer-gränsen med samma belopp. 

När en ny prenumeration skapas får den totala regionala virtuella processorer inte vara lika med summan av standard kvoter för vCPU för alla enskilda VM-serier. Detta kan resultera i en prenumeration med tillräcklig kvot för varje enskild VM-serie som du vill distribuera, men inte tillräckligt med kvot för den totala regionala virtuella processorer för alla distributioner. I så fall måste du skicka en begäran om att öka den totala regionala virtuella processorer-gränsen explicit. Den totala regionala virtuella processorer-gränsen får inte överstiga summan av den godkända kvoten över alla VM-serier för regionen.

Läs mer om kvoter på sidan för [vCPU kvoter för virtuell dator](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och sidan för [Azure-prenumeration och tjänst begränsningar](https://aka.ms/quotalimits) . 

