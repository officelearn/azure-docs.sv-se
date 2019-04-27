---
title: Vanliga problem vid skapande av virtuell Hårddisk (FAQ) för Azure Marketplace | Microsoft Docs
description: Vanliga frågor om att skapa en virtuell Hårddisk och associerade problem.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: Patrick.Butler
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: 381f88c4641417bceca0f988d4b1a187aedaa642
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744201"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Vanliga problem vid skapande av virtuell Hårddisk (FAQ)

Följande och vanliga frågor svar (FAQ) omfattar vanliga problem påträffades under virtuell hårddisk (VHD) och virtuella datorer (VM) skapa en virtuell dator-erbjudanden. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Hur skapar du en virtuell dator från Azure-portalen med hjälp av den virtuella Hårddisken som har överförts till premium storage?

Azure Marketplace stöder för närvarande inte skapa VM-erbjudanden från avbildningar som finns på hanterad lagring eller från Azure Premium Storage.  Mer information om dessa lagringsalternativ finns i [Azure översikten över Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Kan du använda virtuella datorer i generation 2-erbjudanden?

Nej, endast generering 1 virtuella hårddiskar stöds.  Men arbetar vi med Microsoft Azure Platform-teamet att undersöka stöd för virtuella datorer i generation 2.  Mer information om skillnaderna finns [bör jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Hur ändrar du namnet på värden?

Du kan inte.  När datorn har skapats kan kan inte användare (inklusive ägare) uppdatera namnet på värden.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hur du återställer Fjärrskrivbordstjänsten eller dess lösenord för inloggning?

Följande artiklar beskriver hur du utför antalet återställningar av klientåtkomstlicenser för Windows - och Linux-baserade virtuella datorer:   

- [Så här återställer du fjärrskrivbordstjänsten eller dess inloggningslösenord på en virtuell Windows-dator](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Så här återställer du en Linux VM-lösenord eller SSH-nyckel, korrigera SSH-konfigurationen och kontrollera disk konsekvens med hjälp av VMAccess-tillägget](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Hur du för att generera nya SSH-certifikat?

Generering av certifikat beskrivs i artikeln [Get delad åtkomstsignatur URI för din avbildning](./cpp-get-sas-uri.md) i efterföljande avsnitt [skapa tekniska resurser till en virtuell dator](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hur konfigurerar du ett virtuellt privat nätverk (VPN) för att arbeta med Mina virtuella datorer?

Om du använder Azure Resource Manager-distributionsmodellen, har du tre vanliga alternativ för att skapa en VPN-anslutning:
- [Skapa en ruttbaserad VPN-gateway med Azure portal](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Skapa en ruttbaserad VPN-gateway med hjälp av PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Skapa en ruttbaserad VPN-gateway med hjälp av CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Vad är Microsoft support-principer för att köra serverprogramvara från Microsoft på Azure-baserade virtuella datorer?

Supporttjänsterna principer beskrivs i artikeln [Microsofts serverprogramsupport för Microsoft Azure-datorer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Har unika identifierare som är kopplade till dem i virtuella datorer?

Ja, om finns i Azure.  Azure tilldelar en unik identifierare som heter Azure VM unikt ID till varje ny VM-resurs som har skapats.  Mer information finns i blogginlägget [unikt ID för Azure VM](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Du kan också hämta den här identifieraren programmässigt via den [lista API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>I en virtuell dator, hur du hanterar det anpassa skripttillägget startåtgärden?

I följande artikel beskriver hur man använder det anpassade Skripttillägget med Azure PowerShell-modulen, Azure Resource Manager-mallar och information om felsökning i Windows-System: [Tillägget för anpassat skript för Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Är 32-bitars program eller tjänster som stöds på Azure Marketplace?

I allmänhet Nej.  Är alla 64-bitars operativsystem som stöds och standardtjänster för virtuella Azure-datorer.  Men ur en teknisk synvinkel mest 64-bitars operativsystem kan du köra 32-bitars versioner av program för bakåtkompatibilitet.  Dock använda 32-bitarsprogram som en del av din lösning för virtuell dator stöds inte och därför är *med hög rekommenderas inte*.  I stället omkompilera ditt program som ett 64-bitars-projekt.

Mer information finns i följande artiklar:
- [32-bitars program som körs](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Stöd för 32-bitars operativsystem i Azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsofts serverprogramsupport för Microsoft Azure Virtual Machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Varje gång jag försöker skapa en avbildning från min virtuella hårddiskar, visas felmeddelandet `.VHD is already registered with image repository as the resource` i PowerShell. Jag har skapat inte en avbildning innan eller var det jag hittade bilder med det här namnet i Azure. Hur löser jag det här problemet?

Det här problemet uppstår vanligen om användaren har etablerat en virtuell dator från en virtuell Hårddisk som har låst.  Kontrollera att det finns inga VM som har allokerats från den här virtuella Hårddisken och försök sedan igen.  Om problemet kvarstår, öppna ett supportärende enligt beskrivningen i [stöd för Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

