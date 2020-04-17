---
title: Utvecklarvägledning för Azure Active Directory Villkorlig åtkomst
description: Utvecklarvägledning och scenarier för Azure AD-villkorlig åtkomst och Microsoft-identitetsplattform.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 03/16/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: aae1b8aa27363e8f1d3c72d3934146c47b0cf2c9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535901"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Utvecklarvägledning för Azure Active Directory Villkorlig åtkomst

Funktionen Villkorlig åtkomst i Azure Active Directory (Azure AD) erbjuder ett av flera sätt som du kan använda för att skydda din app och skydda en tjänst. Med villkorlig åtkomst kan utvecklare och företagskunder skydda tjänster på många olika sätt, bland annat:

* Multi-Factor Authentication
* Tillåter endast Intune-registrerade enheter att komma åt specifika tjänster
* Begränsa användarplatser och IP-intervall

Mer information om de fullständiga funktionerna i villkorlig åtkomst finns [i Villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

För utvecklare som skapar appar för Azure AD visar den här artikeln hur du kan använda villkorlig åtkomst och du får också lära dig om effekten av att komma åt resurser som du inte har kontroll över som kan ha principer för villkorlig åtkomst tillämpad. I artikeln undersöks också konsekvenserna av villkorlig åtkomst i flödets för-, webbappar, åtkomst till Microsoft Graph och anropa API:er.

Kunskap om appar [med en](quickstart-register-app.md) och [flera innehavare](howto-convert-app-to-be-multi-tenant.md) och [vanliga autentiseringsmönster](authentication-scenarios.md) antas.

> [!NOTE]
> För att använda den här funktionen krävs en Azure AD Premium P1-licens. Hitta rätt licens för dina behov i [Jämför allmänt tillgängliga funktioner i Free, Basic och Premium-utgåvorna](https://azure.microsoft.com/pricing/details/active-directory/).
> Kunder med [Microsoft 365 Business-licenser](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) har också tillgång till funktioner för villkorlig åtkomst.

## <a name="how-does-conditional-access-impact-an-app"></a>Hur påverkar villkorlig åtkomst en app?

### <a name="app-types-impacted"></a>Apptyper påverkade

I de flesta vanliga fall ändrar villkorlig åtkomst inte en apps beteende eller kräver några ändringar från utvecklaren.Endast i vissa fall när en app indirekt eller tyst begär en token för en tjänst, kräver en app kodändringar för att hantera villkorade åtkomst "utmaningar".Det kan vara så enkelt som att utföra en interaktiv inloggningsbegäran.

I följande scenarier krävs kod för att hantera villkorade åtkomst "utmaningar":

* Appar som utför flödet för den skull
* Appar som använder flera tjänster/resurser
* Ensidiga appar med MSAL.js
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

Azure AD Villkorlig åtkomst är en funktion som ingår i [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Kunder med [Microsoft 365 Business-licenser](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) har också tillgång till funktioner för villkorlig åtkomst.

### <a name="considerations-for-specific-scenarios"></a>Överväganden för specifika scenarier

Följande information gäller endast i dessa scenarier för villkorlig åtkomst:

* Appar som utför flödet för den skull
* Appar som använder flera tjänster/resurser
* Ensidiga appar med MSAL.js

I följande avsnitt beskrivs vanliga scenarier som är mer komplexa. Den grundläggande driftsprincipen är principer för villkorlig åtkomst utvärderas när token begärs för tjänsten som har en princip för villkorlig åtkomst tillämpad.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: App som utför flödet för den skull

I det här fallet går vi igenom det fall där en inbyggd app anropar en webbtjänst/ETT-API. I sin tur gör den här tjänsten "för"-för-på"-flödet för att anropa en nedströmstjänst. I vårt fall har vi tillämpat vår policy för villkorlig åtkomst på nedströmstjänsten (Webb-API 2) och använder en inbyggd app i stället för en server/daemon-app.

![App som utför flödesdiagrammet för den skull](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

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

Det här scenariot finns i exemplet [med .NET-kod](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access). Den visar hur du skickar anspråksutmaningen tillbaka från Webb-API 1 till den inbyggda appen och konstruerar en ny begäran i klientappen.

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: App som använder flera tjänster

I det här fallet går vi igenom det fall där en webbapp får åtkomst till två tjänster varav en har tilldelats en princip för villkorlig åtkomst. Beroende på applogiken kan det finnas en sökväg där appen inte kräver åtkomst till båda webbtjänsterna. I det här fallet spelar den ordning i vilken du begär en token en viktig roll i slutanvändarens upplevelse.

Låt oss anta att vi har webbtjänst A och B och webbtjänst B har vår policy för villkorlig åtkomst tillämpad. Den ursprungliga interaktiva auth-begäran kräver samtycke för båda tjänsterna, men principen villkorlig åtkomst krävs inte i alla fall. Om appen begär en token för webbtjänst B anropas principen och efterföljande begäranden om webbtjänst A lyckas också enligt följande.

![Flödesdiagram för flera tjänster](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Alternativt, om appen först begär en token för webbtjänst A, anropar slutanvändaren inte principen villkorlig åtkomst. På så sätt kan apputvecklaren styra slutanvändarens upplevelse och inte tvinga principen villkorlig åtkomst att anropas i alla fall. Det knepiga fallet är om appen senare begär en token för webbtjänst B. Nu måste slutanvändaren följa principen villkorlig åtkomst. När appen försöker `acquireToken`kan den generera följande fel (illustrerat i följande diagram):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App som använder flera tjänster som begär en ny token](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Om appen använder MSAL-biblioteket görs alltid ett försök att hämta token interaktivt. När den här interaktiva begäran inträffar har slutanvändaren möjlighet att följa villkorlig åtkomst. Detta gäller om inte begäran `AcquireTokenSilentAsync` `PromptBehavior.Never` är en eller i vilket ```AcquireToken``` fall appen behöver utföra en interaktiv begäran för att ge slutanvändaren möjlighet att följa principen.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>Scenario: Ensidig app (SPA) med MSAL.js

I det här fallet går vi igenom fallet när vi har en ensidig app (SPA), med MSAL.js för att anropa ett skyddat webb-API för villkorlig åtkomst. Detta är en enkel arkitektur men har några nyanser som måste beaktas när man utvecklar runt Villkorlig åtkomst.

I MSAL.js finns det några funktioner som `loginPopup()` `acquireTokenSilent(...)`hämtar `acquireTokenPopup(…)`token: , , och `acquireTokenRedirect(…)`.

* `loginPopup()`hämtar en ID-token via en interaktiv inloggningsbegäran men får inte åtkomsttoken för någon tjänst (inklusive ett skyddat webb-API för villkorlig åtkomst).
* `acquireTokenSilent(…)`kan sedan användas för att tyst få en åtkomsttoken vilket innebär att det inte visar användargränssnittet under några omständigheter.
* `acquireTokenPopup(…)`och `acquireTokenRedirect(…)` används båda för att interaktivt begära en token för en resurs vilket innebär att de alltid visar inloggningsgränssnittet.

När en app behöver en åtkomsttoken för att `acquireTokenSilent(…)`anropa ett webb-API försöker den en . Om tokensessionen har upphört att gälla eller om vi måste följa en princip `acquireTokenPopup()` för `acquireTokenRedirect()`villkorlig åtkomst misslyckas funktionen *acquireToken* och appen använder eller .

![Ensidig app med msal-flödesdiagram](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

Låt oss gå igenom ett exempel med vårt scenario med villkorlig åtkomst. Slutanvändaren har precis landat på webbplatsen och har ingen session. Vi utför `loginPopup()` ett samtal, får en ID-token utan multifaktorautentisering. Sedan trycker användaren på en knapp som kräver att appen begär data från ett webb-API. Appen försöker göra `acquireTokenSilent()` ett samtal men misslyckas eftersom användaren inte har utfört multifaktorautentisering ännu och måste följa principen villkorlig åtkomst.

Azure AD skickar tillbaka följande HTTP-svar:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Vår app måste `error=interaction_required`fånga . Programmet kan sedan `acquireTokenPopup()` använda `acquireTokenRedirect()` antingen eller på samma resurs. Användaren är tvungen att göra en multifaktorautentisering. När användaren har slutfört multifaktorautentiseringen får appen en ny åtkomsttoken för den begärda resursen.

För att prova det här scenariot, se vår [JS SPA På uppdrag av kodexempel](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access). Det här kodexemplet använder principen villkorlig åtkomst och webb-API som du registrerade tidigare med ett JS SPA för att demonstrera det här scenariot. Den visar hur du hanterar anspråksutmaningen på rätt sätt och får en åtkomsttoken som kan användas för ditt webb-API. Alternativt kan du checka ut det allmänna [kodexemplet Angular.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) för vägledning om ett vinkelspa

## <a name="see-also"></a>Se även

* Mer information om funktionerna finns [i Villkorlig åtkomst i Azure Active Directory](/azure/active-directory/conditional-access/overview).
* Mer Azure AD-kodexempel finns i [exempel](sample-v2-code.md).
* Mer information om MSAL SDK:er och få tillgång till referensdokumentationen finns i [översikt över Microsoft Authentication Library](msal-overview.md).
* Mer information om scenarier med flera innehavare finns [i Så här loggar du in användare med flera innehavare](howto-convert-app-to-be-multi-tenant.md).
* Läs mer om [villkorlig åtkomst och så att du skyddar åtkomsten till IoT-appar](/azure/architecture/example-scenario/iot-aad/iot-aad).
