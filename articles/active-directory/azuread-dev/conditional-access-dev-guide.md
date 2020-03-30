---
title: Utvecklarvägledning för Azure AD-villkorsåtkomst
description: Utvecklarvägledning och scenarier för Azure AD-villkorlig åtkomst
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ROBOTS: NOINDEX
ms.openlocfilehash: 92acb1a475fbd41bfb7351d73c61db866ce2bbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154941"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Utvecklarvägledning för Azure Active Directory Villkorlig åtkomst

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Funktionen Villkorlig åtkomst i Azure Active Directory (Azure AD) erbjuder ett av flera sätt som du kan använda för att skydda din app och skydda en tjänst. Med villkorlig åtkomst kan utvecklare och företagskunder skydda tjänster på många olika sätt, bland annat:

* Multi-Factor Authentication
* Tillåter endast Intune-registrerade enheter att komma åt specifika tjänster
* Begränsa användarplatser och IP-intervall

Mer information om de fullständiga funktionerna i villkorlig åtkomst finns [i Villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

För utvecklare som skapar appar för Azure AD visar den här artikeln hur du kan använda villkorlig åtkomst och du får också lära dig om effekten av att komma åt resurser som du inte har kontroll över som kan ha principer för villkorlig åtkomst tillämpad. I artikeln undersöks också konsekvenserna av villkorlig åtkomst i flödets för-, webbappar, åtkomst till Microsoft Graph och anropa API:er.

Kunskap om appar [med en och flera innehavare](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) och vanliga [autentiseringsmönster](v1-authentication-scenarios.md) antas.

## <a name="how-does-conditional-access-impact-an-app"></a>Hur påverkar villkorlig åtkomst en app?

### <a name="app-types-impacted"></a>Apptyper påverkade

I de flesta vanliga fall ändrar villkorlig åtkomst inte en apps beteende eller kräver några ändringar från utvecklaren.Endast i vissa fall när en app indirekt eller tyst begär en token för en tjänst, kräver en app kodändringar för att hantera villkorade åtkomst "utmaningar".Det kan vara så enkelt som att utföra en interaktiv inloggningsbegäran.

I följande scenarier krävs kod för att hantera villkorade åtkomst "utmaningar":

* Appar som utför flödet för den skull
* Appar som använder flera tjänster/resurser
* Ensidiga appar med ADAL.js
* Webbappar som anropar en resurs

Principer för villkorlig åtkomst kan tillämpas på appen, men kan också tillämpas på ett webb-API som appen har åtkomst till. Mer information om hur du konfigurerar en princip för villkorlig åtkomst finns i [Snabbstart: Kräv MFA för specifika appar med Azure Active Directory Villkorlig åtkomst](../conditional-access/app-based-mfa.md).

Beroende på scenariot kan en företagskund tillämpa och ta bort principer för villkorlig åtkomst när som helst. För att appen ska fortsätta fungera när en ny princip tillämpas måste du implementera "utmaningshanteringen". Följande exempel illustrerar utmaningshantering.

### <a name="conditional-access-examples"></a>Exempel på villkorlig åtkomst

Vissa scenarier kräver kodändringar för att hantera villkorlig åtkomst medan andra fungerar som de är. Här är några scenarier med villkorlig åtkomst för att göra multifaktorautentisering som ger en inblick i skillnaden.

* Du skapar en iOS-app med en klient och tillämpar en princip för villkorlig åtkomst. Appen loggar in en användare och begär inte åtkomst till ett API. När användaren loggar in anropas principen automatiskt och användaren måste utföra MFA (Multifaktorautentisering). 
* Du skapar en inbyggd app som använder en tjänst på mellannivå för att komma åt ett efterföljande API. En företagskund på företaget som använder den här appen tillämpar en princip på API:et i efterföljande led. När en slutanvändare loggar in begär den inbyggda appen åtkomst till mellannivån och skickar token. Den mellersta nivån utför för-på-av flödet för att begära åtkomst till nedströms API. Vid denna punkt, en fordran "utmaning" presenteras för den mellersta nivån. Den mellersta nivån skickar tillbaka utmaningen till den inbyggda appen, som måste följa principen villkorlig åtkomst.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph har särskilda överväganden när du skapar appar i miljöer med villkorlig åtkomst. I allmänhet fungerar mekaniken för villkorlig åtkomst på samma sätt, men de principer som användarna ser baseras på de underliggande data som appen begär från diagrammet. 

Specifikt representerar alla Microsoft Graph-scope en viss datauppsättning som kan tillämpas individuellt. Eftersom principer för villkorlig åtkomst tilldelas specifika datauppsättningar kommer Azure AD att tillämpa principer för villkorlig åtkomst baserat på data bakom Graph – i stället för Själva graph.

Om en app till exempel begär följande Microsoft Graph-scope

```
scopes="Bookings.Read.All Mail.Read"
```

En app kan förvänta sig att användarna uppfyller alla policyer som anges i Bokningar och Exchange. Vissa scope kan mappas till flera datauppsättningar om de ger åtkomst. 

### <a name="complying-with-a-conditional-access-policy"></a>Följa en princip för villkorlig åtkomst

För flera olika apptopologier utvärderas en princip för villkorlig åtkomst när sessionen upprättas. När en princip för villkorlig åtkomst fungerar på detaljerna i appar och tjänster beror den punkt där den anropas i hög grad på vilket scenario du försöker uppnå.

När din app försöker komma åt en tjänst med en princip för villkorlig åtkomst kan den stöta på en utmaning för villkorlig åtkomst. Den här utmaningen kodas i parametern `claims` som kommer i ett svar från Azure AD. Här är ett exempel på den här utmaningsparametern: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Utvecklare kan anta den här utmaningen och lägga till den på en ny begäran till Azure AD. Om du skickar det här tillståndet uppmanas slutanvändaren att utföra alla åtgärder som krävs för att följa principen villkorlig åtkomst. I följande scenarier förklaras specifika fel och hur du extraherar parametern.

## <a name="scenarios"></a>Scenarier

### <a name="prerequisites"></a>Krav

Azure AD Villkorlig åtkomst är en funktion som ingår i [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Du kan läsa mer om licenskrav i [rapporten olicensierad användning](../active-directory-conditional-access-unlicensed-usage-report.md). Utvecklare kan gå med i [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), som innehåller en kostnadsfri prenumeration på Enterprise Mobility Suite, som innehåller Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Överväganden för specifika scenarier

Följande information gäller endast i dessa scenarier för villkorlig åtkomst:

* Appar som utför flödet för den skull
* Appar som använder flera tjänster/resurser
* Ensidiga appar med ADAL.js

I följande avsnitt beskrivs vanliga scenarier som är mer komplexa. Den grundläggande driftsprincipen är principer för villkorlig åtkomst utvärderas när token begärs för tjänsten som har en princip för villkorlig åtkomst tillämpad.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: App som utför flödet för den skull

I det här fallet går vi igenom det fall där en inbyggd app anropar en webbtjänst/ETT-API. I sin tur gör den här tjänsten "för"-för-på"-flödet för att anropa en nedströmstjänst. I vårt fall har vi tillämpat vår policy för villkorlig åtkomst på nedströmstjänsten (Webb-API 2) och använder en inbyggd app i stället för en server/daemon-app. 

![App som utför flödesdiagrammet för den skull](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Den första tokenbegäran för Webb-API 1 uppmanar inte slutanvändaren för multifaktorautentisering eftersom webb-API 1 kanske inte alltid träffar nedströms-API:et. När Webb-API 1 försöker begära en token för användaren för Webb-API 2 misslyckas begäran eftersom användaren inte har loggat in med multifaktorautentisering.

Azure AD returnerar ett HTTP-svar med några intressanta data:

> [!NOTE]
> I det här fallet är det en multifaktorautentisering felbeskrivning, `interaction_required` men det finns ett brett utbud av möjliga som rör villkorlig åtkomst.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

I Webb-API 1 fångar `error=interaction_required`vi upp `claims` felet och skickar tillbaka utmaningen till skrivbordsappen. Då kan skrivbordsappen ringa `acquireToken()` ett nytt samtal `claims`och lägga till utmaningen som en extra frågesträngparameter. Den här nya begäran kräver att användaren gör multifaktorautentisering och sedan skickar tillbaka den nya token till Webb-API 1 och slutför flödet för den skull.This new request requires the user to do multi-factor authentication and then send this new token back to Web API 1 and complete the on-behalf-of flow.

Det här scenariot finns i exemplet [med .NET-kod](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Den visar hur du skickar anspråksutmaningen tillbaka från Webb-API 1 till den inbyggda appen och konstruerar en ny begäran i klientappen.

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: App som använder flera tjänster

I det här fallet går vi igenom det fall där en webbapp får åtkomst till två tjänster varav en har tilldelats en princip för villkorlig åtkomst. Beroende på applogiken kan det finnas en sökväg där appen inte kräver åtkomst till båda webbtjänsterna. I det här fallet spelar den ordning i vilken du begär en token en viktig roll i slutanvändarens upplevelse.

Låt oss anta att vi har webbtjänst A och B och webbtjänst B har vår policy för villkorlig åtkomst tillämpad. Den ursprungliga interaktiva auth-begäran kräver samtycke för båda tjänsterna, men principen villkorlig åtkomst krävs inte i alla fall. Om appen begär en token för webbtjänst B anropas principen och efterföljande begäranden om webbtjänst A lyckas också enligt följande.

![Flödesdiagram för flera tjänster](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Alternativt, om appen först begär en token för webbtjänst A, anropar slutanvändaren inte principen villkorlig åtkomst. På så sätt kan apputvecklaren styra slutanvändarens upplevelse och inte tvinga principen villkorlig åtkomst att anropas i alla fall. Det knepiga fallet är om appen senare begär en token för webbtjänst B. Nu måste slutanvändaren följa principen villkorlig åtkomst. När appen försöker `acquireToken`kan den generera följande fel (illustrerat i följande diagram):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App som använder flera tjänster som begär en ny token](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Om appen använder ADAL-biblioteket görs alltid ett försök att hämta token på nytt. När den här interaktiva begäran inträffar har slutanvändaren möjlighet att följa villkorlig åtkomst. Detta gäller om inte begäran `AcquireTokenSilentAsync` `PromptBehavior.Never` är en eller i vilket ```AcquireToken``` fall appen behöver utföra en interaktiv begäran för att ge slutanvändaren möjlighet att följa principen.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenario: Ensidig app (SPA) med ADAL.js

I det här fallet går vi igenom fallet när vi har en ensidig app (SPA), med ADAL.js för att anropa ett skyddat webb-API för villkorlig åtkomst. Detta är en enkel arkitektur men har några nyanser som måste beaktas när man utvecklar runt Villkorlig åtkomst.

I ADAL.js finns det några funktioner som `login()` `acquireToken(...)`hämtar `acquireTokenPopup(…)`token: , , och `acquireTokenRedirect(…)`.

* `login()`hämtar en ID-token via en interaktiv inloggningsbegäran men får inte åtkomsttoken för någon tjänst (inklusive ett skyddat webb-API för villkorlig åtkomst).
* `acquireToken(…)`kan sedan användas för att tyst få en åtkomsttoken vilket innebär att det inte visar användargränssnittet under några omständigheter.
* `acquireTokenPopup(…)`och `acquireTokenRedirect(…)` används båda för att interaktivt begära en token för en resurs vilket innebär att de alltid visar inloggningsgränssnittet.

När en app behöver en åtkomsttoken för att `acquireToken(…)`anropa ett webb-API försöker den en . Om tokensessionen har upphört att gälla eller om vi måste följa en princip `acquireTokenPopup()` för `acquireTokenRedirect()`villkorlig åtkomst misslyckas funktionen *acquireToken* och appen använder eller .

![Ensidig app med ADAL-flödesdiagram](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Låt oss gå igenom ett exempel med vårt scenario med villkorlig åtkomst. Slutanvändaren har precis landat på webbplatsen och har ingen session. Vi utför `login()` ett samtal, får en ID-token utan multifaktorautentisering. Sedan trycker användaren på en knapp som kräver att appen begär data från ett webb-API. Appen försöker göra `acquireToken()` ett samtal men misslyckas eftersom användaren inte har utfört multifaktorautentisering ännu och måste följa principen villkorlig åtkomst.

Azure AD skickar tillbaka följande HTTP-svar:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Vår app måste `error=interaction_required`fånga . Programmet kan sedan `acquireTokenPopup()` använda `acquireTokenRedirect()` antingen eller på samma resurs. Användaren är tvungen att göra en multifaktorautentisering. När användaren har slutfört multifaktorautentiseringen får appen en ny åtkomsttoken för den begärda resursen.

För att prova det här scenariot, se vår [JS SPA På uppdrag av kodexempel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Det här kodexemplet använder principen villkorlig åtkomst och webb-API som du registrerade tidigare med ett JS SPA för att demonstrera det här scenariot. Den visar hur du hanterar anspråksutmaningen på rätt sätt och får en åtkomsttoken som kan användas för webb-API:et. Alternativt kan du checka ut det allmänna [kodexemplet Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) för vägledning om ett vinkelspa

## <a name="see-also"></a>Se även

* Mer information om funktionerna finns [i Villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Mer Azure AD-kodexempel finns i [GitHub-repo av kodexempel](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Mer information om ADAL SDK:er och få tillgång till referensdokumentationen finns i [biblioteksguiden](active-directory-authentication-libraries.md).
* Mer information om scenarier med flera innehavare finns [i Så här loggar du in användare med flera innehavare](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
