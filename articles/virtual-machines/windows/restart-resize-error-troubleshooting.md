---
title: Virtuell dator startar om eller ändra storlek på problem i Azure | Microsoft Docs
description: Felsöka distributionsproblem i Resource Manager med att starta om eller ändra storlek på en befintlig Windows virtuell dator i Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.workload: required
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4e0c77c03856b4851ee5fe49bd6ae54d47f6c31
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092189"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Felsöka distributionsproblem med att starta om eller ändra storlek på en befintlig Windows-dator i Azure
När du försöker starta en stoppad Azure virtuell dator (VM), eller ändra storlek på en befintlig Azure VM är vanligt fel du stöter på ett Allokeringsfel. Det här felet uppstår när det kluster eller den region antingen inte har resurser som är tillgängliga eller har inte stöd för den begärda VM-storleken.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>Samla in aktivitetsloggar
Starta felsökning genom att samla in aktivitetsloggar för att identifiera felet som är associerade med problemet. Följande länkar innehåller detaljerad information om processen:

[Visa distributionsåtgärder](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Visa aktivitetsloggar för att hantera Azure-resurser](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Fel när du startar en stoppad virtuell dator
Försök att starta en stoppad virtuell dator men får ett Allokeringsfel.

### <a name="cause"></a>Orsak
Begäran om att starta stoppad virtuell dator måste göras i det ursprungliga klustret som är värd för Molntjänsten. Klustret har dock inte ledigt utrymme för att slutföra begäran.

### <a name="resolution"></a>Lösning
* Stoppa alla virtuella datorer i tillgänglighetsuppsättningen och starta sedan om varje virtuell dator.
  
  1. Klicka på **resursgrupper** > *resursgruppen* > **resurser** > *din tillgänglighetsuppsättning*  >  **Virtuella datorer** > *din virtuella dator* > **stoppa**.
  2. När alla virtuella datorer har slutat, väljer du stoppade virtuella datorer och klicka på Start.
* Försöka starta om vid ett senare tillfälle.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Ett fel inträffade när du ändrar storlek på en befintlig virtuell dator
Försök att ändra storlek på en befintlig virtuell dator men får ett Allokeringsfel.

### <a name="cause"></a>Orsak
Förfrågan om att ändra storlek på den virtuella datorn måste göras i det ursprungliga klustret som är värd för Molntjänsten. Klustret stöder dock inte den begärda VM-storleken.

### <a name="resolution"></a>Lösning
* Gör om begäran med en mindre VM-storlek.
* Om storleken på den begärda virtuella datorn inte kan ändras:
  
  1. Stoppa alla virtuella datorer i tillgänglighetsuppsättningen.
     
     * Klicka på **resursgrupper** > *resursgruppen* > **resurser** > *din tillgänglighetsuppsättning*  >  **Virtuella datorer** > *din virtuella dator* > **stoppa**.
  2. När alla virtuella datorer kan du ändra storlek på den virtuella datorn till en större storlek.
  3. Välj den ändrade virtuella datorn och klicka på **starta**, och starta sedan var och en av stoppade virtuella datorer.

## <a name="next-steps"></a>Nästa steg
Om det uppstår problem när du skapar en ny virtuell Windows-dator i Azure finns i [felsöka distributionsproblem med att skapa en ny Windows virtuell dator i Azure](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

