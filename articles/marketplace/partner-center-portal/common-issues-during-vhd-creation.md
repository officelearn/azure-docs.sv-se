---
title: Vanliga problem vid skapande av virtuella hårddiskar (VANLIGA FRÅGOR)
description: Vanliga frågor och svar om vanliga problem när du skapar en virtuell hårddisk (VHD).
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266271"
---
# <a name="common-issues-during-vhd-creation"></a>Vanliga problem när vhd skapas

> [!NOTE]
> Vi flyttar hanteringen av dina Azure VM-erbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats fortsätter du att följa instruktionerna i [vanliga problem under vanliga frågor om att skapa virtuella hårddiskar](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) i Cloud Partner Portal för att hantera dina erbjudanden.

De här vanliga frågorna (FAQ) täcker vanliga problem som kan uppstå när du skapar en virtuell hårddisk (VHD) för ditt Azure Virtual Machine-erbjudande.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Hur skapar jag en virtuell dator från Azure-portalen med en virtuell hårddisk i premiumlagring?

Azure Marketplace stöder för närvarande inte att skapa VM-erbjudanden från avbildningar på hanterad lagring eller från Azure Premium Storage. Mer information finns i [Översikt över hanterade hårddiskar i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Kan jag använda virtuella generation 2-datorer för erbjudanden?

Nej, endast virtuella hårddiskar för generation 1 stöds. Vi arbetar dock för närvarande med Microsoft Azure Platform Team för att undersöka stöd för virtuella generation 2-datorer. Mer information finns i [Ska jag skapa en virtuell generation 1 eller 2 virtuell dator i Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Hur ändrar jag namnet på värden?

Det kan du inte. När en virtuell dator har skapats kan användare (inklusive ägare) inte uppdatera värdnamnet.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hur återställer jag tjänsten Fjärrskrivbord eller dess inloggningslösenord?

I de här artiklarna beskrivs hur du utför RDS-återställningar för Windows- och Linux-baserade virtuella datorer:

* [Så här återställer du fjärrskrivbordstjänsten eller dess inloggningslösenord på en virtuell Windows-dator](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Så här återställer du ett Linux VM-lösenord eller SSH-nyckel, åtgärdar SSH-konfigurationen och kontrollerar diskkonsekvens med VMAccess-tillägget](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Hur skapar jag nya SSH-certifikat?

Generering av certifikat förklaras i [Azure VM-avbildningscertifiering](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hur konfigurerar jag ett virtuellt privat nätverk (VPN) för att arbeta med mina virtuella datorer?

Om du använder distributionsmodellen för Azure Resource Manager har du tre alternativ:

* [Skapa en ruttbaserad VPN-gateway med Azure-portalen](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Skapa en ruttbaserad VPN-gateway med Azure PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Skapa en ruttbaserad VPN-gateway med CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Vilka microsoftsupportprinciper gäller för att köra Microsoft-serverprogramvara på Azure-baserade virtuella datorer?

Du hittar information på [Microsoft Server Software Support för Virtuella Microsoft Azure-datorer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Har virtuella datorer unika identifierare associerade med dem?

Ja, om den finns på Azure. Azure tilldelar en unik identifierare, kallad [Azure Virtual Machine Unique ID,](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)till varje ny vm-resurs som skapas. Mer information finns i Azure Virtual Machine Unique ID. Du kan också hämta den här identifieraren via [list-API:et](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Hur hanterar jag det anpassade skripttillägget i startuppgiften i en virtuell dator?

Mer information om hur du använder tillägget Anpassat skript med Azure PowerShell-modulen, Azure Resource Manager-mallar och felsökningssteg på Windows-system finns i [Anpassat skripttillägg för Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Stöds 32-bitarsprogram eller tjänster i Azure Marketplace?

Nej. I allmänhet inte. De operativsystem och standardtjänster som stöds för virtuella Azure-datorer är alla 64-bitars. Även om de flesta 64-bitars operativsystem stöder 32-bitarsversioner av program för bakåtkompatibilitet, stöds inte 32-bitarsprogram som en del av vm-lösningen och avskräcks inte. Återskapa ditt program som ett 64-bitarsprojekt.

Mer information finns i dessa artiklar:

* [Köra 32-bitarsprogram](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Stöd för 32-bitars operativsystem i Azure Virtual Machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsofts serverprogramsupport för Microsoft Azure Virtual Machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Fel: VHD har redan registrerats med avbildningsdatabasen som resurs

Varje gång jag försöker skapa en avbildning från mina virtuella hårddiskar får jag felmeddelandet "VHD är redan registrerat med avbildningsdatabasen som resurs" i Azure PowerShell. Jag har inte skapat någon avbildning innan inte heller hittade jag någon avbildning med det här namnet i Azure. Hur gör jag för att lösa det?

Det här problemet visas vanligtvis om du har skapat en virtuell dator från en virtuell hårddisk som har ett lås på den. Bekräfta att det inte finns någon virtuell dator allokerad från den här virtuella hårddisken och försök sedan igen. Om problemet kvarstår öppnar du en supportbiljett. Se [Support för Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
