---
title: Vm starta om eller ändra storlek på problem i Azure | Microsoft-dokument
description: Felsöka distributionsproblem i Resource Manager med omstart eller storleksändring av en befintlig virtuell dator i Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a6532558107463311c4225b9855bc4cd3f19eed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965613"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Felsöka distributionsproblem relaterade till omstart eller storleksändring av en befintlig virtuell Windows-dator i Azure
När du försöker starta en stoppad Virtuell Virtuell Dator (VM) eller ändrar storlek på en befintlig Virtuell Azure-dator, är det vanliga felet som du stöter på ett allokeringsfel. Det här felet uppstår när klustret eller regionen antingen inte har tillgängliga resurser eller inte kan stödja den begärda vm-storleken.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Samla in aktivitetsloggar
Om du vill börja felsöka samlar du in aktivitetsloggarna för att identifiera felet som är associerat med problemet. Följande länkar innehåller detaljerad information om processen:

[Visa distributionsåtgärder](../../azure-resource-manager/templates/deployment-history.md)

[Visa aktivitetsloggar för att hantera Azure-resurser](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Fel när du startar en stoppad virtuell dator
Du försöker starta en stoppad virtuell dator men får ett allokeringsfel.

### <a name="cause"></a>Orsak
Begäran om att starta den stoppade virtuella datorn måste göras på det ursprungliga klustret som är värd för molntjänsten. Klustret har dock inte ledigt utrymme för att uppfylla begäran.

### <a name="resolution"></a>Lösning
* Stoppa alla virtuella datorer i tillgänglighetsuppsättningen och starta sedan om varje virtuell dator.
  
  1. Klicka på **Resursgrupper** > *din resursgrupp* > **Resurser** > som din tillgänglighet*anger* > **virtuella datorer** > *för din virtuella dator* > **Stop**.
  2. När alla virtuella datorer har stoppats markerar du var och en av de stoppade virtuella datorerna och klickar på Start.
* Försök igen begäran vid ett senare tillfälle.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Fel när du ändrar storlek på en befintlig virtuell dator
Du försöker ändra storlek på en befintlig virtuell dator men få ett allokeringsfel.

### <a name="cause"></a>Orsak
Begäran om att ändra storlek på den virtuella datorn måste göras på det ursprungliga klustret som är värd för molntjänsten. Klustret stöder dock inte den begärda vm-storleken.

### <a name="resolution"></a>Lösning
* Försök igen med en mindre vm-storlek.
* Om storleken på den begärda virtuella datorn inte kan ändras:
  
  1. Stoppa alla virtuella datorer i tillgänglighetsuppsättningen.
     
     * Klicka på **Resursgrupper** > *din resursgrupp* > **Resurser** > som din tillgänglighet*anger* > **virtuella datorer** > *för din virtuella dator* > **Stop**.
  2. När alla virtuella datorer har stannat ändrar du storlek på den önskade virtuella datorn till en större storlek.
  3. Markera den storleksbesterade virtuella datorn och klicka på **Start**och starta sedan var och en av de stoppade virtuella datorerna.

## <a name="next-steps"></a>Nästa steg
Om du stöter på problem när du skapar en ny Windows-virtuell dator i Azure läser [du Felsöka distributionsproblem med att skapa en ny virtuell Windows-dator i Azure](../windows/troubleshoot-deployment-new-vm.md).

