---
title: Översikt över fel sökning av virtuella Windows-datorer, feedback och support – Azure
description: En översikt för fel sökning av problem när du konfigurerar en Windows-miljö för virtuella Skriv bords klienter.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: a31750d888410b9bdaea28bc5153152c426f1915
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877548"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Översikt, feedback och support för felsökning

Den här artikeln innehåller en översikt över de problem som du kan stöta på när du konfigurerar en Windows-klient för virtuella skriv bord och ger lösningar på problemen.

## <a name="provide-feedback"></a>Ge feedback

Vi tar för närvarande inte support ärenden när Windows Virtual Desktop är i för hands version. Besök [Windows-Tech](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) -communityn för Windows för att diskutera Windows Virtual Desktop-tjänsten med produkt teamet och aktiva community-medlemmar.

## <a name="escalation-tracks"></a>Eskalerade spår

Använd följande tabell för att identifiera och lösa problem som kan uppstå när du konfigurerar en klient miljö med hjälp av fjärr skrivbords klienten. När din klient organisation har konfigurerat kan du använda vår nya [diagnostik-tjänst](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) för att identifiera problem med vanliga scenarier.

>[!NOTE]
>Vi tar för närvarande inte support ärenden när Windows Virtual Desktop är i för hands version. När vi refererar till stöd för virtuella Windows-datorer går du till vårt Tech community-forum för tillfället. Besök Tech-communityn för [Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera problem med produkt teamet och aktiva community-medlemmar. Om du behöver lösa ett support ärende ska du inkludera aktivitets-ID och ungefärlig tidsram för när problemet uppstod.

| **Ge**                                                            | **Föreslagen lösning**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Skapa en klient                                                    | Kontakta [Azure](https://azure.microsoft.com/support/options/)-supporten om det uppstår ett Azure-avbrott. Annars kontaktar du **Fjärrskrivbordstjänster/Windows stöd för virtuella skriv bord**.|
| Åtkomst till Marketplace-mallar i Azure Portal       | Om det uppstår ett Azure-avbrott kan du kontakta [Azure](https://azure.microsoft.com/support/options/)-supporten. <br> <br> Windows Virtual Desktop-mallar för Azure Marketplace är tillgängliga på ett kostnads fritt.|
| Få åtkomst till Azure Resource Manager mallar från GitHub                                  | Se avsnittet "skapa virtuella datorer med virtuella skriv bord för fjärrskrivbordssession" i [klient-och anslutningspoolen](troubleshoot-set-up-issues.md). Kontakta [GitHub support-teamet](https://github.com/contact)om problemet fortfarande inte är löst. <br> <br> Kontakta [Azure](https://azure.microsoft.com/support/options/)-supporten om felet uppstår när du har åtkomst till mallen i GitHub.|
| Session Host pool Azure-Virtual Network (VNET) och Express Route-inställningar               | Kontakta **Azure-supporten (nätverk)** . |
| Skapa virtuell dator för Session Host pool (VM) när Azure Resource Manager mallar som medföljer Windows Virtual Desktop används inte | Kontakta **Azure-supporten (Compute)** . <br> <br> Om du har problem med Azure Resource Manager mallar som medföljer Windows Virtual Desktop kan du läsa avsnittet Skapa Windows-klient för virtuella skriv bord för [klient organisation och skapa värddator](troubleshoot-set-up-issues.md). |
| Hantera Windows virtuell skrivbord värd miljö från Azure Portal    | Kontakta **Azure**-supporten. <br> <br> För hanterings problem när du använder Fjärrskrivbordstjänster/Windows Virtual Desktop PowerShell, se [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) eller kontakta **support teamet för Fjärrskrivbordstjänster/Windows-support för virtuella skriv bord**. |
| Hantera konfiguration av virtuella Windows-skrivbordet knutna till värdar och program grupper (app-grupper)      | Se [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)eller kontakta **support teamet för Fjärrskrivbordstjänster/Windows Virtual Desktop**. <br> <br> Om problemen är knutna till det grafiska användar gränssnittet i exemplet kan du kontakta Yammer-communityn.|
| Fjärr skrivbords klienter fungerar inte på Start                                                 | Se [fjärr skrivbords klient anslutningar](troubleshoot-client-connection.md) och om det inte löser problemet kontaktar du **Fjärrskrivbordstjänster/Windows Virtual Desktop support team**.  <br> <br> Om det är ett nätverks problem måste användarna kontakta sin nätverks administratör. |
| Ansluten men ingen feed                                                                 | Felsök med hjälp av avsnittet "användaren ansluter men ingenting visas (ingen feed)" i [fjärr skrivbords klient anslutningar](troubleshoot-client-connection.md). <br> <br> Om dina användare har tilldelats en app-grupp eskalerar du till **support teamet Fjärrskrivbordstjänster/Windows Virtual Desktop**. |
| Problem med identifiering av feed på grund av nätverket                                            | Användarna måste kontakta sin nätverks administratör. |
| Ansluta klienter                                                                    | Se [fjärr skrivbords klient anslutningar](troubleshoot-client-connection.md) och om det inte löser problemet kan du se [konfiguration av virtuell dator i Session Host](troubleshoot-vm-configuration.md). |
| Svars tider för fjärrprogram eller skriv bord                                      | Om problemen är knutna till en specifik applikation eller produkt kontaktar du det team som ansvarar för produkten. |
| Licensierings meddelanden eller fel                                                          | Om problemen är knutna till en specifik applikation eller produkt kontaktar du det team som ansvarar för produkten. |

## <a name="next-steps"></a>Nästa steg

- Information om hur du felsöker problem när du skapar en klient och en adresspool i en Windows Virtual Desktop-miljö finns i [skapa innehavare och skapa värdar för pooler](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem med klient anslutningar för virtuella Windows-datorer finns i [fjärr skrivbords klient anslutningar](troubleshoot-client-connection.md).
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Mer information om för hands versions tjänsten finns i [Windows Virtual Desktop Preview Environment](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Information om hur du går igenom en fel [söknings kurs finns i Självstudier: Felsök distributioner](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)av Resource Manager-mallar.
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Information om åtgärder för att fastställa felen under distributionen finns i [Visa distributions åtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).