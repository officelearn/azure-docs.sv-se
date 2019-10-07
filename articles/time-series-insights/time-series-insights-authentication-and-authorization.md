---
title: Autentisera och auktorisera med hjälp av ett API i Azure Time Series Insights | Microsoft Docs
description: 'Den här artikeln beskriver hur du konfigurerar autentisering och auktorisering för ett anpassat program som anropar Azure Time Series Insights-API: et.'
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: e98c004b802711c83558bf4d7ec86c418679836b
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981157"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autentisering och auktorisering för Azure Time Series Insights-API

Det här dokumentet beskriver hur du registrerar en app i Azure Active Directory att använda bladet ny Azure Active Directory. Appar som registrerats i Azure Active Directory gör det möjligt för användare att autentisera till och ha behörighet att använda Azure Time Series Insight-API som är associerat med en Time Series Insights miljö.

## <a name="service-principal"></a>Tjänstens huvudnamn

I följande avsnitt beskrivs hur du konfigurerar ett program för att få åtkomst till Time Series Insights-API: et för en app. Programmet kan sedan fråga eller publicera referens data i Time Series Insights-miljön med hjälp av egna programautentiseringsuppgifter via Azure Active Directory.

## <a name="summary-and-best-practices"></a>Sammanfattning och bästa praxis

Registrerings flödet för Azure Active Directory-appen omfattar tre huvud steg.

