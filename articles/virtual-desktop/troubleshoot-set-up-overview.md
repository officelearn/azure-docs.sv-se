---
title: Felsökningsöversikt för Windows Virtual Desktop – Azure
description: En översikt över felsökningsproblem när du konfigurerar en windows virtual desktop-klientmiljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127397"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Översikt, feedback och support för felsökning

Den här artikeln innehåller en översikt över de problem som kan uppstå när du konfigurerar en windows virtual desktop-klientmiljö och innehåller sätt att lösa problemen.

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produktteamet och aktiva communitymedlemmar.

## <a name="escalation-tracks"></a>Eskalering spår

Använd följande tabell för att identifiera och lösa problem som kan uppstå när du konfigurerar en klientmiljö med fjärrskrivbordsklient. När din klient har konfigurerats kan du använda vår nya [diagnostiktjänst](diagnostics-role-service.md) för att identifiera problem för vanliga scenarier.

>[!NOTE]
> Vi har ett Forum för Tech Community som du kan besöka för att diskutera dina problem med produktteamet och aktiva communitymedlemmar. Gå till [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att starta en diskussion.

| **Problem**                                                            | **Föreslagen lösning**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Skapa en Windows Virtual Desktop-klientorganisation                                                    | Om det finns ett Azure-avbrott [öppnar du en Azure-supportbegäran.](https://azure.microsoft.com/support/create-ticket/) Annars [öppnar du en Azure-supportbegäran](https://azure.microsoft.com/support/create-ticket/), väljer Windows Virtual **Desktop** för tjänsten, väljer **Distribution** för problemtypen och väljer sedan Problem som skapar en Windows **Virtual Desktop-klient för** problemundertypen.|
| Komma åt Marketplace-mallar i Azure-portalen       | Om det finns ett Azure-avbrott [öppnar du en Azure-supportbegäran](https://azure.microsoft.com/support/create-ticket/). <br> <br> Azure Marketplace Windows Virtual Desktop-mallar är fritt tillgängliga.|
| Komma åt Azure Resource Manager-mallar från GitHub                                  | Se avsnittet [Skapa virtuella datorer för Windows Virtual Desktop-session i](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) [klient- och värdpoolverktyget](troubleshoot-set-up-issues.md). Om problemet fortfarande är olöst kontaktar du [GitHub-supportteamet](https://github.com/contact). <br> <br> Om felet uppstår efter åtkomst till mallen i GitHub kontaktar du [Azure Support](https://azure.microsoft.com/support/create-ticket/).|
| Inställningar för sessionsvärdpoolen Azure Virtual Network (VNET) och Express Route               | [Öppna en Azure-supportbegäran](https://azure.microsoft.com/support/create-ticket/)och välj sedan lämplig tjänst (under kategorin Nätverk). |
| Skapa skapande av virtuell dator (VM) för session värdpool när Azure Resource Manager-mallar som medföljer Windows Virtual Desktop inte används | [Öppna en Azure-supportbegäran](https://azure.microsoft.com/support/create-ticket/)och välj sedan **Virtuell dator som kör Windows** för tjänsten. <br> <br> Problem med Azure Resource Manager-mallarna som medföljer Windows Virtual Desktop finns i Skapa Windows Virtual Desktop-klientavsnittet för [klient- och värdpoolverktyget](troubleshoot-set-up-issues.md). |
| Hantera värdmiljö för Windows Virtual Desktop-session från Azure-portalen    | [Öppna en Azure-supportbegäran](https://azure.microsoft.com/support/create-ticket/). <br> <br> Hanteringsproblem när du använder PowerShell för fjärrskrivbordstjänster/Windows Virtuella skrivbord finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) eller [öppna en Azure-supportbegäran](https://azure.microsoft.com/support/create-ticket/)väljer du **Windows Virtual Desktop** för tjänsten, väljer Konfiguration **och hantering** för problemtypen och väljer sedan Problem med att konfigurera klient **med PowerShell** för problemundertypen. |
| Hantera Windows Virtual Desktop-konfiguration som är knuten till värdpooler och programgrupper (appgrupper)      | Se [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)eller öppna en [Azure-supportbegäran](https://azure.microsoft.com/support/create-ticket/), välj **Windows Virtual Desktop** för tjänsten och välj sedan lämplig problemtyp.|
| Distribuera och hantera FSLogix-profilbehållare | Se [Felsökningsguide för FSLogix-produkter](/fslogix/fslogix-trouble-shooting-ht/) och om det inte löser [problemet, Öppna en Azure-supportbegäran](https://azure.microsoft.com/support/create-ticket/), välj **Windows Virtual Desktop** för tjänsten, välj **FSLogix** för problemtypen och välj sedan lämplig problemundertyp. |
| Fjärrskrivbordsklienter krånglar vid start                                                 | Se [Felsöka fjärrskrivbordsklienten](troubleshoot-client.md) och om det inte löser [problemet, Öppna en Azure-supportbegäran](https://azure.microsoft.com/support/create-ticket/), välj **Windows Virtual Desktop** för tjänsten och välj sedan **Fjärrskrivbordsklienter** för problemtypen.  <br> <br> Om det är ett nätverksproblem måste användarna kontakta nätverksadministratören. |
| Ansluten men ingen matning                                                                 | Felsöka med hjälp av [användaren ansluter men ingenting visas (ingen feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) i Windows Virtual Desktop [service-anslutningar](troubleshoot-service-connection.md). <br> <br> Om användarna har tilldelats en appgrupp [öppnar du en Azure-supportbegäran](https://azure.microsoft.com/support/create-ticket/), väljer **Windows Virtual Desktop** för tjänsten och väljer sedan **Fjärrskrivbordsklienter** för problemtypen. |
| Problem med identifiering av flöden på grund av nätverket                                            | Användarna måste kontakta sin nätverksadministratör. |
| Ansluta klienter                                                                    | Se [Windows Virtual Desktop-tjänstanslutningar](troubleshoot-service-connection.md) och om det inte löser problemet läser du [Konfigurationen för virtuell dator för session värd](troubleshoot-vm-configuration.md). |
| Responsivitet för fjärrprogram eller skrivbord                                      | Om problem är kopplade till ett visst program eller en viss produkt kontaktar du det team som ansvarar för produkten. |
| Licensiera meddelanden eller fel                                                          | Om problem är kopplade till ett visst program eller en viss produkt kontaktar du det team som ansvarar för produkten. |
| Problem när du använder Windows Virtual Desktop-verktyg på GitHub (Azure Resource Manager-mallar, diagnostikverktyg, hanteringsverktyg) | Se [Azure Resource Manager-mallar för fjärrskrivbordstjänster](https://github.com/Azure/RDS-Templates/blob/master/README.md) för att rapportera problem. |

## <a name="next-steps"></a>Nästa steg

- Information om hur du felsöker problem när du skapar en klient- och värdpool i en Windows Virtual Desktop-miljö finns i [Skapandet av klient- och värdpooler](troubleshoot-set-up-issues.md).
- Mer om du vill felsöka problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [Konfigurationen för virtuell dator för session värd](troubleshoot-vm-configuration.md).
- Mer om du vill felsöka problem med Windows Virtual Desktop-klientanslutningar finns i [Windows Virtual Desktop-tjänstanslutningar](troubleshoot-service-connection.md).
- Om du vill felsöka problem med fjärrskrivbordsklienter finns i [Felsöka klienten för fjärrskrivbord](troubleshoot-client.md)
- Mer om du vill felsöka problem när du använder PowerShell med Virtuellt Windows-skrivbord finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup.md).
- Information om hur du går igenom en felsökningsguide finns i [Självstudiekurs: Felsöka Resource Manager-malldistributioner](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om granskningsåtgärder finns i [Granskningsåtgärder med Resurshanteraren](../azure-resource-manager/management/view-activity-logs.md).
- Mer information om åtgärder för att fastställa fel under distributionen finns i [Visa distributionsåtgärder](../azure-resource-manager/templates/deployment-history.md).
