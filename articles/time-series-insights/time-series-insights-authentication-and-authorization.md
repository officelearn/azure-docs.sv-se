---
title: Autentisera och auktorisera med hjälp av ett API i Azure Time Series Insights | Microsoft Docs
description: 'Den här artikeln beskriver hur du konfigurerar autentisering och auktorisering för ett anpassat program som anropar API: et för Azure Time Series Insights.'
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543940"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autentisering och auktorisering för Azure Time Series Insights API

Det här dokumentet beskriver hur du registrerar en app i Azure Active Directory med det nya Azure Active Directory-bladet. Appar som är registrerade i Azure Active Directory kan du autentisera till och ha behörighet att använda API i Azure Time Series Insight som är associerade med en Time Series Insights-miljö.

## <a name="service-principal"></a>Tjänstens huvudnamn

I följande avsnitt beskrivs hur du konfigurerar ett program för att få åtkomst till API: T för Time Series Insights för en app. Programmet kan sedan fråga efter eller publicera referensdata i Time Series Insights-miljö med hjälp av sina egna program autentiseringsuppgifter via Azure Active Directory.

## <a name="summary-and-best-practices"></a>Sammanfattning och bästa praxis

Azure Active Directory app registrering flödet omfattar tre huvudsakliga steg.

1. [Registrera ett program](#azure-active-directory-app-registration) i Azure Active Directory.
1. Godkänna att programmet har [dataåtkomst i Time Series Insights-miljön](#granting-data-access).
1. Använd den **program-ID** och **Klienthemlighet** att hämta en token från `https://api.timeseries.azure.com/` i din [klientappen](#client-app-initialization). Token kan sedan användas för att anropa API: T för Time Series Insights.

Per **steg 3**, att ange dina autentiseringsuppgifter och dina program kan du:

* Tilldela behörigheter till appen identiteten som skiljer sig från dina egna behörigheter. Normalt är behörigheterna begränsade till bara vad appen kräver. Du kan till exempel tillåter appen att läsa data från en viss Time Series Insights-miljö.
* Isolera appsäkerhet från skapa användarens autentiseringsuppgifter genom att använda en **Klienthemlighet** eller säkerhetscertifikat. Därför är programmets autentiseringsuppgifter inte beroende av en viss användares autentiseringsuppgifter. Om användarens roll ändras kan kräver programmet inte nödvändigtvis nya autentiseringsuppgifter eller ytterligare konfiguration. Om användaren ändrar sitt lösenord, kommer all åtkomst till programmet inte kräver nya autentiseringsuppgifter eller nycklar.
* Köra ett oövervakat skript med hjälp av en **Klienthemlighet** eller security-certifikatet i stället för en viss användares autentiseringsuppgifter (att de behöver vara närvarande).
* Använda säkerhetscertifikat i stället för ett lösenord för att skydda åtkomsten till ditt Azure Time Series Insights API.

> [!IMPORTANT]
> Följ principen om **angående rollseparering** (beskrivs i det här scenariot ovan) när konfigurera din säkerhetsprincip i Azure Time Series Insights.

> [!NOTE]
> * Artikeln fokuserar på en enda klient program där programmet är avsett att köras i endast en organisation.
> * Du använder normalt enda klient program för line-of-business-program som körs i din organisation.

## <a name="detailed-setup"></a>Ytterligare inställningar

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory-appregistrering

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Bevilja åtkomst till data

1. Time Series Insights-miljö, Välj **Dataåtkomstprinciper** och välj **Lägg till**.

   [![Lägg till ny policy för åtkomst till Time Series Insights-miljö](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. I den **Välj användare** dialogrutan rutan, klistra in antingen den **programnamn** eller **program-ID** från avsnittet Azure Active Directory app registrering.

   [![Hitta ett program i dialogrutan Välj användare](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Välj rollen. Välj **läsare** att fråga data eller **deltagare** att fråga efter data och ändra referensdata. Välj **OK**.

   [![Välj läsare eller deltagare i dialogrutan Välj användarroll](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Spara principen genom att välja **OK**.

   > [!TIP]
   > Läs mer om [beviljar åtkomst till data](./time-series-insights-data-access.md) till Time Series Insights-miljön i Azure Active Directory.

### <a name="client-app-initialization"></a>Klienten app-initiering

1. Använd den **program-ID** och **Klienthemlighet** (Programnyckel) från avsnittet Azure Active Directory app registrering för att hämta token för programmet.

    I C#, följande kod kan hämta token för programmet. Ett komplett exempel finns i [fråga data med C#](time-series-insights-query-data-csharp.md).

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

1. Token kan sedan skickas i den `Authorization` rubrik när programmet anropar API: T för Time Series Insights.

## <a name="next-steps"></a>Nästa steg

- Exempelkod som anropar API: T GA Time Series Insights, se [fråga data med hjälp av C# ](./time-series-insights-query-data-csharp.md).

- Kodexempel för förhandsversion Time Series Insights API, se [Förhandsgranska fråga data med hjälp av C# ](./time-series-insights-update-query-data-csharp.md).

- API-Referensinformation finns i [fråge-API-referens](/rest/api/time-series-insights/ga-query-api).

- Lär dig hur du [skapa ett huvudnamn för tjänsten](../active-directory/develop/howto-create-service-principal-portal.md).