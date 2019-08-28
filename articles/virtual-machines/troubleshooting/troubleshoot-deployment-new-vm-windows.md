---
title: Felsöka distribution av virtuella Windows-datorer i Azure | Microsoft Docs
description: Felsök distributions problem i Resource Manager när du skapar en ny virtuell Windows-dator i Azure
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
ms.openlocfilehash: 6dbe4c1533aecfab4a62ce3ad90b694c0c00f4b6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103491"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Felsöka distributions problem när du skapar en ny virtuell Windows-dator i Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Främsta problem
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Andra problem och frågor om distribution av virtuella datorer finns i [Felsöka distribution av problem med virtuella Windows-datorer i Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Samla in aktivitets loggar
För att starta fel sökningen samlar du in aktivitets loggarna för att identifiera det fel som är associerat med problemet. Följande länkar innehåller detaljerad information om processen som ska följas.

[Visa distributionsåtgärder](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Visa aktivitets loggar för att hantera Azure-resurser](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**JA** Om operativ systemet är Windows generaliserat och har laddats upp och/eller registrerats med den generaliserade inställningen, kommer det inte att finnas några fel. Om operativ systemet är Windows specialiserat, och laddas upp och/eller fångas med den särskilda inställningen, kommer det inte att finnas några fel.

**Överförings fel:**

**N<sup>1</sup>:** Om operativ systemet är Windows generaliserat och det har laddats upp som specialiserat, får du ett timeout-fel för etablering med den virtuella dator som fastnat på OOBE-skärmen.

**N<sup>2</sup>:** Om operativ systemet är Windows specialiserat och det laddas upp som generaliserat får du ett fel meddelande om etablering med den virtuella datorn som fastnat på OOBE-skärmen eftersom den nya virtuella datorn körs med det ursprungliga dator namnet, användar namnet och lösen ordet.

**Lösning**

För att lösa de här felen använder du [Add-AzVhd för att överföra den ursprungliga virtuella hård disken](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), som är tillgänglig lokalt, med samma inställning som för operativ systemet (generaliserad/specialiserad). Kom ihåg att köra Sysprep först för att ladda upp som generaliserad.

**Infångnings fel:**

**N<sup>3</sup>:** Om operativ systemet är Windows generaliserat och det har fångats som specialiserat får du ett timeout-fel för etableringen eftersom den ursprungliga virtuella datorn inte kan användas eftersom den är markerad som generaliserad.

**N<sup>4</sup>:** Om operativ systemet är Windows specialiserat, och det har registrerats som generaliserat, får du ett fel meddelande om etableringen, eftersom den nya virtuella datorn körs med det ursprungliga dator namnet, användar namnet och lösen ordet. Dessutom går det inte att använda den ursprungliga virtuella datorn eftersom den är markerad som specialiserad.

**Lösning**

Du kan lösa båda dessa fel genom att ta bort den aktuella avbildningen från portalen och [avbilda om den från de aktuella virtuella hård diskarna](../windows/create-vm-specialized.md) med samma inställning som för operativ systemet (generaliserat/specialiserat).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Ärende: Anpassad/Galleri/Marketplace-avbildning; allokeringsfel
Det här felet uppstår i situationer när den nya VM-begäran fästs i ett kluster som antingen inte stöder den virtuella dator storleken eller som inte har tillräckligt med ledigt utrymme för att hantera begäran.

**Orsak 1:** Klustret har inte stöd för den begärda virtuella dator storleken.

**Lösning 1:**

* Gör om begäran med en mindre VM-storlek.
* Om det inte går att ändra storleken på den begärda virtuella datorn:
  * Stoppa alla virtuella datorer i tillgänglighets uppsättningen.
    Klicka på **resurs grupper** > *resurs gruppen* > **resurser** > dintillgänglighetsuppsättningVirtual Machinesden > virtuelladatorn > *Stoppa.*  > 
  * När alla virtuella datorer har stoppats skapar du den nya virtuella datorn i önskad storlek.
  * Starta den nya virtuella datorn först och välj sedan var och en av de stoppade virtuella datorerna och klicka på **Starta**.

**Orsak 2:** Det finns inga lediga resurser för klustret.

**Lösning 2:**

* Gör om begäran vid ett senare tillfälle.
* Om den nya virtuella datorn kan ingå i en annan tillgänglighets uppsättning
  * Skapa en ny virtuell dator i en annan tillgänglighets uppsättning (i samma region).
  * Lägg till den nya virtuella datorn i samma virtuella nätverk.

## <a name="next-steps"></a>Nästa steg
Om du får problem när du startar en virtuell Windows-dator som har stoppats eller ändrat storlek på en befintlig virtuell Windows-dator i Azure läser du [Felsöka distributions problem i Resource Manager med att starta om eller ändra storlek på en befintlig virtuell Windows-dator i Azure](restart-resize-error-troubleshooting.md).


