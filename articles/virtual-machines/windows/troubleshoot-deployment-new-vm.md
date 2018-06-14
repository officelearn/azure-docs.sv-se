---
title: Felsöka distributionen av Windows VM i Azure | Microsoft Docs
description: Felsökning av distributionsproblem med Resource Manager när du skapar en ny Windows virtuell dator i Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fff29f6cfed4989386ca5bbd12184dce525add76
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
ms.locfileid: "27580379"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Felsöka distributionsproblem när du skapar en ny Windows virtuell dator i Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>De främsta problemen
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Andra Virtuella distributionsproblem och frågor finns i [felsöka distribution problem med Windows virtuell dator i Azure](troubleshoot-deploy-vm.md).

## <a name="collect-activity-logs"></a>Samla in aktiviteten loggar
Starta felsökning genom att samla in aktivitetsloggar för att identifiera fel som är associerade med problemet. Följande länkar innehåller detaljerad information om processen för att följa.

[Visa distributionsåtgärder](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Visa aktivitetsloggar att hantera Azure-resurser](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** om Operativsystemet är Windows generaliserad, och det är upp och/eller avbildas med inställningen generaliserad och det inte finns några fel. På samma sätt om Operativsystemet är Windows specialiserade, och den är upp och/eller avbildas med inställningen för särskilda och det inte finns några fel.

**Överför fel:**

**N<sup>1</sup>:** om Operativsystemet är Windows generaliserad och det överförs som specialiserade, du får ett etablering timeout-fel med den virtuella datorn som fastnat på skärmen OOBE.

**N<sup>2</sup>:** om Operativsystemet är Windows specialiserade och det överförs som generaliserad, du får felet etablering fel med den virtuella datorn som fastnat på skärmen OOBE eftersom den nya virtuella datorn körs med det ursprungliga datornamnet, användarnamn och lösenord.

**Lösning**

Så här löser du båda dessa fel [Add-AzureRmVhd för att överföra den ursprungliga virtuella Hårddisken](https://msdn.microsoft.com/library/mt603554.aspx), tillgänglig lokalt, med samma inställning som för OS (generaliserad/specialiserade). Kom ihåg att köra sysprep först om du vill överföra som generaliserad.

**Avbilda fel:**

**N<sup>3</sup>:** om Operativsystemet är Windows generaliserad och avbildas som specialiserade, du får en allokering tidsgränsfel eftersom den ursprungliga virtuella datorn inte kan användas eftersom den har markerats som generaliserad.

**N<sup>4</sup>:** om Operativsystemet är Windows specialiserade och avbildningen som generaliserad, får du en allokering felmeddelande eftersom den nya virtuella datorn körs med ursprungliga datornamn, användarnamn och lösenord. Dessutom ursprungligt virtuella datorn kan inte användas eftersom den har markerats som särskilda.

**Lösning**

Ta bort den nuvarande avbildningen från portalen för att lösa båda dessa fel och [avbilda från de aktuella virtuella hårddiskarna](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) med samma inställning som för OS (generaliserad/specialiserade).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problem: Marketplace-anpassad/galleriet bilden. Allokeringsfel
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
Om du får problem när du startar en stoppad virtuell Windows-dator eller ändra storlek på en befintlig Windows virtuell dator i Azure, se [felsöka Resource Manager distributionsproblem med att starta om eller ändrar storlek på en befintlig Windows virtuell dator i Azure](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

