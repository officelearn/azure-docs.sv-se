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
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 0f0de122dc3dbd770e91a8412430423bee222b30
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577978"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Så här: återaktivera en inaktiverad Access Control Service-namnområden

November 2017 meddelade vi att Microsoft Azure Access Control Service (ACS), en tjänst i Azure Active Directory (Azure AD), tas ur bruk 7 November 2018.

Eftersom sedan vi har skickat e-postmeddelande till ACS-prenumerationer administratörens e-post om ACS indragningen 12 månader, 9 månaderna, sex månader tre månader, 1 månad, 2 veckor, 1 vecka, och en dag före datumet för tillbakadragandet 7 November 2018.

3 oktober 2018 meddelade vi (via e-post och [ett blogginlägg](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) ett tillägg erbjudande till kunder som det går inte att slutföra migreringen innan den 7 November 2018. Meddelandet hade också instruktioner för att begära tillägget.

## <a name="why-your-namespace-is-disabled"></a>Varför ditt namnområde är inaktiverad

Om du inte har valt i för tillägget, börjar vi att inaktivera ACS-namnområden från och med 7 November 2018. Om du vill anmäla dig till tillägg till 4 februari 2019 och görs ett avdrag kommunikationen följer du anvisningarna i följande avsnitt.

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

        ```
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

1. Gå till namnområdet ACS-hanteringsportalen genom att gå till `https://{your-namespace}.accesscontrol.windows.net`.
1. Välj den **Läs villkoren** knappen för att läsa den [uppdateras användningsvillkor](https://azure.microsoft.com/support/legal/access-control/), som omdirigerar dig till en sida med uppdaterade villkor för användning.

    [![Välj knappen Läs villkoren](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Välj **begär tillägg** på banderollen högst upp på sidan. Knappen aktiveras endast när du har läst den [uppdateras användningsvillkor](https://azure.microsoft.com/support/legal/access-control/).

    [![Välj knappen begär tillägg](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. När förfrågan för domännamnstillägget har registrerats kan uppdateras sidan med en ny banderoll överst på sidan.

    [![Uppdaterade sidan med uppdateras banderoll](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>Hjälp och support

- Om du stöter på problem när du har genomfört den här anvisningen, kontakta [Azure-supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Om du har frågor eller feedback om ACS dras tillbaka, kontaktar du oss på acsfeedback@microsoft.com.

## <a name="next-steps"></a>Nästa steg

- Granska informationen om ACS dras tillbaka i [så här: migrera från Azure Access Control Service](active-directory-acs-migration.md).
