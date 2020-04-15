---
title: API-autentisering och auktorisering – Insikter i Azure Time Series | Microsoft-dokument
description: I den här artikeln beskrivs hur du konfigurerar autentisering och auktorisering för ett anpassat program som anropar API:et för Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: beefad41a270233336bb9134268c98341e81a7cd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380812"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autentisering och auktorisering för Azure Time Series Insights API

I det här dokumentet beskrivs hur du registrerar en app i Azure Active Directory med det nya Azure Active Directory-bladet. Appar som är registrerade i Azure Active Directory gör det möjligt för användare att autentisera och få behörighet att använda Azure Time Series Insight API som är associerat med en Time Series Insights-miljö.

> [!IMPORTANT]
> Azure Time Series Insights stöder båda följande autentiseringsbibliotek:
> * Det nyare [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Tjänstens huvudnamn

I följande avsnitt beskrivs hur du konfigurerar ett program för åtkomst till Time Series Insights API för en app. Programmet kan sedan fråga eller publicera referensdata i time series insights-miljön med hjälp av sina egna programautentiseringsuppgifter via Azure Active Directory.

## <a name="summary-and-best-practices"></a>Sammanfattning och metodtips

Registreringsflödet för Azure Active Directory-appar omfattar tre huvudsteg.

1. [Registrera ett program](#azure-active-directory-app-registration) i Azure Active Directory.
1. Auktorisera programmet för att ha [dataåtkomst till time series insights-miljön](#granting-data-access).
1. Använd **program-ID** och **klienthemlighet** `https://api.timeseries.azure.com/` för att hämta en token från [klientappen](#client-app-initialization). Token kan sedan användas för att anropa Time Series Insights API.

Per **steg 3**kan du separera programmets och dina användaruppgifter:

* Tilldela behörigheter till appidentiteten som skiljer sig från dina egna behörigheter. Vanligtvis är dessa behörigheter begränsade till endast vad appen kräver. Du kan till exempel tillåta att appen bara läser data från en viss Time Series Insights-miljö.
* Isolera appens säkerhet från den skapande användarens autentiseringsuppgifter med hjälp av en **klienthemlighet** eller ett säkerhetscertifikat. Därför är programmets autentiseringsuppgifter inte beroende av en viss användares autentiseringsuppgifter. Om användarens roll ändras kräver programmet inte nödvändigtvis nya autentiseringsuppgifter eller ytterligare konfiguration. Om användaren ändrar sitt lösenord kräver inte all åtkomst till programmet nya autentiseringsuppgifter eller nycklar.
* Kör ett obevakat skript med hjälp av en **klienthemlighet** eller ett säkerhetscertifikat i stället för en viss användares autentiseringsuppgifter (som kräver att de finns).
* Använd ett säkerhetscertifikat i stället för ett lösenord för att skydda åtkomsten till azure time series insights-API:et.

> [!IMPORTANT]
> Följ principen om **separation av problem** (beskrivs för det här scenariot ovan) när du konfigurerar din Azure Time Series Insights-säkerhetsprincip.

> [!NOTE]
> * Artikeln fokuserar på ett program med en klient där programmet är avsett att köras i endast en organisation.
> * Du använder vanligtvis program med en klientorganisation för affärsprogram som körs i organisationen.

## <a name="detailed-setup"></a>Detaljerad inställning

### <a name="azure-active-directory-app-registration"></a>Registrering av Azure Active Directory-appar

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Bevilja dataåtkomst

1. För time series insights-miljön väljer du **Dataåtkomstprinciper** och väljer **Lägg till**.

   [![Lägga till ny dataåtkomstprincip i time series insights-miljön](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. I dialogrutan **Välj användare** klistrar du in **programnamnet** eller **program-ID:t** i avsnittet Registrering av Azure Active Directory-appar.

   [![Hitta ett program i dialogrutan Välj användare](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Välj rollen. Välj **Läsare** om du vill fråga data eller **Deltagare** för att fråga efter data och ändra referensdata. Välj **OK**.

   [![Välj läsare eller deltagare i dialogrutan Välj användarroll](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Spara principen genom att välja **OK**.

   > [!TIP]
   > För avancerade dataåtkomstalternativ, läs [bevilja dataåtkomst](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Initiering av klientappar

* Utvecklare kan använda [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) eller [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) för att autentisera med Azure Time Series Insights.

* Om du till exempel vill autentisera med ADAL:

   1. Använd **program-ID** och **Client Secret** (Application Key) från azure Active Directory-appregistreringsavsnittet för att hämta token för programmets räkning.

   1. I C#kan följande kod hämta token för programmets räkning. Ett fullständigt exempel läser du [Frågedata med C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Token kan sedan skickas `Authorization` i huvudet när programmet anropar Time Series Insights API.

* Alternativt kan utvecklare välja att autentisera med MSAL. Läs om [att migrera till MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) och se våra [Hantera GA-referensdata för en Azure Time Series Insights-miljö med C#-artikeln](time-series-insights-manage-reference-data-csharp.md) för att lära dig mer. 

## <a name="common-headers-and-parameters"></a>Vanliga rubriker och parametrar

I det här avsnittet beskrivs vanliga HTTP-begäranden och parametrar som används för att ställa frågor mot Time Series Insights GA- och Preview-API:er. API-specifika krav behandlas mer i detalj i [time series insights REST API-referensdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Läs [Azure REST API-referensen](https://docs.microsoft.com/rest/api/azure/) om du vill veta mer om hur du använder REST-API:er, gör HTTP-begäranden och hanterar HTTP-svar.

### <a name="authentication"></a>Autentisering

Om du vill utföra autentiserade frågor mot [TIME Series Insights REST-API:er](https://docs.microsoft.com/rest/api/time-series-insights/)måste en giltig OAuth 2.0-bärarekäken skickas i [auktoriseringshuvudet](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) med valfri REST-klient (Postman, JavaScript, C#). 

> [!TIP]
> Läs den värdbaserade Azure Time Series [Insights-klienten SDK-exempelvisualisering](https://tsiclientsample.azurewebsites.net/) för att lära dig hur du autentiserar med Time Series Insights API:er som programmässigt använder [JavaScript Client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) tillsammans med diagram och diagram.

### <a name="http-headers"></a>HTTP-huvuden

Obligatoriska begäranden beskrivs nedan.

| Obligatoriskt begäranhuvud | Beskrivning |
| --- | --- |
| Auktorisering | Om du vill autentisera med Time Series Insights måste en giltig OAuth 2.0-bärarekäll skickas i **auktoriseringshuvudet.** | 

> [!IMPORTANT]
> Token måste utfärdas exakt till `https://api.timeseries.azure.com/` resursen (kallas även "målgruppen" för token).
> * Din [brevbärare](https://www.getpostman.com/) **AuthURL** kommer därför att vara:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`är giltig `https://api.timeseries.azure.com` men är inte.

Valfria begäranderubriker beskrivs nedan.

| Valfritt begärandehuvud | Beskrivning |
| --- | --- |
| Innehållstyp | stöds `application/json` endast. |
| x-ms-klient-begäran-id | Ett klientfråre-ID. Tjänsten registrerar det här värdet. Gör att tjänsten kan spåra åtgärder mellan tjänster. |
| x-ms-klient-session-id | Ett klientsessions-ID. Tjänsten registrerar det här värdet. Gör att tjänsten kan spåra en grupp relaterade åtgärder mellan tjänster. |
| x-ms-klient-program-namn | Namnet på programmet som genererade den här begäran. Tjänsten registrerar det här värdet. |

Valfria men rekommenderade svarsrubriker beskrivs nedan.

| Svarshuvud | Beskrivning |
| --- | --- |
| Innehållstyp | Endast `application/json` stöds. |
| x-ms-request-id | Servergenererat id-förfrå efterfrå kommer. Kan användas för att kontakta Microsoft för att undersöka en begäran. |
| x-ms-egenskap-hittades inte-beteende | GA API valfri svarshuvud. Möjliga värden `ThrowError` är (standard) eller `UseNull`. |

### <a name="http-parameters"></a>HTTP-parametrar

> [!TIP]
> Mer information om nödvändig och valfri frågeinformation finns i [referensdokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/).

Parametrar för begärenlig URL-frågesträng beror på API-version.

| Frisläpp | Möjliga API-versionsvärden |
| --- |  --- |
| Allmän tillgänglighet | `api-version=2016-12-12`|
| Förhandsversion | `api-version=2018-11-01-preview` |
| Förhandsversion | `api-version=2018-08-15-preview` |

Valfria URL-frågesträngparametrar inkluderar att ange en timeout för http-körningstider för begäran.

| Valfri frågeparameter | Beskrivning | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | Timeout på serversidan för körning av HTTP-begäran. Gäller endast för [API:er](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) för hämta miljöhändelser och [hämta miljöaggregat.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) Timeout-värdet ska till exempel `"PT20S"` vara i ISO 8601-längdformat och bör ligga i intervallet `1-30 s`. Standardvärdet `30 s`är . | Allmän tillgänglighet (GA) |
| `storeType=<storeType>` | För förhandsgranskningsmiljöer med varmt arkiv aktiverat kan frågan `WarmStore` `ColdStore`köras antingen på eller . Den här parametern i frågan definierar vilket arkiv frågan ska köras på. Om den inte har definierats körs frågan i kyl- och fryshuset. Om du vill fråga om det varma `WarmStore`arkivet måste **storeType** ställas in på . Om den inte har definierats körs frågan mot kyl- och fryshuset. | Förhandsversion |

## <a name="next-steps"></a>Nästa steg

- För exempelkod som anropar API:et för GA Time Series Insights läser du [Frågedata med C#](./time-series-insights-query-data-csharp.md).

- För API-kodexempel för Förhandsversioner läser du [Frågeförhandsgranskningsdata med C#](./time-series-insights-update-query-data-csharp.md).

- Information om API-referens finns i referensdokumentationen [för fråge-API.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Läs om hur du [skapar ett huvudnamn för tjänsten](../active-directory/develop/howto-create-service-principal-portal.md).
