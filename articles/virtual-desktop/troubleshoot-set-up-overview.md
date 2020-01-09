---
title: Översikt över fel sökning av Windows Virtual Desktop – Azure
description: En översikt för fel sökning av problem när du konfigurerar en Windows-miljö för virtuella Skriv bords klienter.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/06/2019
ms.author: helohr
ms.openlocfilehash: 817cd09797ad861521a462da9c5d0850c65bf562
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459584"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Översikt, feedback och support för felsökning

Den här artikeln innehåller en översikt över de problem som du kan stöta på när du konfigurerar en Windows-klient för virtuella skriv bord och ger lösningar på problemen.

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows-Tech-communityn för Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produkt teamet och aktiva community-medlemmar.

## <a name="escalation-tracks"></a>Eskalerade spår

Använd följande tabell för att identifiera och lösa problem som kan uppstå när du konfigurerar en klient miljö med hjälp av fjärr skrivbords klienten. När din klient organisation har konfigurerat kan du använda vår nya [diagnostik-tjänst](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) för att identifiera problem med vanliga scenarier.

>[!NOTE]
> Vi har ett Tech community-forum som du kan använda för att diskutera dina problem med produkt teamet och aktiva community-medlemmar. Besök [Windows-Tech-communityn för Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att 

| **Problem**                                                            | **Föreslagen lösning**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Skapa en klient                                                    | Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/)om det uppstår ett Azure-avbrott. **Öppna annars en support förfrågan om Windows Virtual Desktop (Compute)** .|
| Åtkomst till Marketplace-mallar i Azure Portal       | Om det uppstår ett Azure-avbrott kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/). <br> <br> Windows Virtual Desktop-mallar för Azure Marketplace är tillgängliga på ett kostnads fritt.|
| Få åtkomst till Azure Resource Manager mallar från GitHub                                  | Se avsnittet "skapa virtuella datorer med virtuella skriv bord för fjärrskrivbordssession" i [klient-och anslutningspoolen](troubleshoot-set-up-issues.md). Kontakta [GitHub support-teamet](https://github.com/contact)om problemet fortfarande inte är löst. <br> <br> Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/)om felet uppstår när du har åtkomst till mallen i GitHub.|
| Session Host pool Azure-Virtual Network (VNET) och Express Route-inställningar               | Kontakta **Azure-supporten (nätverk)** . |
| Skapa virtuell dator för Session Host pool (VM) när Azure Resource Manager mallar som medföljer Windows Virtual Desktop används inte | Kontakta **Azure-supporten (Compute)** . <br> <br> Om du har problem med Azure Resource Manager mallar som medföljer Windows Virtual Desktop kan du läsa avsnittet Skapa Windows-klient för virtuella skriv bord för [klient organisation och skapa värddator](troubleshoot-set-up-issues.md). |
| Hantera Windows virtuell skrivbord värd miljö från Azure Portal    | Kontakta **Azure-supporten**. <br> <br> För hanterings problem när du använder Fjärrskrivbordstjänster/Windows Virtual Desktop PowerShell, se [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) eller **öppna en support förfrågan för Windows Virtual Desktop (Compute)** . |
| Hantera konfiguration av virtuella Windows-skrivbordet knutna till värdar och program grupper (app-grupper)      | Se [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)eller **öppna en support förfrågan för Windows Virtual Desktop (Compute)** . <br> <br> Om problemen är knutna till det grafiska användar gränssnittet i exemplet kan du kontakta Yammer-communityn.|
| Fjärr skrivbords klienter fungerar inte på Start                                                 | Mer information finns i [Felsöka fjärr skrivbords klienten](troubleshoot-client.md) och om det inte löser problemet **öppnar du en support förfrågan för Windows Virtual Desktop (Compute)** .  <br> <br> Om det är ett nätverks problem måste användarna kontakta sin nätverks administratör. |
| Ansluten men ingen feed                                                                 | Felsök med avsnittet "användaren ansluter men ingenting visas (ingen feed)" i [Felsöka fjärr skrivbords klienten](troubleshoot-client.md). <br> <br> Om dina användare har tilldelats en app-grupp öppnar du **en supportbegäran för Windows Virtual Desktop (Compute)** . |
| Problem med identifiering av feed på grund av nätverket                                            | Användarna måste kontakta sin nätverks administratör. |
| Ansluta klienter                                                                    | Se [Windows Virtual Desktop Service-anslutningar](troubleshoot-service-connection.md) och om det inte löser problemet kan du läsa [konfiguration av virtuell dator i Session Host](troubleshoot-vm-configuration.md). |
| Svars tider för fjärrprogram eller skriv bord                                      | Om problemen är knutna till en specifik applikation eller produkt kontaktar du det team som ansvarar för produkten. |
| Licensierings meddelanden eller fel                                                          | Om problemen är knutna till en specifik applikation eller produkt kontaktar du det team som ansvarar för produkten. |
| Problem när du använder Windows-verktyg för virtuella skriv bord på GitHub (Azure Resource Manager mallar, diagnostikverktyg, hanterings verktyg) | Se [Azure Resource Manager mallar för Fjärrskrivbordstjänster](https://github.com/Azure/RDS-Templates/blob/master/README.md) att rapportera problem. |

## <a name="next-steps"></a>Nästa steg

- Information om hur du felsöker problem när du skapar en klient och en adresspool i en Windows Virtual Desktop-miljö finns i [skapa innehavare och skapa värdar för pooler](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem med klient anslutningar för virtuella Windows-datorer finns i [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md).
- Information om hur du felsöker problem med fjärr skrivbords klienter finns i [Felsöka fjärr skrivbords klienten](troubleshoot-client.md)
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../azure-resource-manager/resource-manager-tutorial-troubleshoot.md).
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md).
- Information om åtgärder för att fastställa fel under distributionen finns i [Visa distributions åtgärder](../azure-resource-manager/resource-manager-deployment-operations.md).
