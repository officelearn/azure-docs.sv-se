---
title: Vanliga problem vid skapande av virtuella hårddiskar (Vanliga frågor) för Azure Marketplace
description: Vanliga frågor och svar om skapande av virtuella hårddiskar och tillhörande problem.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: dsindona
ms.reviewer: v-divte; v-miclar
ms.openlocfilehash: 192fea4101fb972ec66bb5f21a2a83f9903f0855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278117"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Vanliga problem vid skapande av virtuella hårddiskar (VANLIGA FRÅGOR)

Följande vanliga frågor och svar (FAQ) täcker vanliga problem som uppstår under virtuella hårddiskar (VHD) och virtuell dator (VM) skapas för VM-erbjudanden. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Hur skapar du en virtuell dator från Azure-portalen med den virtuella hårddisken som överförs till premiumlagring?

Azure Marketplace stöder för närvarande inte att skapa VM-erbjudanden från avbildningar som finns på hanterad lagring eller från Azure Premium Storage.  Mer information om dessa lagringsalternativ finns i [Översikt över hanterade diskar i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Kan du använda generation 2 virtuella datorer för erbjudanden?

Nej, endast generering 1 virtuella hårddiskar stöds.  Vi arbetar dock för närvarande med Microsoft Azure Platform Team för att undersöka stöd för virtuella generation 2-datorer.  Mer information om skillnaderna finns i Ska jag skapa en virtuell dator för [generation 1 eller 2 i Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Hur ändrar du namnet på värden?

Det kan du inte.  När den virtuella datorn har skapats kan användare (inklusive ägare) inte uppdatera namnet på värden.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hur återställer du tjänsten Fjärrskrivbord eller dess inloggningslösenord?

I följande artiklar beskrivs hur du utför RDS-återställningar för Windows- och Linux-baserade virtuella datorer:   

- [Så här återställer du fjärrskrivbordstjänsten eller dess inloggningslösenord på en virtuell Windows-dator](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Så här återställer du ett Linux VM-lösenord eller SSH-nyckel, åtgärdar SSH-konfigurationen och kontrollerar diskkonsekvens med VMAccess-tillägget](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Hur genererar du nya SSH-certifikat?

Generering av certifikat förklaras i artikeln [Hämta signatur-URI för den virtuella datorn i](./cpp-get-sas-uri.md) det efterföljande avsnittet Skapa tekniska resurser för ett [vm-erbjudande](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hur konfigurerar du ett virtuellt privat nätverk (VPN) för att arbeta med mina virtuella datorer?

Om du använder distributionsmodellen för Azure Resource Manager har du tre vanliga alternativ för att konfigurera ett VPN:
- [Skapa en ruttbaserad VPN-gateway med Azure-portalen](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Skapa en ruttbaserad VPN-gateway med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Skapa en ruttbaserad VPN-gateway med CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Vilka microsoftsupportprinciper gäller för att köra Microsoft-serverprogramvara på Azure-baserade virtuella datorer?

De här supportprinciperna beskrivs i artikeln [stöd för Microsoft Server-programvara för virtuella Datorer i Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Har virtuella datorer unika identifierare associerade med dem?

Ja, om den finns på Azure.  Azure tilldelar en unik identifierare med namnet Azure Virtual Machine Unique ID till varje ny vm-resurs som skapas.  Mer information finns i blogginlägget [Azure Virtual Machine Unique ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Du kan också hämta den här identifieraren programmässigt via [list-API:et](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Hur hanterar du det anpassade skripttillägget i startuppgiften i en virtuell dator?

I följande artikel beskrivs hur du använder tillägget Anpassat skript med Azure PowerShell-modulen, Azure Resource Manager-mallar och felsökningssteg för information om Windows-system: [Anpassat skripttillägg för Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Stöds 32-bitarsprogram eller -tjänster på Azure Marketplace?

Nej. I allmänhet inte.  De operativsystem och standardtjänster som stöds för virtuella Azure-datorer är alla 64-bitars.  Men ur teknisk synvinkel, de flesta 64-bitars operativsystem stöder kör 32-bitars versioner av program för bakåtkompatibilitet.  Användning av 32-bitarsprogram som en del av vm-lösningen stöds dock inte och är därför *mycket avskräckt.*  Kompilera i stället om programmet som ett 64-bitarsprojekt.

Mer information finns i följande artiklar:
- [Köra 32-bitarsprogram](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Stöd för 32-bitars operativsystem i Azure Virtual Machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsofts serverprogramsupport för Microsoft Azure Virtual Machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Varje gång jag försöker skapa en bild från mina `.VHD is already registered with image repository as the resource` virtuella hårddiskar får jag felet i PowerShell. Jag har inte skapat någon avbildning innan inte heller hittade jag någon avbildning med det här namnet i Azure. Hur löser jag problemet?

Det här problemet uppstår vanligtvis om användaren etablerat en virtuell dator från en virtuell hårddisk som har ett lås på den.  Kontrollera att det inte finns någon virtuell dator allokerad från den här virtuella hårddisken och försök sedan igen.  Om problemet kvarstår öppnar du en supportbiljett enligt [Support för Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

