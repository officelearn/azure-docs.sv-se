---
title: Felsöka distribution av problem med virtuella datorer i Windows i Azure | Microsoft-dokument
description: Felsöka distribution av problem med virtuella Windows-datorer i distributionsmodellen för Azure Resource Manager.
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
ms.openlocfilehash: cdbaeb5a97beba342bc471e75d1b07be0d0141ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921419"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Felsöka problem med distribution av virtuella Windows-datorer i Azure

Om du vill felsöka problem med distribution av virtuella datorer (VM) i Azure läser du de [vanligaste problemen](#top-issues) för vanliga fel och lösningar.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.

## <a name="top-issues"></a>De vanligaste problemen
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Hur kan jag använda och distribuera en Windows-klientavbildning till Azure?

Du kan använda Windows 7, Windows 8 eller Windows 10 i Azure för utvecklings-/testscenarier om du har en lämplig Visual Studio-prenumeration (tidigare MSDN). I den här [artikeln](../windows/client-images.md) beskrivs berättigandekraven för att köra Windows-klienten i Azure och användning av Azure Gallery-avbildningarna.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hur distribuerar jag en virtuell dator med hjälp av HYBRID Use Benefit (HUB)?

Det finns ett par olika sätt att distribuera virtuella Windows-datorer med Azure Hybrid Use Benefit.

För en enterprise agreement-prenumeration:

• Distribuera virtuella datorer från specifika Marketplace-avbildningar som är förkonfigurerade med Azure Hybrid Use Benefit.

För Enterprise-avtal:

• Ladda upp en anpassad virtuell dator och distribuera med hjälp av en Resource Manager-mall eller Azure PowerShell.

Mer information finns i följande resurser:

 - [Översikt över Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Nedladdningsbara vanliga frågor](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Azure Hybrid Användningsförmån för Windows Server och Windows Client](../windows/hybrid-use-benefit-licensing.md).

 - [Hur kan jag använda förmånen för hybridanvändning i Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hur aktiverar jag min månatliga kredit för Visual studio Enterprise (BizSpark)

Om du vill aktivera din månatliga kredit läser du den här [artikeln](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Hur lägger du till Företagsutveckling/Test i mitt Enterprise Agreement (EA) för att få tillgång till Windows-klientavbildningar?

Möjligheten att skapa prenumerationer baserat på Enterprise Dev/Test-erbjudandet är begränsad till kontoägare som har fått behörighet att göra det av en företagsadministratör. Kontoägaren skapar prenumerationer via Azure Account Portal och bör sedan lägga till aktiva Visual Studio-prenumeranter som medadministratörer. Så att de kan hantera och använda de resurser som behövs för utveckling och testning. Mer information finns i [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Mina drivrutiner saknas för min virtuella dator i Windows N-serien

Drivrutiner för Windows-baserade virtuella datorer finns [här](../windows/n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Jag kan inte hitta en GPU-instans i min virtuella dator i N-serien

Om du vill dra nytta av GPU-funktionerna i virtuella azure N-seriens virtuella datorer som kör Windows Server 2016 eller Windows Server 2012 R2 måste du installera NVIDIA-grafikdrivrutiner på varje virtuell dator efter distributionen. Information om drivrutinsinställningar är tillgänglig för [virtuella windows-datorer](../windows/n-series-driver-setup.md) och [virtuella Linux-datorer](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Är virtuella datorer i N-serien tillgängliga i min region?

Du kan kontrollera tillgängligheten från [tabellen Produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services)och priser [här](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Vilka klientavbildningar kan jag använda och distribuera i Azure och hur får jag dem?

Du kan använda Windows 7, Windows 8 eller Windows 10 i Azure för utvecklings-/testscenarier förutsatt att du har en lämplig Visual Studio-prenumeration (tidigare MSDN). 

- Windows 10-avbildningar är tillgängliga från Azure Gallery i [kvalificerade utvecklings-/testerbjudanden](../windows/client-images.md#eligible-offers). 
- Visual Studio-prenumeranter inom alla typer av erbjudanden kan också [förbereda och skapa](../windows/prepare-for-upload-vhd-image.md) en 64-bitars Windows 7-, Windows 8- eller Windows 10-avbildning och sedan ladda upp till [Azure](../windows/upload-generalized-managed.md). Användningen är begränsad till utveckling/test av aktiva Visual Studio-prenumeranter.

I den här [artikeln](../windows/client-images.md) beskrivs berättigandekraven för att köra Windows-klienten i Azure och användningen av Azure Gallery-avbildningarna.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Jag kan inte se vm-storlek familj som jag vill när ändra storlek på min virtuella dator.

När en virtuell dator körs distribueras den till en fysisk server. De fysiska servrarna i Azure-regioner är grupperade i kluster av gemensam fysisk maskinvara. Att ändra storlek på en virtuell dator som kräver att den virtuella datorn flyttas till olika maskinvarukluster är olika beroende på vilken distributionsmodell som användes för att distribuera den virtuella datorn.

- Virtuella datorer som distribueras i den klassiska distributionsmodellen måste molntjänstdistributionen tas bort och distribueras om för att ändra de virtuella datorerna till en storlek i en annan storleksfamilj.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- Virtuella datorer som distribueras i Resource Manager-distributionsmodellen måste du stoppa alla virtuella datorer i tillgänglighetsuppsättningen innan du ändrar storleken på en virtuell dator i tillgänglighetsuppsättningen.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Den listade vm-storleken stöds inte vid distribution i tillgänglighetsuppsättning.

Välj en storlek som stöds i tillgänglighetsuppsättningens kluster. Vi rekommenderar att du skapar en tillgänglighetsuppsättning för att välja den största vm-storleken du tror att du behöver och har den första distributionen till tillgänglighetsuppsättningen.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig klassisk virtuell dator i en tillgänglighetsuppsättning?

Ja. Du kan lägga till en befintlig klassisk virtuell dator i en ny eller befintlig tillgänglighetsuppsättning. Mer information finns i [Lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/).

Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
