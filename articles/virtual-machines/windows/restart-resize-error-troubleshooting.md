---
title: "VM att starta om eller ändrar storlek på problem i Azure | Microsoft Docs"
description: "Felsökning av Resource Manager distributionsproblem med att starta om eller ändrar storlek på en befintlig Windows virtuell dator i Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.workload: required
ms.date: 11/03/2017
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2fecb9ebf359e3d3d23d5af0f60882cdfac5ff45
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Felsöka distributionsproblem med att starta om eller ändrar storlek på en befintlig Windows virtuell dator i Azure
När du försöker starta en stoppad Azure virtuell dator (VM), eller ändra storlek på en befintlig Azure VM är vanligt fel du stöter på ett Allokeringsfel. Det här felet uppstår när det kluster eller den region har inte resurser som är tillgänglig eller har inte stöd för den begärda VM-storleken.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Samla in aktiviteten loggar
Starta felsökning genom att samla in aktivitetsloggar för att identifiera fel som är associerade med problemet. Följande länkar innehåller detaljerad information om processen:

[Visa distributionsåtgärder](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Visa aktivitetsloggar att hantera Azure-resurser](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Fel när du startar en stoppad virtuell dator
Försök att starta en stoppad virtuell dator, men ett fel vid allokering.

### <a name="cause"></a>Orsak
Begäran om att starta stoppad VM måste göras i det ursprungliga klustret som är värd för Molntjänsten. Klustret har dock inte ledigt utrymme för att slutföra begäran.

### <a name="resolution"></a>Lösning
* Stoppa alla virtuella datorer i tillgänglighetsuppsättningen och sedan starta om varje virtuell dator.
  
  1. Klicka på **resursgrupper** > *resursgruppen* > **resurser** > *din tillgänglighetsuppsättning* > **virtuella datorer** > *din virtuella dator* > **stoppa**.
  2. När alla virtuella datorer har slutat, Välj varje stoppad virtuell dator och klicka på Start.
* Försöka starta om vid ett senare tillfälle.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Ett fel uppstod när du ändrar storlek på en befintlig virtuell dator
Försök att ändra storlek på en befintlig virtuell dator men får ett Allokeringsfel.

### <a name="cause"></a>Orsak
Begäran att ändra storlek på den virtuella datorn måste göras i det ursprungliga klustret som är värd för Molntjänsten. Klustret stöder dock inte den begärda VM-storleken.

### <a name="resolution"></a>Lösning
* Försök igen med begäran med en mindre VM-storlek.
* Om storleken på den begärda virtuella datorn inte kan ändras:
  
  1. Stoppa alla virtuella datorer i tillgänglighetsuppsättningen.
     
     * Klicka på **resursgrupper** > *resursgruppen* > **resurser** > *din tillgänglighetsuppsättning* > **virtuella datorer** > *din virtuella dator* > **stoppa**.
  2. Stoppa alla virtuella datorer, ändra storlek på den önskade virtuella datorn till en större storlek.
  3. Välj den storleksändrade virtuella datorn och klicka på **starta**, och sedan starta varje stoppad virtuell dator.

## <a name="next-steps"></a>Nästa steg
Om du får problem när du skapar en ny Windows virtuell dator i Azure, se [felsöka distributionsproblem med att skapa en ny Windows virtuell dator i Azure](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

