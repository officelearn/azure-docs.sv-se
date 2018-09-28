---
title: Felsöka distribution Windows VM-problem i Azure | Microsoft Docs
description: Felsöka distribution Windows VM-problem i Azurethe Resource Manager-distributionsmodellen.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 688c7fed65fc2ceae2f508fc46e24ded76f5a54f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414872"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Felsöka distribution Windows VM-problem i Azure

Felsök distributionsproblem för virtuell dator (VM) i Azure genom att granska den [de främsta problemen](#top-issues) för vanliga fel och lösningar.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.

## <a name="top-issues"></a>De vanligaste problemen
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Hur kan jag använda och distribuera en avbildning för windows-klient till Azure?

Du kan använda Windows 7, Windows 8 eller Windows 10 i Azure för utveckling och testning om du har en lämplig Visual Studio (tidigare MSDN)-prenumeration. Detta [artikeln](../windows/client-images.md) beskrivs kraven för berättigande för Windows-klienten som körs i Azure och använder över avbildningar av virtuella Azure-galleriet.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hur kan jag distribuera en virtuell dator med Hybrid Använd förmånen (HUB)?

Det finns ett par olika sätt att distribuera Windows-datorer med Azure Hybrid Use Benefit.

För en Enterprise Agreement-prenumeration:

• Distribuera virtuella datorer från specifika Marketplace-avbildningar som är förkonfigurerad med Azure Hybrid Use Benefit.

För Enterprise-avtal:

• Ladda upp en anpassad virtuell dator och distribuera med hjälp av en Resource Manager-mall eller Azure PowerShell.

Mer information finns i följande resurser:

 - [Översikt över Azure Hybrid Use Benefit ](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Nedladdningsbara vanliga frågor och svar](http://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Azure Hybrid Use Benefit för Windows Server- och Windows](../windows/hybrid-use-benefit-licensing.md).

 - [Hur kan jag använda Hybrid Use-förmånen i Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hur aktiverar jag min månatliga kredit för Visual studio Enterprise (BizSpark)

Om du vill aktivera din månatliga kredit finns i den här [artikeln](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Hur du lägger till Enterprise – utveckling/testning till mitt Enterprise Agreement (EA) att få åtkomst till Windows-klientavbildningar?

Möjligheten att skapa abonnemang baserat på Enterprise – utveckling/testning-erbjudandet är begränsad till kontoinnehavare som har fått tillstånd att göra detta av en företagsadministratör. Kontoinnehavaren skapar prenumerationer via Azure-Kontoportalen och lägger sedan till aktiva Visual Studio-prenumeranter som medadministratörer. Så att de kan hantera och använda de resurser som krävs för utveckling och testning. Mer information finns i [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Drivrutinerna saknas för den virtuella datorn Windows N-serien

Drivrutiner för Windows-baserade virtuella datorer är placerade [här](../windows/n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Jag kan inte hitta en GPU-instans i N-serien virtuella datorn

Om du vill dra nytta av GPU-funktionerna i Azure N-serien virtuella datorer som kör Windows Server 2016 eller Windows Server 2012 R2, måste du installera NVIDIA-grafikdrivrutiner på varje virtuell dator efter distributionen. Inställningsinformation för drivrutinen är tillgänglig för [Windows VMs](../windows/n-series-driver-setup.md) och [virtuella Linux-datorer](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Är virtuella datorer i N-serien i min region?

Du kan kontrollera tillgänglighet från den [produkttillgänglighet per region tabell](https://azure.microsoft.com/regions/services), och priser [här](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Vilka client-avbildningar kan jag använda distribuerar i Azure och hur att jag dem?

Du kan använda Windows 7, Windows 8 eller Windows 10 i Azure för utveckling och testning förutsatt att du har en lämplig Visual Studio (tidigare MSDN)-prenumeration. 

- Windows 10-bilder som är tillgängliga från Azure-galleriet i [berättigade utveckling/testning erbjuder](../windows/client-images.md#eligible-offers). 
- Visual Studio-prenumeranter i någon typ av erbjudande kan också [rätt förbereda och skapa](../windows/prepare-for-upload-vhd-image.md) en 64-bitars Windows 7, Windows 8 eller Windows 10-avbildning och sedan [Överför till Azure](../windows/upload-generalized-managed.md). Användningen är begränsad till utveckling och testning av aktiva Visual Studio-prenumeranter.

Detta [artikeln](../windows/client-images.md) beskrivs kraven för berättigande för som kör Windows-klient i Azure och användning av Azure-galleriet-avbildningar.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Jag kan inte se VM-storleksfamilj som jag vill ha när du ändrar storlek på den virtuella datorn.

Om en virtuell dator är igång kan distribueras den till en fysisk server. De fysiska servrarna i Azure-regioner är grupperade i kluster av vanliga fysisk maskinvara. Ändra storlek på en virtuell dator som kräver att den virtuella datorn flyttas till annan maskinvarukluster är olika beroende på vilken distributionsmodell som användes för att distribuera den virtuella datorn.

- Virtuella datorer som distribuerats i klassiska distributionsmodellen, molntjänstdistribution måste tas bort och distribueras om för att ändra de virtuella datorerna till en storlek i en annan storleksfamilj.

- Virtuella datorer som distribueras i Resource Manager-distributionsmodellen, måste du stoppa alla virtuella datorer i tillgänglighetsuppsättningen innan du ändrar storleken på virtuella datorer i tillgänglighetsuppsättningen.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Den angivna VM-storleken stöds inte när du distribuerar i Tillgänglighetsuppsättningen.

Välj en storlek som stöds på klustret i tillgänglighetsuppsättningen. Du rekommenderas när du skapar en tillgänglighetsuppsättning för att välja den största VM-storlek du tror att du behöver och har som första distributionen i tillgänglighetsuppsättningen.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig klassisk virtuell dator till en tillgänglighetsuppsättning?

Ja. Du kan lägga till en befintlig klassisk virtuell dator till en ny eller befintlig Tillgänglighetsuppsättning. Mer information finns i [lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning](../windows/classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/).

Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
