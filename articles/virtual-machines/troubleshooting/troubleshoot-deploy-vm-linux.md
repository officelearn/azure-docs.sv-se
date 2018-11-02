---
title: Felsöka distribution Linux VM-problem i Azure | Microsoft Docs
description: Felsöka distribution Linux VM-problem i Azurethe Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 45e15fa5b17f75dcc6cfdc7305861d38804f1266
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748144"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Felsöka distribution Linux VM-problem i Azure

Felsök distributionsproblem för virtuell dator (VM) i Azure genom att granska den [de främsta problemen](#top-issues) för vanliga fel och lösningar.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.

## <a name="top-issues"></a>De vanligaste problemen
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Klustret kan inte stöd för den begärda storleken för virtuell dator
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Gör om begäran med en mindre VM-storlek.
- Om storleken på den begärda virtuella datorn inte kan ändras:
    - Stoppa alla virtuella datorer i tillgänglighetsuppsättningen. Klicka på **resursgrupper** > din resursgrupp > **resurser** > tillgänglighetsuppsättningen > **virtuella datorer** > den virtuella datorn >  **Stoppa**.
    - När alla virtuella datorer kan du skapa den virtuella datorn i önskad storlek.
    - Starta den nya virtuella datorn först och väljer du stoppade virtuella datorer och klicka på Start.


## <a name="the-cluster-does-not-have-free-resources"></a>Klustret har inte kostnadsfria resurser
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Försöka senare.
- Om den nya virtuella datorn kan vara en del av en annan tillgänglighetsuppsättning
    - Skapa en virtuell dator i en annan tillgänglighetsuppsättning (i samma region).
    - Lägg till den nya virtuella datorn i samma virtuella nätverk.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hur aktiverar jag min månatliga kredit för Visual studio Enterprise (BizSpark)

Om du vill aktivera din månatliga kredit finns i den här [artikeln](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Varför kan jag inte installera GPU-drivrutinen för en virtuell dator NV Ubuntu?

Linux-GPU-stöd är för närvarande bara tillgängligt på Azure Nätverksstyrenhetens virtuella datorer som kör Ubuntu Server 16.04 LTS. Mer information finns i [konfigurera GPU-drivrutiner för virtuella datorer i N-serien som kör Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Drivrutinerna saknas för min virtuella dator Linux-N-serien

Drivrutiner för Linux-baserade virtuella datorer är placerade [här](../linux/n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Jag kan inte hitta en GPU-instans i N-serien virtuella datorn

Om du vill dra nytta av GPU-funktionerna i Azure N-serien virtuella datorer som kör Windows Server 2016 eller Windows Server 2012 R2, måste du installera NVIDIA-grafikdrivrutiner på varje virtuell dator efter distributionen. Inställningsinformation för drivrutinen är tillgänglig för [Windows VMs](../windows/n-series-driver-setup.md) och [virtuella Linux-datorer](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Är virtuella datorer i N-serien i min region?

Du kan kontrollera tillgänglighet från den [produkttillgänglighet per region tabell](https://azure.microsoft.com/regions/services), och priser [här](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Jag kan inte se VM-storleksfamilj som jag vill ha när du ändrar storlek på den virtuella datorn.

Om en virtuell dator är igång kan distribueras den till en fysisk server. De fysiska servrarna i Azure-regioner är grupperade i kluster av vanliga fysisk maskinvara. Ändra storlek på en virtuell dator som kräver att den virtuella datorn flyttas till annan maskinvarukluster är olika beroende på vilken distributionsmodell som användes för att distribuera den virtuella datorn.

- Virtuella datorer som distribuerats i klassiska distributionsmodellen, molntjänstdistribution måste tas bort och distribueras om för att ändra de virtuella datorerna till en storlek i en annan storleksfamilj.

- Virtuella datorer som distribueras i Resource Manager-distributionsmodellen, måste du stoppa alla virtuella datorer i tillgänglighetsuppsättningen innan du ändrar storleken på virtuella datorer i tillgänglighetsuppsättningen.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Den angivna VM-storleken stöds inte när du distribuerar i Tillgänglighetsuppsättningen.

Välj en storlek som stöds på klustret i tillgänglighetsuppsättningen. Du rekommenderas när du skapar en tillgänglighetsuppsättning för att välja den största VM-storlek du tror att du behöver och har som första distributionen i tillgänglighetsuppsättningen.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Vilka Linux-distributioner /-versioner som stöds på Azure?

Du hittar listan på Linux på [Azure-godkända distributioner](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig klassisk virtuell dator till en tillgänglighetsuppsättning?

Ja. Du kan lägga till en befintlig klassisk virtuell dator till en ny eller befintlig Tillgänglighetsuppsättning. Mer information finns i [lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning](../windows/classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/).

Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
