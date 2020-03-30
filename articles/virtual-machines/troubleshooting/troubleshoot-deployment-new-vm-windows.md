---
title: Felsöka Windows VM-distribution i Azure | Microsoft-dokument
description: Felsöka distributionsproblem i Resource Manager när du skapar en ny virtuell Windows-dator i Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0bc363b87a9f5b2f013c0bae75a07d79a3a7a830
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981392"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Felsöka distributionsproblem när du skapar en ny Windows-virtuell dator i Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>De vanligaste problemen
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Andra problem och frågor om vm-distribution finns i [Felsöka distribution av Windows-problem med virtuella datorer i Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Samla in aktivitetsloggar
Om du vill börja felsöka samlar du in aktivitetsloggarna för att identifiera felet som är associerat med problemet. Följande länkar innehåller detaljerad information om processen att följa.

[Visa distributionsåtgärder](../../azure-resource-manager/templates/deployment-history.md)

[Visa aktivitetsloggar för att hantera Azure-resurser](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Om operativsystemet är Windows generaliserat, och det laddas upp och / eller fångas med den generaliserade inställningen, då kommer det inte att finnas några fel. På samma sätt, om operativsystemet är Windows-specialiserade, och det laddas upp och / eller fångas med den specialiserade inställningen, då kommer det inte att finnas några fel.

**Ladda upp fel:**

**N<sup>1</sup>:** Om operativsystemet är Windows generaliserat, och det laddas upp som specialiserat, får du en etablering timeout fel med den virtuella datorn fastnat på OOBE-skärmen.

**N<sup>2:</sup>** Om operativsystemet är Windows-specialiserat, och det laddas upp som generaliserat, får du ett etableringsfelfel med den virtuella datorn som fastnat på OOBE-skärmen eftersom den nya virtuella datorn körs med det ursprungliga datornamnet, användarnamnet och lösenordet.

**Upplösning**

För att lösa båda dessa fel, använd [Add-AzVhd för att ladda upp den ursprungliga virtuella hårddisken](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), tillgänglig lokalt, med samma inställning som för operativsystemet (generaliserad /specialiserad). Kom ihåg att köra sysprep först om du vill ladda upp som generaliserad.

**Capture-fel:**

**N<sup>3:</sup>** Om operativsystemet är Windows generaliserat och det fångas som specialiserat, får du ett etableringstidsfel eftersom den ursprungliga virtuella datorn inte kan kan kan kanseras eftersom den markeras som generaliserad.

**N<sup>4</sup>:** Om operativsystemet är Windows-specialiserat och det fångas som generaliserat, får du ett etableringsfelfel eftersom den nya virtuella datorn körs med det ursprungliga datornamnet, användarnamnet och lösenordet. Den ursprungliga virtuella datorn kan inte heller kan kan kan tas ut eftersom den är markerad som specialiserad.

**Upplösning**

Om du vill lösa båda dessa fel tar du bort den aktuella bilden från portalen och [återtar den från de aktuella virtuella hårddiskarna](../windows/create-vm-specialized.md) med samma inställning som för operativsystemet (generaliserad/specialiserad).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problem: Anpassad/galleri/marknadsplatsbild; allokeringsfel
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
Om du stöter på problem när du startar en stoppad Windows VM eller ändrar storlek på en befintlig Windows-virtuell dator i Azure läser [du Felsöka distributionsproblem i Resource Manager med att starta om eller ändra storlek på en befintlig Windows Virtual Machine i Azure](restart-resize-error-troubleshooting.md).


