---
title: Översikt över fel sökning av Windows Virtual Desktop – Azure
description: En översikt för fel sökning av problem när du konfigurerar en Windows-miljö för virtuella Skriv bords klienter.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
ms.openlocfilehash: dc4e4e271ac7108f4c29c82646e9849b9a5cd929
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110780"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Översikt, feedback och support för felsökning

Den här artikeln innehåller en översikt över de problem som du kan stöta på när du konfigurerar en Windows-klient för virtuella skriv bord och ger lösningar på problemen.

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows-Tech-communityn för Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produkt teamet och aktiva community-medlemmar.

## <a name="escalation-tracks"></a>Eskalerade spår

Använd följande tabell för att identifiera och lösa problem som kan uppstå när du konfigurerar en klient miljö med hjälp av fjärr skrivbords klienten. När din klient organisation har konfigurerat kan du använda vår nya [diagnostik-tjänst](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) för att identifiera problem med vanliga scenarier.

>[!NOTE]
> Vi har ett Tech community-forum som du kan använda för att diskutera dina problem med produkt teamet och aktiva community-medlemmar. Besök [Windows-Tech-communityn](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att starta en diskussion.

| **Ge**                                                            | **Föreslagen lösning**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Skapa en Windows-klient för virtuella skriv bord                                                    | Om det uppstår ett Azure-avbrott [öppnar du en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/). [Öppna annars en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), Välj **Windows Virtual Desktop** för tjänsten, Välj **distribution** för problem typen och välj sedan **problem som skapar en Windows-klient för virtuella skriv bord** för problem under typen.|
| Åtkomst till Marketplace-mallar i Azure Portal       | Om det uppstår ett Azure-avbrott [öppnar du en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Windows Virtual Desktop-mallar för Azure Marketplace är tillgängliga på ett kostnads fritt.|
| Få åtkomst till Azure Resource Manager mallar från GitHub                                  | Se avsnittet [skapa virtuella datorer för virtuella skriv bord](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) för fjärrskrivbordssession i [klient-och anslutningspoolen](troubleshoot-set-up-issues.md). Kontakta [GitHub support-teamet](https://github.com/contact)om problemet fortfarande inte är löst. <br> <br> Kontakta [Azure-supporten](https://azure.microsoft.com/support/create-ticket/)om felet uppstår när du har åtkomst till mallen i GitHub.|
| Session Host pool Azure-Virtual Network (VNET) och Express Route-inställningar               | [Öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/)och välj lämplig tjänst (under kategorin nätverk). |
| Skapa virtuell dator för Session Host pool (VM) när Azure Resource Manager mallar som medföljer Windows Virtual Desktop används inte | [Öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/)och välj sedan **virtuell dator som kör Windows** för tjänsten. <br> <br> Om du har problem med Azure Resource Manager mallar som medföljer Windows Virtual Desktop kan du läsa avsnittet Skapa Windows-klient för virtuella skriv bord för [klient organisation och skapa värddator](troubleshoot-set-up-issues.md). |
| Hantera Windows virtuell skrivbord värd miljö från Azure Portal    | [Öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> För hanterings problem när du använder Fjärrskrivbordstjänster/Windows Virtual Desktop PowerShell, se [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) eller [öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), Välj **Windows Virtual Desktop** för tjänsten, Välj **konfiguration och hantering** för problem typen och välj sedan **problem med att konfigurera klient med PowerShell** som underordnat problem. |
| Hantera konfiguration av virtuella Windows-skrivbordet knutna till värdar och program grupper (app-grupper)      | Se [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)eller [öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), Välj **Windows Virtual Desktop** för tjänsten och välj sedan lämplig problem typ.|
| Distribuera och hantera FSLogix profil behållare | Se [fel söknings guide för FSLogix-produkter](https://docs.microsoft.com/fslogix/fslogix-trouble-shooting-ht) och om det inte löser [problemet öppnar du en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), väljer **Windows Virtual Desktop** för tjänsten, väljer **FSLogix** för problem typen och väljer sedan motsvarande typ av problem. |
| Fjärr skrivbords klienter fungerar inte på Start                                                 | Se [Felsöka fjärr skrivbords klienten](troubleshoot-client.md) och om det inte löser problemet [öppnar du en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), väljer **Windows Virtual Desktop** för tjänsten och väljer sedan **fjärr skrivbords klienter** för problem typen.  <br> <br> Om det är ett nätverks problem måste användarna kontakta sin nätverks administratör. |
| Ansluten men ingen feed                                                                 | Felsök med hjälp av [användaren ansluter men ingenting visas (ingen feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) i [Windows Virtual Desktop Service-anslutningar](troubleshoot-service-connection.md). <br> <br> Om dina användare har tilldelats en app-grupp öppnar du [en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), väljer **Windows Virtual Desktop** för tjänsten och väljer sedan **fjärr skrivbords klienter** för problem typen. |
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
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Information om åtgärder för att fastställa fel under distributionen finns i [Visa distributions åtgärder](../azure-resource-manager/templates/deployment-history.md).
