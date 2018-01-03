---
title: "Felsöka Linux VM distribution-RM | Microsoft Docs"
description: "Felsökning av distributionsproblem med Resource Manager när du skapar en ny virtuell Linux-dator i Azure"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: aea5db05843b0175b8ef8b713944e12262e33010
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Felsökning av Resource Manager distributionsproblem med att skapa en ny virtuell Linux-dator i Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>De främsta problemen
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Andra Virtuella distributionsproblem och frågor finns i [felsöka distribuera virtuella Linux-problem i Azure](troubleshoot-deploy-vm.md).
## <a name="collect-activity-logs"></a>Samla in aktiviteten loggar
Starta felsökning genom att samla in aktivitetsloggar för att identifiera fel som är associerade med problemet. Följande länkar innehåller detaljerad information om processen för att följa.

[Visa distributionsåtgärder](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Visa aktivitetsloggar att hantera Azure-resurser](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** om Operativsystemet är Linux generaliserad, och det är upp och/eller avbildas med inställningen generaliserad och det inte finns några fel. På samma sätt om Operativsystemet Linux specialiserade, den är upp och/eller avbildas med inställningen specialiserade och det inte finns några fel.

**Överför fel:**

**N<sup>1</sup>:** om Operativsystemet är Linux generaliserad och det överförs som specialiserade, du får en allokering tidsgränsfel eftersom den virtuella datorn har fastnat på läget etablering.

**N<sup>2</sup>:** om Operativsystemet Linux specialiserade och det överförs som generaliserad du får felet etablering misslyckades eftersom den nya virtuella datorn körs med ursprungliga datornamn, användarnamn och lösenord.

**Lösning:**

Överför den ursprungliga virtuella Hårddisken tillgänglig lokalt, med samma inställning som för OS (generaliserad/specialiserade) för att lösa båda dessa fel. Om du vill överföra som generaliserad ihåg att köra - ta bort etableringen först.

**Avbilda fel:**

**N<sup>3</sup>:** om Operativsystemet är Linux generaliserad och avbildas som specialiserade, du får en allokering tidsgränsfel eftersom den ursprungliga virtuella datorn inte kan användas eftersom den har markerats som generaliserad.

**N<sup>4</sup>:** om Operativsystemet Linux specialiserade och avbildningen som generaliserad du får felet etablering misslyckades eftersom den nya virtuella datorn körs med ursprungliga datornamn, användarnamn och lösenord. Dessutom ursprungligt virtuella datorn kan inte användas eftersom den har markerats som särskilda.

**Lösning:**

Ta bort den nuvarande avbildningen från portalen för att lösa båda dessa fel och [avbilda från de aktuella virtuella hårddiskarna](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) med samma inställning som för OS (generaliserad/specialiserade).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Anpassad / galleriet / marketplace-avbildning; Allokeringsfel
Det här felet uppstår i situationer när den nya VM-begäranden är fäst på ett kluster som stöder inte den begärda VM-storlek eller saknar lediga utrymmet för begäran.

**Orsak 1:** klustret stöder inte den begärda VM-storleken.

**Lösning 1:**

* Försök igen med begäran med en mindre VM-storlek.
* Om storleken på den begärda virtuella datorn inte kan ändras:
  * Stoppa alla virtuella datorer i tillgänglighetsuppsättningen.
    Klicka på **resursgrupper** > *resursgruppen* > **resurser** > *din tillgänglighetsuppsättning* > **virtuella datorer** > *din virtuella dator* > **stoppa**.
  * När alla virtuella datorer har slutat, kan du skapa den nya virtuella datorn i önskad storlek.
  * Starta den nya virtuella datorn först och sedan markera varje stoppad virtuell dator och klicka på **starta**.

**Orsak 2:** klustret har inte frigöra resurser.

**Lösning 2:**

* Försök begäran vid ett senare tillfälle.
* Om den nya virtuella datorn kan vara en del av en annan tillgänglighetsuppsättning
  * Skapa en ny virtuell dator i en annan tillgänglighetsuppsättning (i samma region).
  * Lägg till ny virtuell dator i samma virtuella nätverk.

## <a name="next-steps"></a>Nästa steg
Om du får problem när du startar en stoppad Linux VM eller ändra storlek på en befintlig Linux VM i Azure, se [felsöka Resource Manager distributionsproblem med att starta om eller ändrar storlek på en befintlig virtuell Linux-dator i Azure](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

