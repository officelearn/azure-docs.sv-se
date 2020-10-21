---
title: Vanliga frågor om VM på Azure Marketplace
description: Vanliga frågor som påträffades när du skapade en virtuell dator på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 131cd9b1de82213d89ee07f30aed25544f6fcb18
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284911"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Vanliga frågor om VM på Azure Marketplace

Vanliga frågor och svar (FAQ) omfattar vanliga problem som kan uppstå när du skapar ett erbjudande för virtuell dator (VM) i Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hur gör jag för att konfigurera ett virtuellt privat nätverk (VPN) för att fungera med mina virtuella datorer?

Om du använder Azure Resource Manager distributions modell har du tre alternativ:

- [Skapa en Route-baserad VPN-gateway med hjälp av Azure Portal](../vpn-gateway/create-routebased-vpn-gateway-portal.md)
- [Skapa en Route-baserad VPN-gateway med hjälp av Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Skapa en Route-baserad VPN-gateway med CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Vad är Microsofts support principer för att köra Microsoft Server-program på Azure-baserade virtuella datorer?

Du hittar information på [Microsoft Server Software support för Microsoft Azure virtuella datorer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Hur hanterar jag det anpassade skript tillägget i en virtuell dator i Start aktiviteten?

Mer information om hur du använder tillägget för anpassat skript med hjälp av Azure PowerShell modul, Azure Resource Manager mallar och fel söknings steg i Windows-system, finns i [anpassat skript tillägg för Windows](/azure/virtual-machines/extensions/custom-script-windows).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Är 32-bitars program eller tjänster som stöds i Azure Marketplace?

Nej. De operativ system och standard tjänster som stöds för virtuella Azure-datorer är alla 64-bitars. Även om de flesta 64-bitars operativ system stöder 32-bitars versioner av program för bakåtkompatibilitet, stöds inte användning av 32-bitars program som en del av din VM-lösning och rekommenderas inte. Återskapa ditt program som ett 64-bitars projekt.

Mer information finns i de här artiklarna:

- [Köra 32-bitars program](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Stöd för 32-bitars operativsystem i Azure Virtual Machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsofts serverprogramsupport för Microsoft Azure Virtual Machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Fel: VHD har redan registrerats med avbildnings lagrings plats som resurs

Varje gång jag försöker skapa en avbildning från mina virtuella hård diskar visas felet "VHD har redan registrerats med avbildnings lagrings plats som resurs" i Azure PowerShell. Jag har inte skapat någon avbildning förut eller kunde inte hitta någon bild med det här namnet i Azure. Hur gör jag för att lösa det?

Det här problemet uppstår vanligt vis om du har skapat en virtuell dator från en virtuell hård disk som har låst den. Bekräfta att det inte finns någon virtuell dator som har allokerats från denna virtuella hård disk och försök sedan igen. Om problemet kvarstår öppnar du ett support ärende. Se [Support för partner Center](support.md).

## <a name="next-steps"></a>Nästa steg

- [Fel sökning av VM-certifiering](azure-vm-create-certification-faq.md)
