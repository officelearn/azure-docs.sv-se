---
title: Vanliga problem vid skapande av VHD (FAQ) för Azure Marketplace
description: Vanliga frågor och svar om att skapa virtuella hård diskar och relaterade problem.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: dsindona
ms.openlocfilehash: df219ad7428eed5283c7a30a232308dca84fd25f
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146977"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Vanliga problem vid skapande av virtuell hård disk (FAQ)

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [skapa vanliga frågor och svar om VHD](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation) för att hantera dina migrerade erbjudanden.

Följande vanliga frågor och svar (FAQ) omfattar vanliga problem som påträffas vid skapande av virtuell hård disk (VHD) och virtuell dator (VM) för VM-erbjudanden. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Hur skapar du en virtuell dator från Azure Portal med den virtuella hård disk som överförs till Premium Storage?

Azure Marketplace har för närvarande inte stöd för att skapa virtuella dator erbjudanden från avbildningar som finns på hanterad lagring eller från Azure Premium Storage.  Mer information om dessa lagrings alternativ finns i [Översikt över Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Kan du använda virtuella datorer i generation 2 för erbjudanden?

Nej, endast generation 1-VHD: er stöds.  Men vi arbetar för närvarande med Microsoft Azure Platform-teamet för att undersöka stöd för virtuella datorer i generation 2.  Mer information om skillnaderna finns i [ska jag skapa en virtuell dator i generation 1 eller 2 i Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Hur ändrar du namnet på värden?

Du kan inte.  När den virtuella datorn har skapats kan inte användare (inklusive ägare) uppdatera namnet på värden.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hur återställer du fjärr skrivbords tjänsten eller dess inloggnings lösen ord?

I följande artiklar förklaras hur du utför RDS-återställning för Windows-och Linux-baserade virtuella datorer:   

- [Så här återställer du fjärrskrivbordstjänsten eller dess inloggningslösenord på en virtuell Windows-dator](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Så här återställer du ett Linux VM-lösenord eller SSH-nyckel, reparerar SSH-konfigurationen och kontrollerar disk konsekvens med VMAccess-tillägget](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Hur skapar jag nya SSH-certifikat?

Att skapa certifikat beskrivs i artikeln [Hämta signatur-URI för delad åtkomst för din VM-avbildning](./cpp-get-sas-uri.md) i det efterföljande avsnittet [skapa tekniska till gångar för ett erbjudande för virtuella datorer](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hur konfigurerar jag ett virtuellt privat nätverk (VPN) så att det fungerar med mina virtuella datorer?

Om du använder Azure Resource Manager distributions modell har du tre vanliga alternativ för att konfigurera ett VPN:
- [Skapa en Route-baserad VPN-gateway med hjälp av Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Skapa en Route-baserad VPN-gateway med PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Skapa en Route-baserad VPN-gateway med CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Vad är Microsofts support principer för att köra Microsoft Server-program på Azure-baserade virtuella datorer?

Dessa support principer beskrivs i artikeln [Microsoft Server Software support för Microsoft Azure virtuella datorer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Har virtuella datorer unika identifierare som är associerade med dem?

Ja, om det finns på Azure.  Azure tilldelar en unik identifierare med namnet unikt ID för den virtuella Azure-datorn till varje ny VM-resurs som skapas.  Mer information finns i blogg inlägget [unikt ID för virtuell Azure-dator](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Du kan också hämta den här identifieraren genom programmering via [list-API: et](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Hur hanterar du tillägget för anpassat skript i Start aktiviteten i en virtuell dator?

Följande artikel beskriver hur du använder tillägget för anpassat skript med hjälp av Azure PowerShell-modulen, Azure Resource Manager mallar och information om fel söknings steg i Windows-system: [anpassat skript tillägg för Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Är 32-bitars program eller tjänster som stöds på Azure Marketplace?

Nej. I allmänhet inte.  De operativ system och standard tjänster som stöds för virtuella Azure-datorer är alla 64-bitars.  Men från en teknisk synpunkt har de flesta 64-bitars operativ system stöd för att köra 32-bitars versioner av program för bakåtkompatibilitet.  Användning av 32-bitars program som en del av din VM-lösning stöds dock inte *och rekommenderas därför inte.*  Kompilera i stället ditt program som ett 64-bitars projekt.

Mer information finns i följande artiklar:
- [Köra 32-bitars program](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Stöd för 32-bitars operativsystem i Azure Virtual Machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsofts serverprogramsupport för Microsoft Azure Virtual Machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Varje gång jag försöker skapa en avbildning från mina virtuella hård diskar får jag ett fel `.VHD is already registered with image repository as the resource` meddelande i PowerShell. Jag har inte skapat någon avbildning tidigare eller kunde inte hitta någon bild med det här namnet i Azure. Hur gör jag för att du lösa det här problemet?

Det här problemet uppstår vanligt vis om användaren har etablerad en virtuell dator från en virtuell hård disk som har låst den.  Kontrol lera att det inte finns någon virtuell dator som har allokerats från denna virtuella hård disk och försök sedan igen.  Om problemet kvarstår öppnar du ett support ärende enligt beskrivningen i [stöd för Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

