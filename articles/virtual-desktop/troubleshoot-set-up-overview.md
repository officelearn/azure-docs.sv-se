---
title: Windows virtuellt skrivbord översikt, feedback och support – Azure
description: En översikt för att felsöka problem när du konfigurerar en miljö för virtuella Windows-skrivbordet organisationer.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 8e344d6908ba19f8e2294c7777b9c1016eafaf52
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927649"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Översikt över, feedback och support

Den här artikeln innehåller en översikt över de problem du kan stöta på när du konfigurerar en miljö för virtuella Windows-skrivbordet organisationer och innehåller olika sätt att lösa problemen.

## <a name="provide-feedback"></a>Ge feedback

Vi inte är för närvarande tar supportärenden när virtuella Windows-skrivbordet är i förhandsversionen. Gå till den [Windows Desktop Tech-Community virtuella](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) att diskutera virtuellt skrivbord i Windows-tjänsten med produktteamet och aktiva community-medlemmar.

## <a name="escalation-tracks"></a>Eskalering spår

Använd följande tabell för att identifiera och lösa problem som kan uppstå när du installerar en klient-miljön med hjälp av fjärrskrivbordsklienten.

>[!NOTE]
>Vi inte är för närvarande tar supportärenden när virtuella Windows-skrivbordet är i förhandsversionen. När vi refererar till stöd för virtuella Windows-skrivbordet, kan du gå till vårt Tech Community-forum för tillfället. Gå till den [Windows Desktop Tech-Community virtuella](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) att diskutera problem med produktteamet och aktiva community-medlemmar. Om du vill lösa ett supportärende inkludera aktivitets-ID och ungefärliga tidsram för när problemet uppstod.

| **Problemet**                                                            | **Föreslagen lösning**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Skapa en klient                                                    | Om det finns ett avbrott i Azure, kontakta [supporten](https://azure.microsoft.com/support/options/); annars Kontakta **stöd för Remote Desktop Services/Windows virtuellt skrivbord**.|
| Åtkomst till Marketplace-mallar i Azure-portalen       | Om det finns ett avbrott i Azure, kontakta [Azure-supporten](https://azure.microsoft.com/support/options/). <br> <br> Azure Marketplace Windows virtuellt skrivbord mallar är gratis.|
| Åtkomst till Azure Resource Manager-mallar från GitHub                                  | Se avsnittet ”Skapa Windows virtuell värd för virtuella datorer” i [klient och värden lagringspoolen skapa](troubleshoot-set-up-issues.md). Om problemet fortfarande olösta kontaktar den [GitHub-supportteamet](https://github.com/contact). <br> <br> Om felet uppstår när du har åtkomst till mallen i GitHub, kontakta [Azure-supporten](https://azure.microsoft.com/support/options/).|
| Sessionen pool Azure Virtual Network (VNET) och Express Route Värdinställningar               | Kontakta **Azure-supporten (nätverk)**. |
| Sessionen värd pool skapa en virtuell dator (VM) när Azure Resource Manager-mallar som medföljer Windows virtuella skrivbordet inte används | Kontakta **Azure-supporten (beräkning)**. <br> <br> Problem med Azure Resource Manager-mallar som tillhandahålls med virtuella Windows-skrivbordet, finns i Skapa virtuella Windows-skrivbordet klient avsnittet av [klient och värden lagringspoolen skapa](troubleshoot-set-up-issues.md). |
| Hantera virtuella Windows-skrivbordet värdmiljön för sessionen från Azure portal    | Kontakta **Azure-supporten**. <br> <br> Av hanteringsproblem när du använder Remote Desktop Services/Windows Virtual Desktop PowerShell, se [Windows PowerShell för virtuella skrivbord](troubleshoot-powershell.md) eller kontakta den **Remote Desktop Services/Windows virtuellt skrivbord support-teamet** . |
| Hantera virtuella Windows-skrivbordet konfigurationer är kopplad till värden pooler och programgrupper (app-grupper)      | Se [Windows PowerShell för virtuella skrivbord](troubleshoot-powershell.md), eller kontakta den **Remote Desktop Services/Windows virtuellt skrivbord supportteamet**. <br> <br> Om problem är knutna till det grafiska användargränssnittet (GUI) för exemplet, kan du nå ut till Yammer-community.|
| Fjärrskrivbordsklienter skador på start                                                 | Se [Remote Desktop-klientanslutningar](troubleshoot-client-connection.md) och om detta inte löser problemet, kontakta **Remote Desktop Services/Windows virtuellt skrivbord supportteamet**.  <br> <br> Om det är ett nätverksproblem, måste användarna kontakta nätverksadministratören. |
| Nätverket är anslutet men ingen feed                                                                 | Felsök med hjälp av den ”användare ansluter men inget att visas (inga matning)” avsnittet av [Remote Desktop-klientanslutningar](troubleshoot-client-connection.md). <br> <br> Om dina användare har tilldelats till en appgrupp, eskalera till den **Remote Desktop Services/Windows virtuellt skrivbord supportteamet**. |
| Feed-identifiering av problem på grund av nätverket                                            | Användarna måste du kontakta nätverksadministratören. |
| Anslutande klienter                                                                    | Se [Remote Desktop-klientanslutningar](troubleshoot-client-connection.md) och om det inte löser problemet, se [Session Värdkonfiguration för virtuell dator](troubleshoot-vm-configuration.md). |
| Svarstiden för fjärrprogram eller desktop                                      | Om problem är knutna till ett visst program eller en produkt, kontakta teamet som ansvarar för den här produkten. |
| Licensiering meddelanden eller fel                                                          | Om problem är knutna till ett visst program eller en produkt, kontakta teamet som ansvarar för den här produkten. |

## <a name="next-steps"></a>Nästa steg

- Felsökning av problem när du skapar en pool med klient- och värden i en miljö med virtuella Windows-skrivbordet beskrivs [klient och värden lagringspoolen skapa](troubleshoot-set-up-issues.md).
- Felsökning av problem när du konfigurerar en virtuell dator (VM) i virtuella Windows-skrivbordet beskrivs [Session Värdkonfiguration för virtuell dator](troubleshoot-vm-configuration.md).
- Felsökning av problem med virtuella skrivbord i Windows-klientanslutningar beskrivs [Remote Desktop-klientanslutningar](troubleshoot-client-connection.md).
- När du felsöker problem när du använder PowerShell med virtuella Windows-skrivbordet, se [Windows PowerShell för virtuella skrivbord](troubleshoot-powershell.md).
- Läs mer om förhandsversionen av tjänsten i [Windows Desktop förhandsversionsmiljön](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Om du vill gå igenom en självstudiekurs om felsökning finns i [självstudien: Felsöka malldistributioner för Resource Manager-](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Läs om granskning åtgärder i [granskningsåtgärder med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Mer information om åtgärder för att avgöra felen under distributionen, se [visa distributionsåtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).