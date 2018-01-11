---
title: "Felsöka distribuera virtuella Linux-problem i Azure | Microsoft Docs"
description: "Felsöka distribuera Linux virtuella problem i Azurethe Resource Manager-distributionsmodellen."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 9a28d0573dd71238d0d91436cb49776b75b51a36
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Felsöka distribuera virtuella Linux-problem i Azure

Om du vill felsöka distributionsproblem för virtuell dator (VM) i Azure, granska den [vanliga problem](#top-issues) för vanliga fel och lösningar.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.

## <a name="top-issues"></a>De främsta problemen
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Klustret har inte stöd för den begärda VM-storleken
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Försök igen med begäran med en mindre VM-storlek.
- Om storleken på den begärda virtuella datorn inte kan ändras:
    - Stoppa alla virtuella datorer i tillgänglighetsuppsättningen. Klicka på **resursgrupper** > resursgruppen > **resurser** > din tillgänglighetsuppsättning > **virtuella datorer** > din virtuella dator >  **Stoppa**.
    - När alla virtuella datorer har slutat, kan du skapa den virtuella datorn i önskad storlek.
    - Starta den nya virtuella datorn först och markera varje stoppad virtuell dator och klicka på Start.


## <a name="the-cluster-does-not-have-free-resources"></a>Klustret har inte frigöra resurser
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Försöka senare.
- Om den nya virtuella datorn kan vara en del av en annan tillgänglighetsuppsättning
    - Skapa en virtuell dator i en annan tillgänglighetsuppsättning (i samma region).
    - Lägg till ny virtuell dator i samma virtuella nätverk.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hur aktiverar jag min månadskredit för Visual studio Enterprise (BizSpark)

Om du vill aktivera din månadskredit finns [artikel](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Varför kan jag inte installera GPU-drivrutinen för en virtuell dator NV Ubuntu?

För närvarande finns endast stöd för Linux GPU i Azure NC virtuella datorer som kör Ubuntu Server 16.04 LTS. Mer information finns i [ställa in GPU drivrutiner för N-serien virtuella datorer som kör Linux](n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Drivrutinerna saknas för den virtuella datorn N-serien Linux

Drivrutiner för Linux-baserade virtuella datorer är placerade [här](n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Jag kan inte hitta en GPU-instans i den virtuella datorn N-serien

Om du vill dra nytta av GPU-funktionerna i Azure N-serien virtuella datorer som kör Windows Server 2016 eller Windows Server 2012 R2, måste du installera drivrutinerna grafik på varje virtuell dator efter distributionen. Drivrutinen installationsinformationen är tillgängliga för [virtuella Windows-datorer](../windows/n-series-driver-setup.md) och [virtuella Linux-datorer](n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Är N-serien virtuella datorer i min region?

Du kan kontrollera tillgänglighet från den [produkter som är tillgängliga efter region tabell](https://azure.microsoft.com/regions/services), och prissättning [här](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Jag kan inte se VM-storlek familj som jag vill när du ändrar storlek på den virtuella datorn.

När en virtuell dator körs distribueras den till en fysisk server. De fysiska servrarna i Azure-regioner grupperas i kluster av vanliga fysisk maskinvara. Ändra storlek på en virtuell dator som kräver att den virtuella datorn flyttas till annan maskinvara kluster är olika beroende på vilken distributionsmodell som användes för att distribuera den virtuella datorn.

- Virtuella datorer som distribueras i klassiska distributionsmodellen cloud service-distributionen måste tas bort och för att ändra de virtuella datorerna till en storlek i en annan familj för storlek på nytt.

- Virtuella datorer som distribueras i Resource Manager-distributionsmodellen, måste du stoppa alla virtuella datorer i tillgänglighetsuppsättning innan du ändrar storleken på någon virtuell dator i tillgänglighetsuppsättningen.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Den angivna VM-storleken stöds inte vid distribution i Tillgänglighetsuppsättningen.

Välj en storlek som stöds för den tillgänglighetsuppsättningen klustret. Det rekommenderas när du skapar en tillgänglighet för att välja den största VM storlek som du tror att du behöver och ha som ditt första distributionen till tillgänglighetsuppsättningen.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Vilka Linux-distributioner /-versioner som stöds på Azure?

Du hittar listan på Linux på [Azure-Endorsed distributioner](endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig klassiska virtuell dator till en tillgänglighetsuppsättning?

Ja. Du kan lägga till en befintlig klassiska virtuell dator till en ny eller befintlig Tillgänglighetsuppsättning. Mer information finns i [lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning](../windows/classic/configure-availability.md#addmachine).


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/).

Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
