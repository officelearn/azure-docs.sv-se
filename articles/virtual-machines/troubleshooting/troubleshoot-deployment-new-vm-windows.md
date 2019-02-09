---
title: Felsöka distribution av Windows virtuella datorer i Azure | Microsoft Docs
description: Felsöka distributionsproblem i Resource Manager när du skapar en ny Windows virtuell dator i Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8f18fbabe52c9170cde70900933ce96a3a6400c7
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984349"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Felsöka distributionsproblem när du skapar en ny virtuell Windows-dator i Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>De vanligaste problemen
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Andra problem med VM-distribution och frågor finns i [felsöka distribuera Windows VM-problem i Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Samla in aktivitetsloggar
Starta felsökning genom att samla in aktivitetsloggar för att identifiera felet som är associerade med problemet. Följande länkar innehåller detaljerad information om processen för att följa.

[Visa distributionsåtgärder](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Visa aktivitetsloggar för att hantera Azure-resurser](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Om Operativsystemet är generaliserad Windows, och det är laddat upp och/eller med generaliserad inställningen, kommer finnas det inte några fel. På samma sätt om Operativsystemet är Windows specialiserade och det är laddat upp och/eller med inställningen specialiserade och det inte finns några fel.

**Uppladdningsfel:**

**N<sup>1</sup>:** Om Operativsystemet är generaliserad Windows och den har laddats upp som specialiserade, du får ett etablering timeout-fel med den virtuella datorn som fastnat på OOBE-skärmen.

**N<sup>2</sup>:** Om Operativsystemet är Windows specialiserade och den har laddats upp som generaliserad, får du en allokering fel med den virtuella datorn som fastnat på skärmen OOBE eftersom den nya virtuella datorn körs med det ursprungliga datornamn, användarnamn och lösenord.

**Lösning**

Lös båda de här felen med [Add-AzVhd för att överföra den ursprungliga virtuella Hårddisken](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), tillgängligt lokalt, med samma inställning som för Operativsystemet (generaliserad/specialiserade). Kom ihåg att köra sysprep först om du vill överföra som generaliserad.

**Samla in fel:**

**N<sup>3</sup>:** Om Operativsystemet är generaliserad Windows och avbildningen som specialiserade, du får ett etablering tidsgränsfel eftersom den ursprungliga virtuella datorn inte kan användas eftersom den har markerats som generaliserad.

**N<sup>4</sup>:** Om Operativsystemet är Windows specialiserade och avbildningen som generaliserad, får du en allokering fel eftersom den nya virtuella datorn körs med den ursprungliga datornamn, användarnamn och lösenord. Dessutom ursprungligt virtuella datorn kan inte användas eftersom den är markerad som specialiserad.

**Lösning**

Lös båda de här felen genom att ta bort den nuvarande avbildningen från portalen och [avbilda från de aktuella virtuella hårddiskarna](../windows/create-vm-specialized.md) med samma inställning som för Operativsystemet (generaliserad/specialiserade).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Ärende: Marketplace-anpassad/galleriet bilden. Allokeringsfel
Det här felet uppstår i situationer när den nya VM-förfrågan är fäst på ett kluster som antingen har inte stöd för VM-storlek som begärts eller har inte tillgängligt ledigt utrymme för att hantera begäran.

**Orsak 1:** Klustret stöder inte den begärda VM-storleken.

**Lösning 1:**

* Gör om begäran med en mindre VM-storlek.
* Om storleken på den begärda virtuella datorn inte kan ändras:
  * Stoppa alla virtuella datorer i tillgänglighetsuppsättningen.
    Klicka på **resursgrupper** > *resursgruppen* > **resurser** > *din tillgänglighetsuppsättning*  >  **Virtuella datorer** > *din virtuella dator* > **stoppa**.
  * När alla virtuella datorer kan du skapa den nya virtuella datorn i önskad storlek.
  * Starta den nya virtuella datorn först och sedan väljer du stoppade virtuella datorer och klicka på **starta**.

**Orsak 2:** Klustret har inte kostnadsfria resurser.

**Lösning 2:**

* Försök begäran vid ett senare tillfälle.
* Om den nya virtuella datorn kan vara en del av en annan tillgänglighetsuppsättning
  * Skapa en ny virtuell dator i en annan tillgänglighetsuppsättning (i samma region).
  * Lägg till den nya virtuella datorn i samma virtuella nätverk.

## <a name="next-steps"></a>Nästa steg
Om det uppstår problem när du startar en stoppad virtuell Windows-dator eller ändra storlek på en befintlig Windows-dator i Azure finns i [felsöka Resource Manager distributionsproblem med att starta om eller ändra storlek på en befintlig Windows virtuell dator i Azure](restart-resize-error-troubleshooting.md).


