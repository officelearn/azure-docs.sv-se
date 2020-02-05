---
title: API-autentisering och auktorisering – Azure Time Series Insights | Microsoft Docs
description: 'Den här artikeln beskriver hur du konfigurerar autentisering och auktorisering för ett anpassat program som anropar Azure Time Series Insights-API: et.'
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: ff5f7a80e2dcedb1795bae14ee9140c2842303a5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984600"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autentisering och auktorisering för Azure Time Series Insights-API

Det här dokumentet beskriver hur du registrerar en app i Azure Active Directory att använda bladet ny Azure Active Directory. Appar som registrerats i Azure Active Directory gör det möjligt för användare att autentisera till och ha behörighet att använda Azure Time Series Insight-API som är associerat med en Time Series Insights miljö.

> [!IMPORTANT]
> Azure Time Series Insights stöder båda följande autentiseringsscheman:
> * Det senaste [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [ADAL (Azure Active Directory Authentication Library)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Tjänstens huvud namn

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

   [![lägga till ny princip för data åtkomst i Time Series Insightss miljön](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. I dialog rutan **Välj användare** klistrar du in antingen **program namnet** eller **program-ID: t** från avsnittet Azure Active Directory app Registration.

   [![hitta ett program i dialog rutan Välj användare](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Välj rollen. Välj **läsare** för att fråga data eller **deltagare** att fråga efter data och ändra referens data. Välj **OK**.

   [![Välj läsare eller deltagare i dialog rutan Välj användar roll](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Spara principen genom att välja **OK**.

   > [!TIP]
   > För avancerade data åtkomst alternativ, Läs [bevilja åtkomst till data](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Initiering av klient program

* Utvecklare kan använda [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) eller [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) för att autentisera med Azure Time Series Insights.

* Till exempel för att autentisera med ADAL:

   1. Använd **program-ID** och **klient hemlighet** (program nyckel) från avsnittet Azure Active Directory app Registration för att hämta token för programmets räkning.

   1. I C#kan följande kod Hämta token för programmets räkning. För ett fullständigt exempel läser du [fråga data med C#hjälp av ](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Token kan sedan skickas i `Authorization`-rubriken när programmet anropar Time Series Insights-API: et.

* Utvecklare kan också välja att autentisera med MSAL. Läs om hur [du migrerar till MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) och se våra [Hantera ga-referens data för C# en Azure Time Series Insights miljö med hjälp av](time-series-insights-manage-reference-data-csharp.md) artikeln för mer information. 

## <a name="common-headers-and-parameters"></a>Vanliga rubriker och parametrar

I det här avsnittet beskrivs vanliga HTTP-begärandehuvuden och parametrar som används för att skapa frågor mot Time Series Insights GA-och Preview-API: er. API-särskilda krav beskrivs mer detaljerat i [Time Series Insights REST API referens dokumentation](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Läs [Azure REST API-referensen](https://docs.microsoft.com/rest/api/azure/) om du vill veta mer om hur du använder REST-API: er, gör HTTP-begäranden och hanterar http-svar.

### <a name="authentication"></a>Autentisering

För att utföra autentiserade frågor mot [Time Series Insights REST-API: er](https://docs.microsoft.com/rest/api/time-series-insights/)måste en giltig OAuth 2,0 Bearer-token skickas i [Authorization-huvudet](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) med en rest-klient som du väljer (Postman, Java Script, C#). 

> [!TIP]
> Läs den värdbaserade Azure Time Series Insights [SDK-exemplet för klient-SDK](https://tsiclientsample.azurewebsites.net/) för att lära dig att autentisera med Time Series Insights-API: er program mässigt med hjälp av [Java Script client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) tillsammans med diagram och diagram.

### <a name="http-headers"></a>HTTP-rubriker

Obligatoriska begärandehuvuden beskrivs nedan.

| Nödvändigt begär ande huvud | Beskrivning |
| --- | --- |
| Autentisering | För att autentisera med Time Series Insights måste en giltig OAuth 2,0 Bearer-token skickas i **Authorization** -huvudet. | 

> [!IMPORTANT]
> Token måste utfärdas exakt `https://api.timeseries.azure.com/` resursen (kallas även för "mål grupp" för token).
> * Din [Postman](https://www.getpostman.com/) - **AuthURL** kommer därför att: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/` är giltig men `https://api.timeseries.azure.com` inte.

Valfria begärandehuvuden beskrivs nedan.

| Valfritt begärandehuvud | Beskrivning |
| --- | --- |
| Innehålls typ | endast `application/json` stöds. |
| x-MS-client-Request-ID | En klientbegärans-ID. Tjänsten registrerar det här värdet. Tillåter att tjänsten spårar åtgärder mellan tjänster. |
| x-MS-client-session-ID | Ett klientsession-ID. Tjänsten registrerar det här värdet. Tillåter tjänsten att spåra en grupp relaterade åtgärder mellan tjänster. |
| x-MS-client-program-Name | Namnet på programmet som skapade den här begäran. Tjänsten registrerar det här värdet. |

Valfria men rekommenderade svarshuvuden beskrivs nedan.

| Svarshuvud | Beskrivning |
| --- | --- |
| Innehålls typ | endast `application/json` stöds. |
| x-MS-Request-ID | Server-genererat fråge-ID. Kan användas för att kontakta Microsoft för att undersöka en begäran. |
| x-MS-Property-not-found-Behavior | Huvud alternativ svars rubrik för GA API. Möjliga värden är `ThrowError` (standard) eller `UseNull`. |

### <a name="http-parameters"></a>HTTP-parametrar

> [!TIP]
> Hitta mer information om obligatoriska och valfria frågedata i [referens dokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/).

Obligatoriska URL-frågeparametrar är beroende av API-versionen.

| Publicera | Möjliga API-värden |
| --- |  --- |
| Allmän tillgänglighet | `api-version=2016-12-12`|
| Förhandsversion | `api-version=2018-11-01-preview` |
| Förhandsversion | `api-version=2018-08-15-preview` |

Valfria URL-parametrar för frågesträngar inkluderar att ange en tids gräns för körnings tider för HTTP-begäran.

| Valfri frågeparameter | Beskrivning | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | Tids gräns på Server sidan för körning av HTTP-begäran. Gäller endast för [Hämta miljö händelser](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) och hämta API: er för [samling av miljö](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) . Timeout-värdet ska vara i ISO 8601-varaktighets format, till exempel `"PT20S"` och måste vara inom intervallet `1-30 s`. Standardvärdet är `30 s`. | Allmänt tillgänglig |
| `storeType=<storeType>` | För för hands versioner av miljöer där varm lagring är aktiverat kan frågan köras antingen på `WarmStore` eller `ColdStore`. Den här parametern i frågan definierar i vilken lagring frågan ska köras. Om den inte är definierad körs frågan på kall butiken. **StoreType** måste ställas in på `WarmStore`för att kunna fråga det varmt arkivet. Om den inte är definierad utförs frågan mot kyl lagret. | Förhandsversion |

## <a name="next-steps"></a>Nästa steg

- För exempel kod som anropar GA Time Series Insights API: et läser du [frågans data med hjälp av C# ](./time-series-insights-query-data-csharp.md).

- För för hands versionen Time Series Insights API-kod exempel läser du läsa [förhands gransknings data med C# ](./time-series-insights-update-query-data-csharp.md).

- Information om API-referensinformation finns i [referens](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api) dokumentationen för fråge-API.

- Lär dig hur du [skapar ett huvud namn för tjänsten](../active-directory/develop/howto-create-service-principal-portal.md).
