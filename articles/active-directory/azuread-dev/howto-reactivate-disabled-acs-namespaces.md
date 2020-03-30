---
title: Återaktivera acs-namnområden (Disabled Access Control Service)
description: Hitta och aktivera dina NAMNområden för Azure Access Control Service (ACS) och begär ett tillägg för att hålla dem aktiverade fram till den 4 februari 2019.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ff0ce05b13fea8409475e3415c5d810d7c79769a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154873"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Så här: Återaktivera namnområden för tjänsten För åtkomstkontroll

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Den 7 november 2017 meddelade vi att Microsoft Azure Access Control Service (ACS), en tjänst i Azure Active Directory (Azure AD), dras tillbaka den 7 november 2018.

Sedan dess har vi skickat e-post till ACS-prenumerationernas admin-e-post om ACS-pensioneringen 12 månader, 9 månader, 6 månader, 3 månader, 1 månad, 2 veckor, 1 vecka och 1 dag före pensioneringsdatumet den 7 november 2018.

Den 3 oktober 2018 meddelade vi (via e-post och [ett blogginlägg](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) ett tilläggserbjudande till kunder som inte kan slutföra sin migrering före den 7 november 2018. Tillkännagivandet hade också instruktioner för att begära förlängning.

## <a name="why-your-namespace-is-disabled"></a>Varför namnområdet är inaktiverat

Om du inte har valt tillägget börjar vi inaktivera ACS-namnområden från och med den 7 november 2018. Du måste ha begärt förlängningen till den 4 februari 2019 redan. Annars kan du inte aktivera namnområdena via PowerShell.

> [!NOTE]
> Du måste vara tjänstadministratör eller medadministratör för prenumerationen för att kunna köra PowerShell-kommandona och begära ett tillägg.

## <a name="find-and-enable-your-acs-namespaces"></a>Söka efter och aktivera dina ACS-namnområden

Du kan använda ACS PowerShell för att lista alla ACS-namnområden och återaktivera dem som har inaktiverats.

1. Ladda ned och installera ACS PowerShell:
    1. Gå till PowerShell-galleriet och hämta [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Installera modulen:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Få en lista över alla möjliga kommandon:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Om du vill ha hjälp med ett visst kommando kör du:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        där `[Command-Name]` är namnet på ACS-kommandot.
1. Anslut till ACS med **cmdlet connect-acsaccount.** 

    Du kan behöva ändra körningsprincipen genom att köra **Set-ExecutionPolicy** innan du kan köra kommandot.
1. Lista dina tillgängliga Azure-prenumerationer med hjälp av **cmdleten Get-AcsSubscription.**
1. Lista dina ACS-namnområden med cmdleten **Get-AcsNamespace.**
1. Bekräfta att namnområdena är inaktiverade `State` `Disabled`genom att bekräfta att det är .

    [![Bekräfta att namnområdena är inaktiverade](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Du kan `nslookup {your-namespace}.accesscontrol.windows.net` också använda för att bekräfta om domänen fortfarande är aktiv.

1. Aktivera acs-namnområden med hjälp av cmdleten **Enable-AcsNamespace.**

    När du har aktiverat dina namnområden kan du begära ett tillägg så att namnområdet inte inaktiveras igen före den 4 februari 2019. Efter detta datum misslyckas alla begäranden till ACS.

## <a name="request-an-extension"></a>Begär ett tillägg

Vi tar nya förlängningsförfrågningar från och med den 21 januari 2019.

Vi kommer att börja inaktivera namnområden för kunder som har begärt tillägg till den 4 februari 2019. Du kan fortfarande återaktivera namnområden via PowerShell, men namnområdena inaktiveras igen efter 48 timmar.

Efter den 4 mars 2019 kan kunderna inte längre återaktivera några namnområden via PowerShell.

Ytterligare tillägg kommer inte längre att godkännas automatiskt. Om du behöver ytterligare tid att migrera kontaktar du [Azure-supporten](https://portal.azure.com/#create/Microsoft.Support) för att tillhandahålla en detaljerad migreringstidslinje.

### <a name="to-request-an-extension"></a>Så här begär du ett tillägg

1. Logga in på Azure-portalen och skapa en [ny supportbegäran](https://portal.azure.com/#create/Microsoft.Support).
1. Fyll i det nya formuläret för supportbegäran enligt följande exempel.

    | Fältet Supportbegäran | Värde |
    |-----------------------|--------------------|
    | **Typ av problem** | `Technical` |
    | **Prenumeration** | Ställ in på din prenumeration |
    | **Tjänst** | `All services` |
    | **Resurs** | `General question/Resource not available` |
    | **Typ av problem** | `ACS to SAS Migration` |
    | **Subjekt** | Beskriv problemet |

   ![Visar ett exempel på en ny begäran om teknisk support](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Hjälp och support

- Om du stöter på några problem efter att ha följt den här inkörningen kontaktar du [Azure-supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Om du har frågor eller feedback om acsfeedback@microsoft.comACS pensionering, kontakta oss på .

## <a name="next-steps"></a>Nästa steg

- Granska informationen om ACS-pensionering i [Så här migrerar du från Azure Access Control Service](active-directory-acs-migration.md).
