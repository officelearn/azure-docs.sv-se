---
title: "Felsöka distribution problem med Windows virtuell dator i Azure | Microsoft Docs"
description: "Felsöka distribution problem med Windows virtuell dator i Azurethe Resource Manager-distributionsmodellen."
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 0f296791a8cce154bf9b258827243d76638e59ac
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Felsöka distribution problem med Windows virtuell dator i Azure

Om du vill felsöka distributionsproblem för virtuell dator (VM) i Azure, granska den [vanliga problem](#top-issues) för vanliga fel och lösningar.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.

## <a name="top-issues"></a>De främsta problemen
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Hur kan använda och distribuera en avbildning för windows-klienten till Azure?

Du kan använda Windows 7, Windows 8 eller Windows 10 i Azure för utveckling och testning scenarier om du har en lämplig Visual Studio (tidigare MSDN)-prenumeration. Detta [artikel](client-images.md) beskrivs behörighetskraven för Windows-klienter som körs i Azure och använder Azure-galleriet bilder.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hur kan jag distribuera en virtuell dator med Hybrid Använd förmånen (NAV)?

Det finns ett par olika sätt att distribuera Windows-datorer och de fördelar som Azure Hybrid användning.

För en prenumeration för Enterprise-avtal:

• Distribuera virtuella datorer från specifika Marketplace-avbildningar som är förkonfigurerad med Azure Hybrid Använd förmån.

För Enterprise-avtal:

• Ladda upp en egen virtuell dator och distribuera med hjälp av en Resource Manager-mall eller Azure PowerShell.

Mer information finns i följande resurser:

 - [Översikt över Azure Hybrid Använd förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Nedladdningsbar vanliga frågor och svar](http://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Hybridrapportering i Azure används förmån för Windows Server och Windows-klient](hybrid-use-benefit-licensing.md).

 - [Hur kan jag använda Hybrid använda fördelen i Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hur aktiverar jag min månadskredit för Visual studio Enterprise (BizSpark)

Om du vill aktivera din månadskredit finns [artikel](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Hur du lägger till utveckling och testning Enterprise till mitt Enterprise avtal (EA) att få tillgång till fönstret klientavbildningar?

Möjligheten att skapa prenumerationer baserat på utveckling och testning Enterprise erbjudandet är begränsad till kontot ägare som har behörighet att göra det genom att företagsadministratör. Kontoägaren skapar prenumerationer via Azure-Kontoportalen och sedan ska lägga till aktiva Visual Studio-prenumeranter som medadministratörer. Så att de kan hantera och använda de resurser som krävs för utveckling och testning. Mer information finns i [utveckling och testning Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Drivrutinerna saknas för den virtuella datorn N-serien Windows

Drivrutiner för Windows-baserade virtuella datorer är placerade [här](n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Jag kan inte hitta en GPU-instans i den virtuella datorn N-serien

Om du vill dra nytta av GPU-funktionerna i Azure N-serien virtuella datorer som kör Windows Server 2016 eller Windows Server 2012 R2, måste du installera drivrutinerna grafik på varje virtuell dator efter distributionen. Drivrutinen installationsinformationen är tillgängliga för [virtuella Windows-datorer](n-series-driver-setup.md) och [virtuella Linux-datorer](../linux/n-series-driver-setup.md).

## <a name="are-client-images-supported-for-n-series"></a>Stöds klientavbildningar för N-serien?

Azure stöder för närvarande endast N-serien på virtuella datorer som kör Windows Server- och Linux-operativsystem.

## <a name="is-n-series-vms-available-in-my-region"></a>Är N-serien virtuella datorer i min region?

Du kan kontrollera tillgänglighet från den [produkter som är tillgängliga efter region tabell](https://azure.microsoft.com/regions/services), och prissättning [här](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Vilka klientavbildningar kan jag använda och distribuera i Azure och hur att jag dem?

Du kan använda Windows 7, Windows 8 eller Windows 10 i Azure för utveckling och testning scenarier som du har en lämplig Visual Studio (tidigare MSDN)-prenumeration. 

- Windows 10-avbildningar som är tillgängliga från Azure-galleriet inom [berättigade utveckling och testning erbjuder](client-images.md#eligible-offers). 
- Visual Studio-prenumeranter inom någon typ av erbjudandet kan också [på lämpligt sätt förbereda och skapa](prepare-for-upload-vhd-image.md) en 64-bitars Windows 7, Windows 8 eller Windows 10-avbildning och sedan [Överför till Azure](upload-generalized-managed.md). Användningen är begränsad till utveckling och testning av aktiva Visual Studio-prenumeranter.

Detta [artikel](client-images.md) beskrivs behörighetskraven för Windows-klienter som körs i Azure och användning av Azure-galleriet bilder.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Jag kan inte se VM-storlek familj som jag vill när du ändrar storlek på den virtuella datorn.

När en virtuell dator körs distribueras den till en fysisk server. De fysiska servrarna i Azure-regioner grupperas i kluster av vanliga fysisk maskinvara. Ändra storlek på en virtuell dator som kräver att den virtuella datorn flyttas till annan maskinvara kluster är olika beroende på vilken distributionsmodell som användes för att distribuera den virtuella datorn.

- Virtuella datorer som distribueras i klassiska distributionsmodellen cloud service-distributionen måste tas bort och för att ändra de virtuella datorerna till en storlek i en annan familj för storlek på nytt.

- Virtuella datorer som distribueras i Resource Manager-distributionsmodellen, måste du stoppa alla virtuella datorer i tillgänglighetsuppsättning innan du ändrar storleken på någon virtuell dator i tillgänglighetsuppsättningen.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Den angivna VM-storleken stöds inte vid distribution i Tillgänglighetsuppsättningen.

Välj en storlek som stöds för den tillgänglighetsuppsättningen klustret. Det rekommenderas när du skapar en tillgänglighet för att välja den största VM storlek som du tror att du behöver och ha som ditt första distributionen till tillgänglighetsuppsättningen.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig klassiska virtuell dator till en tillgänglighetsuppsättning?

Ja. Du kan lägga till en befintlig klassiska virtuell dator till en ny eller befintlig Tillgänglighetsuppsättning. Mer information finns i [lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning](classic/configure-availability.md#addmachine).


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/).

Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