1. [Registrera ett program](#azure-active-directory-app-registration) i Azure Active Directory.
1. Ge programmet [behörighet att komma åt data i Time Series Insightss miljön](#granting-data-access).
1. Använd **program-ID** och **klient hemlighet** för att hämta en token från `https://api.timeseries.azure.com/` i [klient programmet](#client-app-initialization). Token kan sedan användas för att anropa Time Series Insights-API: et.

Enligt **steg 3**kan du genom att avgränsa ditt program och dina användarautentiseringsuppgifter göra följande:

* Tilldela behörighet till appens identitet som är distinkt från dina egna behörigheter. Dessa behörigheter är vanligt vis begränsade till vad appen kräver. Du kan till exempel tillåta att appen endast läser data från en viss Time Series Insightss miljö.
* Isolera appens säkerhet från användarens autentiseringsuppgifter för autentisering med hjälp av en **klient hemlighet** eller ett säkerhetscertifikat. Därför är programmets autentiseringsuppgifter inte beroende av en specifik användares autentiseringsuppgifter. Om användarens roll ändras kräver programmet inte nödvändigt vis nya autentiseringsuppgifter eller ytterligare konfiguration. Om användaren ändrar sitt lösen ord kräver all åtkomst till programmet inga nya autentiseringsuppgifter eller nycklar.
* Kör ett obevakat skript med en **klient hemlighet** eller ett säkerhetscertifikat i stället för en enskild användares autentiseringsuppgifter (vilket kräver att de finns).
* Använd ett säkerhetscertifikat i stället för ett lösen ord för att skydda åtkomsten till ditt Azure Time Series Insights-API.

> [!IMPORTANT]
> Följ principen om **separering av problem** (beskrivs i det här scenariot ovan) när du konfigurerar din Azure Time Series Insights säkerhets princip.

> [!NOTE]
> * Artikeln fokuserar på ett program med en enda klient organisation där programmet endast är avsett att köras i en organisation.
> * Du använder vanligt vis program med en enda klient för branschspecifika program som körs i din organisation.

## <a name="detailed-setup"></a>Detaljerad installation

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory app-registrering

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Bevilja åtkomst till data

1. I Time Series Insightss miljön väljer du **data åtkomst principer** och väljer **Lägg till**.

   [![Add ny princip för data åtkomst till Time Series Insightss miljö](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. I dialog rutan **Välj användare** klistrar du in antingen **program namnet** eller **program-ID: t** från avsnittet Azure Active Directory app Registration.

   [![Find ett program i dialog rutan Välj användare](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Välj rollen. Välj **läsare** för att fråga data eller **deltagare** att fråga efter data och ändra referens data. Välj **OK**.

   [![Pick läsare eller deltagare i dialog rutan Välj användar roll](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Spara principen genom att välja **OK**.

   > [!TIP]
   > Läs om hur du [beviljar data åtkomst](./time-series-insights-data-access.md) till din Time Series Insights-miljö i Azure Active Directory.

### <a name="client-app-initialization"></a>Initiering av klient program

1. Använd **program-ID** och **klient hemlighet** (program nyckel) från avsnittet Azure Active Directory app Registration för att hämta token för programmets räkning.

    I C#kan följande kod Hämta token för programmets räkning. Ett fullständigt exempel finns i [fråga efter data med C#hjälp av ](time-series-insights-query-data-csharp.md).

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

1. Token kan sedan skickas i `Authorization`-huvudet när programmet anropar Time Series Insights-API: et.

## <a name="common-headers-and-parameters"></a>Vanliga rubriker och parametrar

I det här avsnittet beskrivs vanliga HTTP-begärandehuvuden och parametrar som används för att skapa frågor mot Time Series Insights GA-och Preview-API: er. API-särskilda krav beskrivs mer detaljerat i [Time Series Insights REST API referens dokumentation](https://docs.microsoft.com/rest/api/time-series-insights/).

### <a name="authentication"></a>Authentication

För att utföra autentiserade frågor mot [Time Series Insights REST-API: er](https://docs.microsoft.com/rest/api/time-series-insights/)måste en giltig OAuth 2,0 Bearer-token skickas i [Authorization-huvudet](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) med en rest-klient som du väljer (Postman, Java Script, C#). 

> [!IMPORTANT]
> Token måste utfärdas exakt till `https://api.timeseries.azure.com/`-resursen (kallas även "mål grupp" för token).
> * Din [Postman](https://www.getpostman.com/) - **AuthURL** med motsvarar därför följande: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`

> [!TIP]
> Mer information om hur du autentiserar med Time Series Insights-API: er med hjälp av [Java Script client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) tillsammans med diagram och diagram finns i [exempel visualiseringen för klient-SDK](https://tsiclientsample.azurewebsites.net/) för värdbaserade Azure Time Series Insights.

### <a name="http-headers"></a>HTTP-rubriker

Obligatoriska begärandehuvuden:

- `Authorization` för autentisering och auktorisering måste en giltig OAuth 2,0 Bearer-token skickas i Authorization-huvudet. Token måste utfärdas exakt till `https://api.timeseries.azure.com/`-resursen (kallas även "mål grupp" för token).

Valfria begärandehuvuden:

- `Content-type` – endast `application/json` stöds.
- `x-ms-client-request-id`-klient begär ande-ID. Tjänsten registrerar det här värdet. Tillåter att tjänsten spårar åtgärder mellan tjänster.
- `x-ms-client-session-id`-ett klientsession-ID. Tjänsten registrerar det här värdet. Tillåter tjänsten att spåra en grupp relaterade åtgärder mellan tjänster.
- `x-ms-client-application-name`-namnet på programmet som skapade den här begäran. Tjänsten registrerar det här värdet.

Svars rubriker:

- `Content-type` – endast `application/json` stöds.
- `x-ms-request-id` – Server-genererat fråge-ID. Kan användas för att kontakta Microsoft för att undersöka en begäran.

### <a name="http-parameters"></a>HTTP-parametrar

Obligatoriska parametrar för URL-frågesträng:

- `api-version=2016-12-12`
- `api-version=2018-11-01-preview`

Valfria URL-parametrar för frågesträng:

- `timeout=<timeout>` – tids gräns för Server sidan för körning av begäran. Gäller endast för [Hämta miljö händelser](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) och hämta API: er för [samling av miljö](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) . Timeout-värdet ska vara i ISO 8601-varaktighets format, till exempel `"PT20S"` och måste vara inom intervallet `1-30 s`. Standardvärdet är `30 s`.

## <a name="next-steps"></a>Nästa steg

- Exempel kod som anropar GA Time Series Insights API finns i [fråga efter data med C#hjälp av ](./time-series-insights-query-data-csharp.md).

- För för hands versionen Time Series Insights API-kod exempel, se [fråga C#för hands versions data med ](./time-series-insights-update-query-data-csharp.md).

- Information om API-referensinformation finns i [API-referens för frågor](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Lär dig hur du [skapar ett huvud namn för tjänsten](../active-directory/develop/howto-create-service-principal-portal.md).