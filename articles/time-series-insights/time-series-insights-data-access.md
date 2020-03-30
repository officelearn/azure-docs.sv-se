---
title: Konfigurera säkerhet för att bevilja dataåtkomst – förhandsversionen av Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du konfigurerar säkerhet, behörigheter och hantera dataåtkomstprinciper i förhandsversionen av Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 1c8f14bb1bca082a9d887e5d6d88aec213448c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254357"
---
# <a name="grant-data-access-to-an-environment"></a>Bevilja dataåtkomst till en miljö

I den här artikeln beskrivs de två typerna av azure time series insights preview-åtkomstprinciper.

> [!TIP]
> Läs [Autentisering och auktorisering](time-series-insights-authentication-and-authorization.md) för registreringssteg för Azure Active Directory-appar.

## <a name="sign-in-to-time-series-insights"></a>Logga in på Time Series Insights

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Hitta din time series insights-miljö. Ange `Time Series` i **sökrutan.** Välj **Tidsseriemiljö** i sökresultaten.
1. Välj Time Series Insights-miljön från listan.

## <a name="grant-data-access"></a>Bevilja åtkomst till data

Följ dessa steg för att bevilja dataåtkomst för ett användarobjekt.

1. Välj **Data Access-principer**och välj sedan **+ Lägg till**.

    [![Markera och lägga till en dataåtkomstprincip](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Välj **Välj användare**. Sök efter användarnamnet eller e-postadressen för att hitta den användare du vill lägga till. Välj **Välj** för att bekräfta markeringen.

    [![Välj en användare att lägga till](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Välj **Välj roll**. Välj lämplig åtkomstroll för användaren:

    * Välj **Deltagare** om du vill tillåta användaren att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer du **Läsare** så att användaren kan fråga data i miljön och spara personliga, inte delade, frågor i miljön.

   Välj **OK** för att bekräfta rollvalet.

    [![Bekräfta den valda rollen](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Välj **OK** på sidan **Välj användarroll.**

    [![Välj OK på sidan Välj användarroll](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Bekräfta att sidan **Dataåtkomstprinciper** visar användarna och rollerna för varje användare.

    [![Verifiera rätt användare och roller](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Ge gäståtkomst från en annan Azure AD-klientorganisation

Rollen `Guest` är inte en chefsroll. Det är en term som används för ett konto som är inbjudna från en klient till en annan. När gästkontot har bjudits in till klientens katalog kan det ha samma åtkomstkontroll som tillämpas på det som alla andra konton. Du kan bevilja hanteringsåtkomst till en Time Series Insights-miljö med hjälp av bladet Åtkomstkontroll (IAM). Du kan också bevilja åtkomst till data i miljön via bladet Data Access Policies. Mer information om Azure Active Directory (Azure AD) klientgäståtkomst finns [i Lägg till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Följ dessa steg för att ge gäståtkomst till en Time Series Insights-miljö till en Azure AD-användare från en annan klientorganisation.

1. Välj **Dataåtkomstprinciper**och välj sedan **+ Bjud in**.

    [![Välj Data Access-poliser och sedan + Bjud in](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Ange e-postadressen för den användare som du vill bjuda in. Den här e-postadressen måste associeras med Azure AD. Du kan också inkludera ett personligt meddelande med inbjudan.

    [![Ange e-postadressen för att hitta den valda användaren](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Leta efter bekräftelsebubblan som visas på skärmen.

    [![Leta efter bekräftelsebubblan som ska visas](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Välj **Välj användare**. Sök efter e-postadressen till gästanvändaren som du har bjudit in för att hitta den användare du vill lägga till. **Markera** sedan för att bekräfta markeringen.

    [![Markera användaren och bekräfta markeringen](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Välj **Välj roll**. Välj lämplig åtkomstroll för gästanvändaren:

    * Välj **Deltagare** om du vill tillåta användaren att ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer du **Läsare** så att användaren kan fråga data i miljön och spara personliga, inte delade, frågor i miljön.

   Välj **OK** för att bekräfta rollvalet.

    [![Bekräfta rollvalet](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Välj **OK** på sidan **Välj användarroll.**

1. Bekräfta att sidan **Dataåtkomstprinciper** visar gästanvändaren och rollerna för varje gästanvändare.

    [![Kontrollera att användare och roller har tilldelats korrekt](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Nu får gästanvändaren ett e-postmeddelande med en inbjudan till den e-postadress som anges ovan. Gästanvändaren väljer **Kom igång** för att bekräfta att de godkänns och ansluter till Azure Cloud.

    [![Gäst väljer Kom igång för att acceptera](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. När du har valt **Kom igång**visas gästanvändaren med en behörighetsruta som är associerad med administratörens organisation. När du beviljar tillstånd genom att välja **Acceptera**loggas de in.

    [![Gästrecensioner behörigheter och accepterar](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. Administratören [delar miljö-URL:en](time-series-insights-parameterized-urls.md) med sin gäst.

1. När gästanvändaren har loggat in på den e-postadress som du använde för att bjuda in dem, och de accepterar inbjudan, dirigeras de till Azure-portalen. 

1. Gästen kan nu komma åt den delade miljön med hjälp av miljö-URL:en som tillhandahålls av administratören. De kan ange webbadressen i sin webbläsare för omedelbar åtkomst.

1. Administratörens klient kommer att visas för gästanvändaren efter att ha valt deras profilikon i det övre högra hörnet av Tidsserieutforskaren.

    [![Avatar val på insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    När gästanvändaren har valt administratörens klientorganisation kan de välja den delade Time Series Insights-miljön. 
    
    De har nu alla funktioner i samband med den roll som du försåg dem med i **steg 5**.

    [![Gästanvändare väljer din Azure-klient från listrutan](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [lägger till en Azure Event Hubs-händelsekälla](./time-series-insights-how-to-add-an-event-source-eventhub.md) i din Time Series Insights-miljö.

* Skicka [händelser till händelsekällan](./time-series-insights-send-events.md).

* Visa [din miljö i förhandsutforskaren Förhandsgranskning av Time Series Insights](./time-series-insights-update-explorer.md).
