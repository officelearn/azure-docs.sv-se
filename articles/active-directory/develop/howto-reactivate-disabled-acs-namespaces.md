---
title: Återaktivera inaktiverade namnområden som Azure Access Control Service (ACS)
description: Lär dig hur du hittar och aktivera dina Azure Access Control Service (ACS)-namnområden och begära en förlängning så att de aktiverade tills 4 februari 2019.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 085923dd124a4f973a709f0e59a07ad4137c6901
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438512"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Anvisningar: Återaktivera inaktiverade Access Control Service-namnområden

November 2017 meddelade vi att Microsoft Azure Access Control Service (ACS), en tjänst i Azure Active Directory (Azure AD), tas ur bruk 7 November 2018.

Eftersom sedan vi har skickat e-postmeddelande till ACS-prenumerationer administratörens e-post om ACS indragningen 12 månader, 9 månaderna, sex månader tre månader, 1 månad, 2 veckor, 1 vecka, och en dag före datumet för tillbakadragandet 7 November 2018.

3 oktober 2018 meddelade vi (via e-post och [ett blogginlägg](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) ett tillägg erbjudande till kunder som det går inte att slutföra migreringen innan den 7 November 2018. Meddelandet hade också instruktioner för att begära tillägget.

## <a name="why-your-namespace-is-disabled"></a>Varför ditt namnområde är inaktiverad

Om du inte har valt i för tillägget, börjar vi att inaktivera ACS-namnområden från och med 7 November 2018. Du måste har begärt anknytningen till 4 februari 2019 redan; i annat fall kommer du inte att kunna aktivera namnområden via PowerShell.

> [!NOTE]
> Du måste vara en tjänstadministratör eller medadministratör för prenumerationen för att köra PowerShell-kommandon och begära en förlängning.

## <a name="find-and-enable-your-acs-namespaces"></a>Hitta och aktivera ACS-namnområden

Du kan använda ACS PowerShell för att lista alla ACS-namnområden och återaktivera de som har inaktiverats.

1. Hämta och installera ACS PowerShell:
    1. Gå till PowerShell-galleriet och hämta [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Installera modulen:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Hämta en lista över alla möjliga kommandon:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Om du behöver hjälp med ett visst kommando kör du:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        där `[Command-Name]` är namnet på ACS-kommandot.
1. Anslut till ACS med hjälp av den **Connect AcsAccount** cmdlet. 

    Du kan behöva ändra dina körningsprincipen genom att köra **Set-ExecutionPolicy** innan du kan köra kommandot.
1. Lista över dina tillgängliga Azure-prenumerationer med hjälp av den **Get-AcsSubscription** cmdlet.
1. Lista din ACS namnområden med hjälp av den **Get-AcsNamespace** cmdlet.
1. Bekräfta att namnområden är inaktiverade som bekräftar att `State` är `Disabled`.

    [![Bekräfta att namnområden är inaktiverade](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Du kan också använda `nslookup {your-namespace}.accesscontrol.windows.net` att bekräfta om domänen är fortfarande är aktiv.

1. Aktivera din ACS namespace(s) med hjälp av den **aktivera AcsNamespace** cmdlet.

    När du har aktiverat din namespace(s) kan begära du en förlängning så att namespace(s) inte inaktiveras igen innan den 4 februari 2019. Efter detta datum kommer alla förfrågningar till ACS kommer att misslyckas.

## <a name="request-an-extension"></a>Begära en förlängning

Vi tar nya begäranden från och med den 21 januari 2019.

Vi startar inaktiverar namnområden för kunder som har begärt tillägg till 4 februari 2019. Du kan fortfarande återaktivera namnområden via PowerShell, men namnrymder inaktiveras igen efter 48 timmar.

Efter 4 mars 2019 kommer kunder inte längre att kunna aktivera alla namnområden via PowerShell.

Ytterligare tillägg kommer inte längre att godkännas automatiskt. Om du behöver mer tid för att migrera Kontakta [Azure-supporten](https://portal.azure.com/#create/Microsoft.Support) att ge en detaljerad migreringstidsplan.

### <a name="to-request-an-extension"></a>Att begära en förlängning

1. Logga in på Azure-portalen och skapa en [ny supportbegäran](https://portal.azure.com/#create/Microsoft.Support).
1. Fyll i formuläret för begäran av nytt stöd som du ser i följande exempel.

    | Stöd för begäran fält | Värde |
    |-----------------------|--------------------|
    | **Typ av problem** | `Technical` |
    | **Prenumeration** | Ställ in till din prenumeration |
    | **Tjänst** | `All services` |
    | **Resurs** | `General question/Resource not available` |
    | **Problemtyp** | `ACS to SAS Migration` |
    | **Ämne** | Beskriv problemet |

  ![Ny förfrågan om teknisk support](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

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

- Om du stöter på problem när du har genomfört den här anvisningen, kontakta [Azure-supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Om du har frågor eller feedback om ACS dras tillbaka, kontaktar du oss på acsfeedback@microsoft.com.

## <a name="next-steps"></a>Nästa steg

- Granska informationen om ACS dras tillbaka i [så här: Migrera från Azure Access Control Service](active-directory-acs-migration.md).
