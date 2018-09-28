---
title: Felsöka Linux VM-distribution | Microsoft Docs
description: Felsöka distributionsproblem i Resource Manager när du skapar en ny virtuell Linux-dator i Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: 08009ca7f9faaa75e593670c22cf864c12236e8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414710"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Felsöka distributionsproblem i Resource Manager med att skapa en ny virtuell Linux-dator i Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>De vanligaste problemen
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Andra problem med VM-distribution och frågor finns i [felsöka distribution Linux VM-problem i Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Samla in aktivitetsloggar
Starta felsökning genom att samla in aktivitetsloggar för att identifiera felet som är associerade med problemet. Följande länkar innehåller detaljerad information om processen för att följa.

[Visa distributionsåtgärder](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Visa aktivitetsloggar för att hantera Azure-resurser](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** om Operativsystemet är generaliserad, Linux och den är laddat upp och/eller med inställningen generaliserad, så det finns inte några fel. På samma sätt om Operativsystemet är Linux specialiserade och det är laddat upp och/eller med inställningen specialiserade och det inte finns några fel.

**Uppladdningsfel:**

**N<sup>1</sup>:** om Operativsystemet är generaliserad Linux och den har laddats upp som specialiserade, får du ett etablering tidsgränsfel eftersom den virtuella datorn har fastnat på scenen för etablering.

**N<sup>2</sup>:** om Operativsystemet är Linux specialiserade och den har laddats upp som generaliserad, visas en allokering fel eftersom den nya virtuella datorn körs med det ursprungliga datornamn, användarnamn och lösenord.

**Lösning:**

Ladda upp den ursprungliga virtuella Hårddisken finns tillgängliga lokalt, med samma inställning som för Operativsystemet (generaliserad/specialiserade) för att lösa båda dessa fel. Om du vill överföra som generaliserad, Kom ihåg att köra - avetablera först.

**Samla in fel:**

**N<sup>3</sup>:** om Operativsystemet är generaliserad Linux och den är klar som specialiserade, du får ett etablering tidsgränsfel eftersom den ursprungliga virtuella datorn inte kan användas eftersom den har markerats som generaliserad.

**N<sup>4</sup>:** om Operativsystemet är Linux specialiserade och avbildningen som generaliserad, visas en allokering fel eftersom den nya virtuella datorn körs med det ursprungliga datornamn, användarnamn och lösenord. Dessutom ursprungligt virtuella datorn kan inte användas eftersom den är markerad som specialiserad.

**Lösning:**

Lös båda de här felen genom att ta bort den nuvarande avbildningen från portalen och [avbilda från de aktuella virtuella hårddiskarna](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) med samma inställning som för Operativsystemet (generaliserad/specialiserade).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Anpassade / galleriet / marketplace-avbildning Allokeringsfel
Det här felet uppstår i situationer när den nya VM-förfrågan är fäst på ett kluster som antingen har inte stöd för VM-storlek som begärts eller har inte tillgängligt ledigt utrymme för att hantera begäran.

**Orsak 1:** klustret inte stöd för den begärda VM-storleken.

**Lösning 1:**

* Gör om begäran med en mindre VM-storlek.
* Om storleken på den begärda virtuella datorn inte kan ändras:
  * Stoppa alla virtuella datorer i tillgänglighetsuppsättningen.
    Klicka på **resursgrupper** > *resursgruppen* > **resurser** > *din tillgänglighetsuppsättning*  >  **Virtuella datorer** > *din virtuella dator* > **stoppa**.
  * När alla virtuella datorer kan du skapa den nya virtuella datorn i önskad storlek.
  * Starta den nya virtuella datorn först och sedan väljer du stoppade virtuella datorer och klicka på **starta**.

**Orsak 2:** klustret inte har kostnadsfria resurser.

**Lösning 2:**

* Försök begäran vid ett senare tillfälle.
* Om den nya virtuella datorn kan vara en del av en annan tillgänglighetsuppsättning
  * Skapa en ny virtuell dator i en annan tillgänglighetsuppsättning (i samma region).
  * Lägg till den nya virtuella datorn i samma virtuella nätverk.

## <a name="next-steps"></a>Nästa steg
Om det uppstår problem när du startar en stoppad Linux-VM eller ändra storlek på en befintlig Linux VM i Azure finns i [felsöka Resource Manager distributionsproblem med att starta om eller ändra storlek på en befintlig Linux virtuell dator i Azure](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

