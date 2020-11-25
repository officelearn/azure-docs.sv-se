---
title: Felsöka distribution av problem med virtuella Linux-datorer i Azure | Microsoft Docs
description: Felsök distribution av problem med att distribuera virtuella Linux-datorer i Azure Resource Manager distributions modell.
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
ms.openlocfilehash: cf8fb383625ec2752264d6e5a70d8625f06689fc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022826"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Felsöka problem med distributionen av virtuella Linux-datorer i Azure

För att felsöka distributions problem med virtuella datorer i Azure kan du läsa om de vanligaste [problemen](#top-issues) med vanliga fel och lösningar.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.

## <a name="top-issues"></a>Främsta problem
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Klustret har inte stöd för den begärda virtuella dator storleken
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Gör om begäran med en mindre VM-storlek.
- Om det inte går att ändra storleken på den begärda virtuella datorn:
    - Stoppa alla virtuella datorer i tillgänglighets uppsättningen. Klicka på **resurs grupper** > resurs gruppen > **resurser** > din tillgänglighets uppsättning > **Virtual Machines** > den virtuella datorn > **stoppa**.
    - När alla virtuella datorer har stoppats skapar du den virtuella datorn i önskad storlek.
    - Starta den nya virtuella datorn först och välj sedan var och en av de stoppade virtuella datorerna och klicka på Starta.


## <a name="the-cluster-does-not-have-free-resources"></a>Det finns inga lediga resurser för klustret
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Gör om begäran senare.
- Om den nya virtuella datorn kan ingå i en annan tillgänglighets uppsättning
    - Skapa en virtuell dator i en annan tillgänglighets uppsättning (i samma region).
    - Lägg till den nya virtuella datorn i samma virtuella nätverk.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hur gör jag för att aktivera min månads kredit för Visual Studio Enterprise (BizSpark)

Information om hur du aktiverar din månatliga kredit finns i den här [artikeln](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Varför kan jag inte installera GPU-drivrutinen för en Ubuntu NV-dator?

För närvarande är Linux GPU-stöd endast tillgängligt på Azure NC-virtuella datorer som kör Ubuntu Server 16,04 LTS. Mer information finns i [Konfigurera GPU-drivrutiner för virtuella datorer i N-serien som kör Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Mina driv rutiner saknas för den virtuella Linux N-seriens virtuella dator

Instruktioner för att installera driv rutiner för Linux-baserade virtuella datorer finns [här](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Jag kan inte hitta en GPU-instans i min virtuella dator i N-serien

Om du vill dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien måste du installera grafik driv rutiner på varje virtuell dator efter distributionen. Information om driv rutins installation finns [här](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="are-n-series-vms-available-in-my-region"></a>Är virtuella datorer i N-serien tillgängliga i min region?

Du kan kontrol lera tillgängligheten från [tabellen produkter som är tillgängliga per region](https://azure.microsoft.com/regions/services)och prissättning [här](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Jag kan inte se den virtuella datorns storleks familj som jag vill när du ändrar storlek på den virtuella datorn.

När en virtuell dator körs distribueras den till en fysisk server. De fysiska servrarna i Azure-regionerna grupperas i kluster av gemensamt fysiskt maskin vara. Att ändra storlek på en virtuell dator som kräver att den virtuella datorn flyttas till olika maskin varu kluster är olika beroende på vilken distributions modell som användes för att distribuera den virtuella datorn.

- Virtuella datorer som distribueras i den klassiska distributions modellen måste moln tjänst distributionen tas bort och omdistribueras för att ändra de virtuella datorerna till en storlek i en annan storleks familj.

- Virtuella datorer som distribueras i distributions modellen för Resource Manager måste du stoppa alla virtuella datorer i tillgänglighets uppsättningen innan du ändrar storleken på virtuella datorer i tillgänglighets uppsättningen.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Den angivna virtuella dator storleken stöds inte vid distribution i tillgänglighets uppsättning.

Välj en storlek som stöds i tillgänglighets uppsättningens kluster. Vi rekommenderar att du skapar en tillgänglighets uppsättning för att välja den största VM-storlek du tror att du behöver, och att den är din första distribution till tillgänglighets uppsättningen.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Vilka Linux-distributioner/versioner stöds på Azure?

Du hittar listan i Linux på [Azure-godkända distributioner](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig klassisk virtuell dator i en tillgänglighets uppsättning?

Ja. Du kan lägga till en befintlig klassisk virtuell dator i en ny eller befintlig tillgänglighets uppsättning. Mer information finns i [lägga till en befintlig virtuell dator i en tillgänglighets uppsättning](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/).

Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
