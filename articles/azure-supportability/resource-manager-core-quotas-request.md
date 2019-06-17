---
title: Azure Resource Manager-vCPU-kvoten öka begäranden | Microsoft Docs
description: Azure Resource Manager-vCPU förfrågningar om att öka kvoten
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9a997af984b92ea59cc02d99fbd66d8967ca31bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076814"
---
# <a name="quota-increase-requests"></a>Begäranden om att öka kvoten

Resource Manager vCPU-kvoter för virtuella datorer och VM-skalningsuppsättningar tillämpas på två nivåer för varje prenumeration i varje region. 

Den första nivån är gränsen för totalt antal regionala virtuella processorer (för alla VM-serier) och det andra lagret är den per VM vcpu: er gräns (till exempel D-serien virtuella processorer). Varje gång en ny virtuell dator är att distribueras, får summan av nya och befintliga virtuella processorer användning för VM serien inte överskrida vCPU-kvoten som godkänts för den specifika VM-serie. Dessutom kan får den antal för totalt antal nya och befintliga virtuella processorer som distribueras över alla VM-serier inte överstiga Totalt antal regionala kvoten för virtuella processorer som godkänts för prenumerationen. Om något av dessa kvoter överskrids får inte VM-distributionen.
Du kan begära en ökning av virtuella processorer kvotgränsen för VM-serierna från Azure-portalen. En ökning av kvoten för VM-serie ökar automatiskt gränsen för totalt antal regionala virtuella processorer med samma belopp. 

När en ny prenumeration skapas, kanske Totalt antal regionala virtuella processorer standard inte lika med summan av standard vCPU-kvoter för alla enskilda VM-serier. Detta kan resultera i en prenumeration med tillräckligt stor kvot för varje enskild VM-serie som du vill distribuera, men inte tillräcklig kvot för totalt antal regionala virtuella processorer för alla distributioner. I det här fallet behöver du ansöka om att öka gränsen för totalt antal regionala virtuella processorer uttryckligen. Totalt antal regionala virtuella processorer gränsen får inte överskrida summan av godkända kvot för alla VM-serier för regionen.

Läs mer om kvoter på den [VM vCPU-kvoter sidan](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) och [Azure-prenumeration och tjänstbegränsningar](https://aka.ms/quotalimits) sidan. 

