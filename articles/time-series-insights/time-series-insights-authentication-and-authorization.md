---
title: 'Så här att autentisera och auktorisera av API: et i Azure Time Series Insights | Microsoft Docs'
description: 'Den här artikeln beskriver hur du konfigurerar autentisering och auktorisering för ett anpassat program som anropar API: et för Azure Time Series Insights.'
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 5fb2802bfe9cc0a4d3297e6fa749e5b94008c616
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472651"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autentisering och auktorisering för Azure Time Series Insights API

Den här artikeln förklarar hur du konfigurerar autentisering och auktorisering som används i ett anpassat program som anropar API: et för Azure Time Series Insights.

> [!TIP]
> Läs mer om [beviljar åtkomst till data](./time-series-insights-data-access.md) till Time Series Insights-miljön i Azure Active Directory.

## <a name="service-principal"></a>Tjänstens huvudnamn

Det här och följande avsnitt beskrivs hur du konfigurerar ett program för att få åtkomst till API: T för Time Series Insights åt programmet. Programmet kan sedan fråga eller publicera referensdata i Time Series Insights-miljö med autentiseringsuppgifter för program i stället för användarens autentiseringsuppgifter.

## <a name="best-practices"></a>Regelverk

När du har ett program som måste åtkomst Time Series Insights:

1. Konfigurera en Azure Active Directory-app.
1. [Tilldela dataåtkomstprinciper](./time-series-insights-data-access.md) i Time Series Insights-miljö.

Det är önskvärt sedan att använda program i stället för dina autentiseringsuppgifter:

* Du kan tilldela behörigheter till appen identiteten som skiljer sig från dina egna behörigheter. Normalt är behörigheterna begränsade till bara vad appen kräver. Du kan till exempel Tillåt att appen endast kan läsa data i en viss Time Series Insights-miljö.
* Du behöver ändra appens autentiseringsuppgifter, ändrar dina ansvarsområden.
* Du kan använda ett certifikat eller en programnyckel för att automatisera autentisering när du kör ett oövervakat skript.

I följande avsnitt visar hur du utför de här stegen via Azure portal. Artikeln fokuserar på en enda klient program där programmet är avsett att köras i endast en organisation. Du använder normalt enda klient program för line-of-business-program som körs i din organisation.

## <a name="set-up-summary"></a>Konfigurera sammanfattning

Installationsprogrammet flödet består av tre steg:

1. Skapa ett program i Azure Active Directory.
1. Tillåter programmet att få åtkomst till Time Series Insights-miljö.
1. Använd program-ID och nyckel för att hämta en token från `https://api.timeseries.azure.com/`. Token kan sedan användas för att anropa API: T för Time Series Insights.

## <a name="detailed-setup"></a>Ytterligare inställningar

1. I Azure-portalen väljer du **Azure Active Directory** > **appregistreringar** > **ny programregistrering**.

   [![Ny programregistrering i Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Ge programmet ett namn, Välj den typ som ska vara **webbapp / API**, Välj en giltig URI för **inloggnings-URL**, och klicka på **skapa**.

   [![Skapa programmet i Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Välj ditt nya program och kopiera dess program-ID till valfri textredigerare.

   [![Kopiera program-ID](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Välj **nycklar**, ange nyckelnamnet, Välj förfallodatum och klicka på **spara**.

   [![Välj nycklar för programmet](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Ange namn och upphör att gälla och klicka på Spara](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Kopiera nyckeln till valfri textredigerare.

   [![Kopiera programnyckeln](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Time Series Insights-miljö, Välj **Dataåtkomstprinciper** och klicka på **Lägg till**.

   [![Lägg till ny policy för åtkomst till Time Series Insights-miljö](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. I den **Välj användare** dialogrutan rutan, klistra in namnet på programmet (från **steg 2**) eller program-ID (från **steg 3**).

   [![Hitta ett program i dialogrutan Välj användare](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Välj rollen (**läsare** för att fråga efter data, **deltagare** för datafrågor och ändra referensdata) och klicka på **OK**.

   [![Välj läsare eller deltagare i dialogrutan Välj roll](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Spara principen genom att klicka på **OK**.

1. Använda program-ID (från **steg 3**) och programnyckel (från **steg 5**) att hämta token för programmet. Token kan sedan skickas i den `Authorization` rubrik när programmet anropar API: T för Time Series Insights.

    Om du använder C#, kan du använda följande kod för att hämta token för programmet. Ett komplett exempel finns i [fråga data med C#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

Använd den **program-ID** och **nyckeln** i din app för att autentisera med Azure Time Series Insight.

## <a name="next-steps"></a>Nästa steg

- Exempelkod som anropar API: T för Time Series Insights, se [fråga data med C#](time-series-insights-query-data-csharp.md).

- API-Referensinformation finns i [fråge-API-referens](/rest/api/time-series-insights/ga-query-api).

- Lär dig hur du [skapa ett huvudnamn för tjänsten](../active-directory/develop/howto-create-service-principal-portal.md).
