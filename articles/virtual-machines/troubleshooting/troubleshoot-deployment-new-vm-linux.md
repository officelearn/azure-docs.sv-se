---
title: Felsöka distribution av virtuella linux-datorer| Microsoft-dokument
description: Felsöka distributionsproblem i Resource Manager när du skapar en ny virtuell Linux-dator i Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 98c3a6b14230e30ccbb103be741595696a20c236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981409"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Felsöka distributionsproblem för Resource Manager med att skapa en ny virtuell Linux-dator i Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>De vanligaste problemen
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Information om andra problem med VM-distribution samt frågor finns i [Felsöka problem med distributionen av virtuella Linux-datorer i Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Samla in aktivitetsloggar
Om du vill börja felsöka samlar du in aktivitetsloggarna för att identifiera felet som är associerat med problemet. Följande länkar innehåller detaljerad information om processen att följa.

[Visa distributionsåtgärder](../../azure-resource-manager/templates/deployment-history.md)

[Visa aktivitetsloggar för att hantera Azure-resurser](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Om operativsystemet är Linux generaliserat, och det laddas upp och / eller fångas med den generaliserade inställningen, då kommer det inte att finnas några fel. På samma sätt, om operativsystemet är Linux-specialiserad, och det laddas upp och / eller fångas med den specialiserade inställningen, då kommer det inte att finnas några fel.

**Ladda upp fel:**

**N<sup>1</sup>:** Om operativsystemet är Linux generaliserat och det överförs som specialiserat, får du ett etableringstidsfel eftersom den virtuella datorn har fastnat i etableringsfasen.

**N<sup>2:</sup>** Om operativsystemet är Linux-specialiserat, och det laddas upp som generaliserat, får du ett etableringsfelfel eftersom den nya virtuella datorn körs med det ursprungliga datornamnet, användarnamnet och lösenordet.

**Upplösning:**

För att lösa båda dessa fel, ladda upp den ursprungliga virtuella hårddisken, tillgänglig lokalt, med samma inställning som för operativsystemet (generaliserad / specialiserad). Om du vill ladda upp som generaliserad, kom ihåg att köra -deetavision först.

**Capture-fel:**

**N<sup>3:</sup>** Om operativsystemet är Linux generaliserat och det fångas som specialiserat, får du ett etableringstidsfel eftersom den ursprungliga virtuella datorn inte kan kan kanseras eftersom den markeras som generaliserad.

**N<sup>4</sup>:** Om operativsystemet är Linux-specialiserat, och det fångas som generaliserat, får du ett etableringsfelfel eftersom den nya virtuella datorn körs med det ursprungliga datornamnet, användarnamnet och lösenordet. Den ursprungliga virtuella datorn kan inte heller kan kan kan tas ut eftersom den är markerad som specialiserad.

**Upplösning:**

Om du vill lösa båda dessa fel tar du bort den aktuella bilden från portalen och [återtar den från de aktuella virtuella hårddiskarna](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) med samma inställning som för operativsystemet (generaliserad/specialiserad).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Anpassad/ galleri / marknadsplats bild; allokeringsfel
Det här felet uppstår i situationer när den nya vm-begäran är fäst vid ett kluster som antingen inte kan stödja den vm-storlek som begärs eller inte har tillgängligt ledigt utrymme för att hantera begäran.

**Orsak 1:** Klustret kan inte stödja den begärda vm-storleken.

**Lösning 1:**

* Försök igen med en mindre vm-storlek.
* Om storleken på den begärda virtuella datorn inte kan ändras:
  * Stoppa alla virtuella datorer i tillgänglighetsuppsättningen.
    Klicka på **Resursgrupper** > *din resursgrupp* > **Resurser** > som din tillgänglighet*anger* > **virtuella datorer** > *för din virtuella dator* > **Stop**.
  * När alla virtuella datorer har stannat skapar du den nya virtuella datorn i önskad storlek.
  * Starta den nya virtuella datorn först och välj sedan var och en av de stoppade virtuella datorerna och klicka på **Start**.

**Orsak 2:** Klustret har inga lediga resurser.

**Lösning 2:**

* Försök igen begäran vid ett senare tillfälle.
* Om den nya virtuella datorn kan ingå i en annan tillgänglighetsuppsättning
  * Skapa en ny virtuell dator i en annan tillgänglighetsuppsättning (i samma region).
  * Lägg till den nya virtuella datorn i samma virtuella nätverk.

## <a name="next-steps"></a>Nästa steg
Om du stöter på problem när du startar en stoppad Virtuell Linux-dator eller ändrar storlek på en befintlig Linux-virtuell dator i Azure läser [du Felsöka distributionsproblem i Resource Manager med omstart eller storleksändring av en befintlig Virtuell Linux-dator i Azure](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

