---
title: Felsöka distribution av problem med virtuella Linux-datorer i Azure | Microsoft-dokument
description: Felsöka distribution av problem med virtuella Linux-datorer i distributionsmodellen för Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 5a8a58a3935b7cd5efb8565f7e3278ccaae4f4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921444"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Felsöka problem med distributionen av virtuella Linux-datorer i Azure

Om du vill felsöka problem med distribution av virtuella datorer (VM) i Azure läser du de [vanligaste problemen](#top-issues) för vanliga fel och lösningar.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.

## <a name="top-issues"></a>De vanligaste problemen
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Klustret kan inte stödja den begärda vm-storleken
\<egenskaper supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Försök igen med en mindre vm-storlek.
- Om storleken på den begärda virtuella datorn inte kan ändras:
    - Stoppa alla virtuella datorer i tillgänglighetsuppsättningen. Klicka på **Resursgrupper** > resursgruppen > **resurser** > tillgänglighetsuppsättningen > **virtuella datorer** > din virtuella dator > **Stopp.**
    - När alla virtuella datorer har stannat skapar du den virtuella datorn i önskad storlek.
    - Starta den nya virtuella datorn först och välj sedan var och en av de stoppade virtuella datorerna och klicka på Start.


## <a name="the-cluster-does-not-have-free-resources"></a>Klustret har inga lediga resurser
\<egenskaper supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Försök igen begäran senare.
- Om den nya virtuella datorn kan ingå i en annan tillgänglighetsuppsättning
    - Skapa en virtuell dator i en annan tillgänglighetsuppsättning (i samma region).
    - Lägg till den nya virtuella datorn i samma virtuella nätverk.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hur aktiverar jag min månatliga kredit för Visual studio Enterprise (BizSpark)

Om du vill aktivera din månatliga kredit läser du den här [artikeln](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Varför kan jag inte installera GPU-drivrutinen för en Ubuntu NV VM?

För närvarande är Linux GPU-stöd endast tillgängligt på virtuella Azure NC-datorer som kör Ubuntu Server 16.04 LTS. Mer information finns i [Konfigurera GPU-drivrutiner för virtuella datorer i N-serien som kör Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Mina drivrutiner saknas för min Virtuella Linux N-serien VM

Drivrutiner för Linux-baserade virtuella datorer finns [här](../linux/n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Jag kan inte hitta en GPU-instans i min virtuella dator i N-serien

Om du vill dra nytta av GPU-funktionerna i virtuella azure N-seriens virtuella datorer som kör Windows Server 2016 eller Windows Server 2012 R2 måste du installera NVIDIA-grafikdrivrutiner på varje virtuell dator efter distributionen. Information om drivrutinsinställningar är tillgänglig för [virtuella windows-datorer](../windows/n-series-driver-setup.md) och [virtuella Linux-datorer](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Är virtuella datorer i N-serien tillgängliga i min region?

Du kan kontrollera tillgängligheten från [tabellen Produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services)och priser [här](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Jag kan inte se vm-storlek familj som jag vill när ändra storlek på min virtuella dator.

När en virtuell dator körs distribueras den till en fysisk server. De fysiska servrarna i Azure-regioner är grupperade i kluster av gemensam fysisk maskinvara. Att ändra storlek på en virtuell dator som kräver att den virtuella datorn flyttas till olika maskinvarukluster är olika beroende på vilken distributionsmodell som användes för att distribuera den virtuella datorn.

- Virtuella datorer som distribueras i den klassiska distributionsmodellen måste molntjänstdistributionen tas bort och distribueras om för att ändra de virtuella datorerna till en storlek i en annan storleksfamilj.

- Virtuella datorer som distribueras i Resource Manager-distributionsmodellen måste du stoppa alla virtuella datorer i tillgänglighetsuppsättningen innan du ändrar storleken på en virtuell dator i tillgänglighetsuppsättningen.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Den listade vm-storleken stöds inte vid distribution i tillgänglighetsuppsättning.

Välj en storlek som stöds i tillgänglighetsuppsättningens kluster. Vi rekommenderar att du skapar en tillgänglighetsuppsättning för att välja den största vm-storleken du tror att du behöver och har den första distributionen till tillgänglighetsuppsättningen.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Vilka Linux-distributioner/-versioner stöds på Azure?

Du hittar listan på Linux på [Azure-Signerade distributioner](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig klassisk virtuell dator i en tillgänglighetsuppsättning?

Ja. Du kan lägga till en befintlig klassisk virtuell dator i en ny eller befintlig tillgänglighetsuppsättning. Mer information finns i [Lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/).

Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
