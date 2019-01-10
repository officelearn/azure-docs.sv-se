---
title: Vägledning för utvecklare för villkorlig åtkomst i Azure Active Directory
description: Vägledning för utvecklare och scenarier för villkorlig åtkomst i Azure AD
services: active-directory
keywords: ''
author: CelesteDG
manager: mtillman
ms.author: celested
ms.reviewer: dadobali
ms.date: 09/24/2018
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: 24644faab85305f18fe4b657d3e982a306a41c16
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157084"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Vägledning för utvecklare för villkorlig åtkomst i Azure Active Directory

Funktionen för villkorlig åtkomst i Azure Active Directory (Azure AD) erbjuder på flera olika sätt som du kan använda för att skydda din app och en tjänst. Villkorlig åtkomst gör det möjligt för utvecklare och enterprise-kunder för att skydda tjänster i en mängd olika sätt, t.ex.:

* Multi-Factor Authentication
* Så att bara Intune-registrerade enheter att få åtkomst till specifika tjänster
* Begränsa användarplatser och IP-intervall

Mer information om alla funktioner för villkorlig åtkomst finns i [villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Den här artikeln visar hur du kan använda villkorlig åtkomst för utvecklare att bygga appar för Azure AD, och du får också lära dig om effekten av åtkomst till resurser som du inte har kontroll över som kan ha principer för villkorlig åtkomst tillämpas. Artikeln också utforskar konsekvenserna av villkorlig åtkomst i on-behalf-of-flöde webbappar, få åtkomst till Microsoft Graph och anropa API: er.

Kunskap om [enda](quickstart-v1-integrate-apps-with-azure-ad.md) och [flera innehavare](howto-convert-app-to-be-multi-tenant.md) appar och [vanliga autentiseringsmönster](authentication-scenarios.md) antas.

## <a name="how-does-conditional-access-impact-an-app"></a>Hur påverkar en app i villkorlig åtkomst?

### <a name="app-types-impacted"></a>Apptyper som påverkas

I de vanligaste fall villkorlig åtkomst ändra inte appens beteende eller kräver ändringar från utvecklaren. Endast i vissa fall kräver en app kodändringar att hantera villkorlig åtkomst ”utmaningar” när en app, indirekt eller obevakat begär en token för en tjänst. Det kan vara så enkla som utför en interaktiv inloggningsbegäran.

Mer specifikt kan kräver följande scenarier kod för att hantera villkorlig åtkomst ”utmaningar”:

* Appar får åtkomst till Microsoft Graph
* Appar som utför on-behalf-of-flöde
* Appar får åtkomst till flera tjänster/resurser
* Ensidesappar med ADAL.js
* Web Apps som anropar en resurs

Villkorlig åtkomst principer kan tillämpas på appen, men även kan tillämpas på ett webb-API har åtkomst till att din app. Mer information om hur du konfigurerar en princip för villkorlig åtkomst finns [Snabbstart: Kräva MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](../conditional-access/app-based-mfa.md).

Beroende på scenario, en företagskund tillämpa och ta bort principer för villkorlig åtkomst när som helst. Du behöver implementera ”utmaning”-hantering för din app för att fortsätta att fungera när en ny princip tillämpas. I följande exempel visas utmaning hantering.

### <a name="conditional-access-examples"></a>Exempel på villkorlig åtkomst

Vissa scenarier kräver ändringar i koden att hantera villkorlig åtkomst medan andra fungera på samma sätt. Här följer några scenarier med villkorlig åtkomst för att göra multifaktorautentisering som ger viss insikt om skillnaden.

* Du skapar en enda klient iOS-app och tillämpa en princip för villkorlig åtkomst. Appen loggar in en användare och begär inte åtkomst till ett API. När användaren loggar in, principen anropas automatiskt och användaren behöver utföra multifaktorautentisering (MFA).
* Du skapar en webbapp för flera klienter som använder Microsoft Graph för att få åtkomst till Exchange, bland andra tjänster. En företagskund som antar den här appen anger en princip för Exchange. När appen begär en token för MS Graph, kommer appen inte angripas att följa principen. Användaren har loggat in med giltig token. När appen försöker använda den här token mot Microsoft Graph för att få åtkomst till Exchange-data, en anspråk ”utmaning” returneras till webbappen via den ```WWW-Authenticate``` rubrik. Appen kan sedan använda den ```claims``` i en ny begäran och användaren uppmanas att följa villkoren.
* Du skapar en inbyggd app som använder en mellannivå-tjänst för att få åtkomst till en underordnad API. En företagskund på företag som använder den här appen gäller en princip för underordnade API: et. När en användare loggar in, den inbyggda appen begär åtkomst till mellannivån och skickar token. Mellannivån utför on-behalf-of-flöde för att begära åtkomst till underordnade API: et. Nu visas en anspråk ”utmaning” till mellannivån. På mellannivå skickar utmaningen tillbaka till den interna appen, som måste följa principen för villkorlig åtkomst.

### <a name="complying-with-a-conditional-access-policy"></a>Uppfyller en princip för villkorlig åtkomst

För flera topologier i annan app utvärderas principer för villkorlig åtkomst när sessionen har upprättats. När en princip för villkorlig åtkomst fungerar på Granulariteten för appar och tjänster, beror den punkt då den anropas kraftigt på vilket scenario du försöker utföra.

När din app försöker få åtkomst till en tjänst med en princip för villkorlig åtkomst, kan det uppstå en utmaning för villkorlig åtkomst. Den här utmaningen är kodat i den `claims` parameter som finns i ett svar från Azure AD eller Microsoft Graph. Här är ett exempel på den här utmaningen-parametern:

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Utvecklare kan dra denna utmaning och lägger till dem till en ny begäran till Azure AD. Skicka det här tillståndet uppmanas användaren att utföra alla åtgärder som behövs för att följa principen för villkorlig åtkomst. Information om felet och hur man extraherar parametern beskrivs i följande scenarier.

## <a name="scenarios"></a>Scenarier

### <a name="prerequisites"></a>Förutsättningar

Azure AD villkorlig åtkomst är en funktion som ingår i [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis#choose-an-edition). Du kan lära dig mer om Licensieringskrav i den [olicensierad användningsrapporten](../active-directory-conditional-access-unlicensed-usage-report.md). Utvecklare kan ansluta till den [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), vilket omfattar en kostnadsfri prenumeration på Enterprise Mobility Suite, som innehåller Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Överväganden för specifika scenarier

Följande information gäller endast i dessa scenarier för villkorlig åtkomst:

* Appar får åtkomst till Microsoft Graph
* Appar som utför on-behalf-of-flöde
* Appar får åtkomst till flera tjänster/resurser
* Ensidesappar med ADAL.js

I följande avsnitt beskrivs vanliga scenarier som är mer komplexa. De grundläggande drift principen är villkorlig åtkomst som principer utvärderas vid den tidpunkt som token begärs för tjänsten som har en princip för villkorlig åtkomst tillämpas, såvida inte den som kan nås via Microsoft Graph.

## <a name="scenario-app-accessing-microsoft-graph"></a>Scenario: Appen åtkomst till Microsoft Graph

Lär dig hur en webbapp begär åtkomst till Microsoft Graph i det här scenariot. Principen för villkorlig åtkomst kunde i det här fallet tilldelas SharePoint, Exchange eller någon annan tjänst som används som en arbetsbelastning via Microsoft Graph. I det här exemplet vi antar att det finns en princip för villkorlig åtkomst på SharePoint Online.

![Appen åtkomst till Microsoft Graph flödesdiagram](./media/conditional-access-dev-guide/app-accessing-microsoft-graph-scenario.png)

Appen först begär tillstånd att Microsoft Graph som kräver åtkomst till en underordnad arbetsbelastning utan villkorlig åtkomst. Begäran lyckas utan att anropa eventuella principer och appen tar emot token för Microsoft Graph. Appen kan nu använda åtkomsttoken i en ägar-begäran för den slutpunkt som begärdes. Nu behöver appen åtkomst till en SharePoint Online-slutpunkt för Microsoft Graph, till exempel: `https://graph.microsoft.com/v1.0/me/mySite`

Appen har redan en giltig token för Microsoft Graph, så att den kan utföra den nya förfrågan utan en ny token utfärdas. Den här begäran misslyckas och en utmaning anspråk utfärdas från Microsoft Graph i form av ett HTTP 403 Åtkomst nekas med en ```WWW-Authenticate``` utmaning.

Här är ett exempel på svaret:

```
HTTP 403; Forbidden
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

Utmaningen för anspråk som finns i den ```WWW-Authenticate``` rubriken, som kan parsas för att extrahera parametern anspråk för nästa begäran. När den läggs till i den nya förfrågan, Azure AD vet för att utvärdera principen för villkorlig åtkomst när du loggar in användaren och appen är nu kompatibel med principen för villkorlig åtkomst. Upprepa begäran till SharePoint Online-slutpunkten lyckas.

Den ```WWW-Authenticate``` rubrik har en unik struktur och är inte enkelt att parsa för att extrahera värden. Här är en kort metod för att hjälpa.

```csharp
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph.
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string.
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null;
        }
```

Kodexempel som visar hur du hanterar anspråk utmaningen, finns det [On-behalf-of-kodexempel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) för ADAL .NET.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: App fungerar on-behalf-of-flöde

I detta scenario går vi igenom fall där en inbyggd app anropar ett webb-/ API. I sin tur har den här tjänsten [he ”on-behalf-of” flöde för att anropa en underordnad tjänst. I vårt fall vi har tillämpat vår princip för villkorlig åtkomst till tjänsten underordnade (Web API 2) och använder en inbyggd app i stället för en server/daemon-app.

![App fungerar on-behalf-of-flödesdiagram](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Inledande Tokenbegäran för Web API 1 tillfrågas inte användaren för multi-Factor authentication som Web API 1 inte kanske alltid träffar underordnade API: et. När webb-API: et 1 försöker begära en token on-behalf-of användaren för Web API 2, misslyckas denna begäran eftersom användaren inte har loggat in med multifaktorautentisering.

Azure AD returnerar ett HTTP-svar med några intressanta data:

> [!NOTE]
> I den här instansen är det en beskrivning för multifaktorautentisering fel, men det finns en mängd olika `interaction_required` möjligt om villkorlig åtkomst.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

I Web API-1, vi fånga felet `error=interaction_required`, och skickar tillbaka den `claims` utmaning att skrivbordsappen. I det här läget skrivbordsappen kan göra en ny `acquireToken()` anropa och lägger till den `claims`utmaning som en extra frågesträngsparameter. Den här nya begäran kräver att användarna genomföra Multi-Factor authentication och skickar den här nya token tillbaka till Web API-1 och slutföra on-behalf-of-flöde.

Om du vill prova att använda det här scenariot, se vår [.NET-kodexempel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Den visar hur du skickar anspråk utmaningen från Web API 1 i den interna appen och skapa en ny begäran i klientappen.

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: Appen åtkomst till flera tjänster

I detta scenario går vi igenom om en webbapp som ansluter till två tjänster av vilka har en princip för villkorlig åtkomst som tilldelats. Beroende på din applogik kan det finnas en sökväg där din app inte kräver åtkomst till båda webbtjänster. I det här scenariot spelar en viktig roll i slutanvändarens upplevelse i den ordning som du begär en token.

Anta att vi har webbtjänst A och B och B-webbtjänsten har vår princip för villkorlig åtkomst tillämpas. När inledande interaktiva auth-begäranden kräver tillstånd för båda tjänsterna, krävs inte principen för villkorlig åtkomst i samtliga fall. Om appen begär en token för webbtjänsten B, principen har anropats och efterföljande begäranden om webbtjänsten A också lyckas på följande sätt.

![Appen åtkomst till flera tjänster flödesdiagram](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Du kan också om appen först begär en token för web service A, anropar användaren inte principen för villkorlig åtkomst. På så sätt kan apputvecklaren kontroll användaren får och inte framtvinga princip för villkorlig åtkomst anropas i samtliga fall. Knepigt fallet är om appen därefter begär en token för webbtjänsten B. Användaren måste då att följa principen för villkorlig åtkomst. När appen försöker `acquireToken`, det kan generera följande fel (illustreras i följande diagram):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Appen åtkomst till flera tjänster som begär en ny token](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Om appen använder ADAL-biblioteket, görs om det gick inte att hämta token alltid ett nytt interaktivt. När den här interaktiva begäran sker, har användaren möjlighet att uppfylla villkorlig åtkomst. Detta är SANT om inte begäran är en `AcquireTokenSilentAsync` eller `PromptBehavior.Never` då appen behöver utföra en interaktiv ```AcquireToken``` begäran och ge slutanvändningen möjlighet att följa principen.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenario: Ensidesapp (SPA) med hjälp av ADAL.js

I det här scenariot kan gå vi igenom fallet när vi har en ensidesapp (SPA) med hjälp av ADAL.js för att anropa en villkorlig åtkomst i skyddade webb-API. Detta är en enkel arkitektur men har vissa nyanser som måste beaktas när du utvecklar runt villkorlig åtkomst.

Det finns några funktioner som hämta token i ADAL.js,: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, och `acquireTokenRedirect(…)`.

* `login()` hämtar en ID-token via en interaktiv inloggningsbegäran men hämta inte åtkomsttoken för alla tjänster (inklusive en villkorlig åtkomst i skyddade webb-API).
* `acquireToken(…)` kan sedan användas till att hämta en åtkomsttoken, vilket innebär att Användargränssnittet inte visas i några omständigheter tyst.
* `acquireTokenPopup(…)` och `acquireTokenRedirect(…)` är båda för att begära en token för en resurs interaktivt vilket innebär att de alltid visa ett användargränssnitt för inloggning.

När en app behöver en åtkomsttoken att anropa ett webb-API, försöker den en `acquireToken(…)`. Om token sessionen har upphört att gälla eller vi behöver att följa en princip för villkorlig åtkomst kommer *acquireToken* funktionen misslyckas och appen använder `acquireTokenPopup()` eller `acquireTokenRedirect()`.

![Program med hjälp av ADAL flödesdiagram](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Låt oss gå igenom ett exempel med vår scenariot för villkorlig åtkomst. Användaren bara hamnade på platsen och har inte en session. Vi utför en `login()` anropa, hämta ett ID-token utan multifaktorautentisering. Sedan kommer du till en knapp som kräver appen att begärandedata från ett webb-API. Appen försöker att göra en `acquireToken()` anrop men misslyckas eftersom användaren inte har utfört Multi-Factor authentication ännu och behov att följa principen för villkorlig åtkomst.

Azure AD skickar tillbaka följande HTTP-svaret:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Vår app behöver fånga den `error=interaction_required`. Programmet kan sedan använda antingen `acquireTokenPopup()` eller `acquireTokenRedirect()` på samma resurs. Om användaren tvingas att göra en multifaktorautentisering. När användaren uppfyller Multi-Factor authentication, utfärdas en ny åtkomsttoken för den begärda resursen i appen.

Om du vill prova att använda det här scenariot, se vår [JS SPA On-behalf-of-kodexempel som](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Det här kodexemplet använder principen för villkorlig åtkomst och webb-API som du registrerade tidigare med en JS SPA att visa det här scenariot. Den visar hur att korrekt hantera utmaningen anspråk och få en åtkomsttoken som kan användas för Web API. Du kan också Kolla in allmänna [Angular.js-kodexempel som](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) råd om hur en Angular SPA


## <a name="see-also"></a>Se också

* Läs mer om funktionerna i [villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Fler Azure AD-kodexempel, se [GitHub-lagringsplatsen kodexempel](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Mer information om ADAL SDK- och åtkomsthantering referensdokumentationen Se [biblioteket guiden](active-directory-authentication-libraries.md).
* Läs mer om scenarier med flera innehavare i [så här loggar du in användare med flera innehavare mönstret](howto-convert-app-to-be-multi-tenant.md).
