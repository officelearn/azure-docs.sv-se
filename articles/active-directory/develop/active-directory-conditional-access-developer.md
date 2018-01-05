---
title: "Guide för utvecklare för villkorlig åtkomst i Azure Active Directory"
description: "Guide för utvecklare och scenarier för villkorlig åtkomst i Azure AD"
services: active-directory
keywords: 
author: danieldobalian
manager: mtillman
editor: PatAltimore
ms.author: dadobali
ms.date: 07/19/2017
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: 346f19b01460aaa4aeb2c2d97c07ef11924ec80f
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Guide för utvecklare för villkorlig åtkomst i Azure Active Directory

Azure Active Directory (AD) finns flera sätt att skydda din app och skydda en tjänst.  En av dessa unika funktioner är villkorlig åtkomst.  Villkorlig åtkomst kan utvecklare och enterprise-kunder att skydda tjänster i en mängd olika sätt, inklusive:

* Multi-Factor Authentication
* Att tillåta att Intune endast registrerade enheter att få åtkomst till särskilda tjänster
* Begränsa användarplatser och IP-adressintervall

Mer information om alla funktioner för villkorlig åtkomst finns [villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md). 

I den här artikeln fokuserar vi på vilka villkorlig åtkomst som innebär att utvecklare som bygger appar för Azure AD.  Den förutsätter kunskaper om [enda](active-directory-integrating-applications.md) och [flera innehavare](active-directory-devhowto-multi-tenant-overview.md) appar och [vanliga autentisering mönster](active-directory-authentication-scenarios.md).

Vi ska ta en titt på effekten av att komma åt resurser som du inte har kontroll över och som kan ha principer för villkorlig åtkomst tillämpas.  Dessutom undersöka vi hur villkorlig åtkomst i on-behalf-of-flöde web apps åtkomst till Microsoft Graph och anropar API: er.

## <a name="how-does-conditional-access-impact-an-app"></a>Hur påverkar en app av villkorlig åtkomst?

### <a name="app-types-impacted"></a>Apptyper som påverkas

I de flesta vanliga fall villkorlig åtkomst ändra inte appens beteende, eller kräver ändringar från utvecklaren.  Endast i vissa fall kräver en app kodändringar att hantera villkorlig åtkomst ”utmaningar” när en app, indirekt eller obevakat begär en token för en tjänst.  Det kan vara så enkelt som att utföra en begäran för interaktiv inloggning. 

Följande scenarier kräver mer specifikt kod för att hantera villkorlig åtkomst ”utmaningar”: 

* Appar som ansluter till Microsoft Graph
* Appar som utför on-behalf-of-flöde
* Appar som har åtkomst till flera services-resurser
* Ensidesappar med ADAL.js
* Web Apps som anropar en resurs

Villkorlig åtkomst principer kan tillämpas på appen, men även kan tillämpas på ett webb-API har åtkomst till att din app. Läs mer om hur du konfigurerar en princip för villkorlig åtkomst i [komma igång med Azure Active Directory villkorlig åtkomst](../active-directory-conditional-access-azure-portal-get-started.md).

En enterprise-kund kan, beroende på scenario, tillämpa och ta bort principer för villkorlig åtkomst när som helst.  Du behöver implementera ”challenge”-hantering för din app ska fortsätta att fungera när en ny princip används. Följande exempel visar challenge hantering. 

### <a name="conditional-access-examples"></a>Exempel på villkorlig åtkomst

Vissa scenarier kräver kodändringar att hantera villkorlig åtkomst medan andra fungerar som är.  Här följer några scenarier med villkorlig åtkomst för att göra multifaktorautentisering som ger viss insikt om skillnaden.

* Du skapar en enskild klient iOS-app och tillämpa en princip för villkorlig åtkomst.  Appen loggar in en användare och begära inte åtkomst till en API.  När användaren loggar in, principen aktiveras automatiskt och användaren behöver utföra multifaktorautentisering (MFA). 
* Du skapar ett webbprogram med flera innehavare som använder Microsoft Graph åtkomst till Exchange, bland annat.  En enterprise-kunder som antar den här appen anger en princip för Exchange.  När webbappen begär en token för MS Graph, angripas appen inte att följa principen.  Slutanvändaren har loggat in med giltiga tokens. När appen försöker använda denna token mot Microsoft Graph för att få åtkomst till Exchange-data, en anspråk ”utmaning” returneras till webbprogram via den ```WWW-Authenticate``` rubrik.  Appen kan sedan använda den ```claims``` i en ny begäran och slutanvändaren uppmanas att följa villkoren. 
* Du skapar en intern app som använder en mellannivå-tjänst för åtkomst till en underordnad API.  En enterprise-kund på företaget genom att använda den här appen gäller en princip för underordnade API: et.  När en slutanvändare loggar in, den inbyggda appen begär åtkomst till mellannivån och skickar token.  Mellannivån utför på-flöde för att begära åtkomst till underordnade API.  Nu visas en anspråk ”utmaning” till mellannivån. På mellannivå skickar utmaningen tillbaka till den inbyggda appen som måste vara kompatibel med principen för villkorlig åtkomst.

### <a name="complying-with-a-conditional-access-policy"></a>Uppfyller en princip för villkorlig åtkomst

En princip för villkorlig åtkomst utvärderas för flera olika app topologier när sessionen har upprättats.  Som en princip för villkorlig åtkomst fungerar på Granulariteten för appar och tjänster, beror den punkt då den anropas kraftigt på det scenario som du försöker utföra.

När din app försöker få åtkomst till en tjänst med en princip för villkorlig åtkomst, kan det uppstå en utmaning för villkorlig åtkomst.  Denna utmaning har kodats i den `claims` parameter som ingår i ett svar från Azure AD eller Microsoft Graph.  Här är ett exempel på den här challenge-parametern: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Utvecklare kan ta denna utmaning och lägga till en ny begäran till Azure AD.  Skicka det här tillståndet uppmanar slutanvändaren att utföra alla åtgärder som är nödvändiga för att följa principen för villkorlig åtkomst. Information om felet och extrahera parametern beskrivs i följande scenarier. 

## <a name="scenarios"></a>Scenarier

### <a name="prerequisites"></a>Förutsättningar

Azure AD villkorlig åtkomst är en funktion som ingår i [Azure AD Premium](../active-directory-whatis.md#choose-an-edition).  Du kan lära dig mer om licensiering krav i den [olicensierad användningsrapporten](../active-directory-conditional-access-unlicensed-usage-report.md).  Utvecklare kan ansluta till den [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), som innehåller en kostnadsfri prenumeration på Enterprise Mobility Suite som innehåller Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Överväganden för specifika scenarier

Följande information gäller endast för dessa scenarier för villkorlig åtkomst:

* Appar som ansluter till Microsoft Graph
* Appar som utför on-behalf-of-flöde
* Appar som har åtkomst till flera services-resurser
* Ensidesappar med ADAL.js

I följande avsnitt ska vi gå in vanliga scenarier som är mer komplexa.  De grundläggande drift principen är villkorlig åtkomst principer utvärderas när token har begärts för tjänsten som har en villkorlig åtkomstprincip som tillämpas om den som kan nås via Microsoft Graph.

## <a name="scenario-app-accessing-microsoft-graph"></a>Scenario: App åtkomst till Microsoft Graph

I det här scenariot vi att gå igenom om en web app begär åtkomst till Microsoft Graph. Principen för villkorlig åtkomst kan i det här fallet tilldelas SharePoint, Exchange och andra tjänster som används som en arbetsbelastning via Microsoft Graph.  I vårt exempel antar vi att det finns en princip för villkorlig åtkomst på Sharepoint Online.

![Appen åtkomst till Microsoft Graph flödesdiagram](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

Appen först begär tillstånd att Microsoft Graph som kräver åtkomst till en underordnad arbetsbelastning utan villkorlig åtkomst.  Begäran lyckas utan att anropa princip och appen tar emot token för Microsoft Graph.  Appen kan nu använda den åtkomst-token i en ägar-begäran för den slutpunkt som begärdes. Appen måste nu, till exempel öppnar en Sharepoint Online Microsoft Graph-slutpunkt:`https://graph.microsoft.com/v1.0/me/mySite`

Appen har redan en giltig token för Microsoft Graph, så den kan utföra nya begäran utan att utfärda en ny token. Denna begäran misslyckas och en utmaning anspråk utfärdas från Microsoft Graph i form av en HTTP 403 Åtkomst nekas med en ```WWW-Authenticate``` utmaning.
Här är ett exempel på svaret: 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

Utmaningen för anspråk finns i den ```WWW-Authenticate``` rubriken, som kan parsas för att extrahera parametern anspråk för nästa begäran.  När den läggs till av en ny begäran Azure AD vet för att utvärdera principen för villkorlig åtkomst när du loggar in användaren och appen är nu i enlighet med principen för villkorlig åtkomst.  Upprepa begäran till Sharepoint Online-slutpunkten lyckas.

Den ```WWW-Authenticate``` sidhuvud har en unik struktur och är inte trivial att parsa för att extrahera värden.  Här är en kort metod för att hjälpa.

```C#
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

Kodexempel som visar hur du hanterar utmaningen anspråk finns i den [On-behalf-of kodexemplet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) för ADAL .NET.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: App utför on-behalf-of-flöde

I det här scenariot går vi igenom de fall där en intern app anropar en webbtjänstens/API.  I sin tur den här tjänsten har [flödet ”on-behalf-of”](active-directory-authentication-scenarios.md#application-types-and-scenarios) att anropa en underordnad tjänst.  I vårt fall vi har använt våra principen för villkorlig åtkomst till tjänsten underordnade (Web API 2) och använder en intern app i stället för en server-daemon-app. 

![Utför Flödesdiagram för on-behalf-of App](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

Inledande Tokenbegäran för Web API 1 frågar inte slutanvändaren för multifaktorautentisering som Web API 1 inte kanske alltid träffar underordnade API: et.  När Web API 1 försöker begära en token on-behalf-of användaren för Web API 2, misslyckas denna begäran eftersom användaren inte har loggat in med multifaktorautentisering.

Azure AD returnerar ett HTTP-svar med vissa intressanta data: 

> [!NOTE]
> I den här instansen är det en beskrivning för multifaktorautentisering fel, men det finns en mängd olika `interaction_required` möjliga rör villkorlig åtkomst.  

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

I vår Web API-1 vi fånga felet `error=interaction_required`, och skicka tillbaka den `claims` svårt att skrivbordsappen.  Vid den punkten skrivbord appen kan göra en ny `acquireToken()` anropa och Lägg till den `claims`utmaning som en extra frågesträngparametern.  Den här nya begäran kräver att användarna genomföra Multi-Factor authentication och skicka den här nya token tillbaka till Web API-1 och slutföra on-behalf-of-flöde.

Om du vill testa det här scenariot finns i vår [.NET kodexemplet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Den visar hur du skickar anspråk challenge tillbaka från Web API-1 till den inbyggda appen och skapa en ny begäran i klientappen. 

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: App åtkomst till flera tjänster

I det här scenariot går vi igenom de fall där ett webbprogram har åtkomst till två tjänster som har en princip för villkorlig åtkomst som tilldelats.  Beroende på din app-logik kan det finnas en sökväg som din app inte kräver åtkomst till både webbtjänster.  I det här scenariot spelar en viktig roll i slutanvändarens upplevelse i den ordning i vilken du begära en token.

Antar vi att vi har webbtjänsten A och B och webbtjänsten B har vår principen för villkorlig åtkomst tillämpas.  Medan inledande interaktiva auth begäran kräver godkännande för båda tjänsterna, krävs inte principen för villkorlig åtkomst i samtliga fall.  Om appen begär en token för webbtjänsten B, principen anropas och efterföljande förfrågningar för webbtjänsten A också lyckas på följande sätt.

![Appen åtkomst till Flödesdiagram för flera tjänster](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

Du kan också om appen först begär en token för web service A, anropar slutanvändaren inte principen för villkorlig åtkomst.  På så sätt kan apputvecklaren att styra slutanvändarens upplevelse och utan att principen för villkorlig åtkomst kan anropas i samtliga fall. Komplicerade fallet är om appen därefter begär en token för webbtjänsten B. Nu måste slutanvändaren att följa principen för villkorlig åtkomst.  När appen försöker `acquireToken`, det kan generera följande fel (illustreras i följande diagram): 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![Appen åtkomst till flera tjänster som begär en ny token](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

Om appen använder ADAL-biblioteket, försöks alltid inte går att hämta token interaktivt.  När denna interaktiva begäran uppstår kan har slutanvändaren möjlighet att vara kompatibel med villkorlig åtkomst.  Detta är SANT om begäran är en `AcquireTokenSilentAsync` eller `PromptBehavior.Never` då appen behöver utföra en interaktiv ```AcquireToken``` begäran och ge slutanvändningen möjlighet att följa principen. 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenario: Enkel sida App (SPA) med hjälp av ADAL.js

I det här scenariot går vi igenom fallet när vi har en enstaka sida app (SPA) med hjälp av ADAL.js för att anropa ett webb-API för villkorlig åtkomst som skyddas.  Detta är en enkel arkitektur men vissa olika delarna som måste beaktas när du utvecklar runt villkorlig åtkomst.

ADAL.js, finns det några funktioner som hämta token: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, och `acquireTokenRedirect(…)`. 

* `login()`hämtar en token ID via en interaktiv inloggning begäran men erhålla inte åtkomsttoken för alla tjänster (inklusive en villkorlig åtkomst i skyddade webb-API).  
* `acquireToken(…)`kan sedan användas för att få en åtkomsttoken vilket innebär att Användargränssnittet inte visas i några omständigheter tyst.  
* `acquireTokenPopup(…)`och `acquireTokenRedirect(…)` är både för att begära en token för en resurs interaktivt vilket innebär att de alltid visar UI-inloggning.

När en app måste en åtkomsttoken att anropa ett webb-API, försöker den en `acquireToken(…)`.  Om token sessionen har upphört att gälla eller måste vara kompatibel med en princip för villkorlig åtkomst i *acquireToken* funktionen misslyckas och appen använder `acquireTokenPopup()` eller `acquireTokenRedirect()`.

![En sida-app med Flödesdiagram för ADAL](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

Låt oss gå igenom ett exempel med vårt scenario för villkorlig åtkomst.  Slutanvändaren bara landat på platsen och har inte en session.  Vi utföra en `login()` anropa, hämta ett ID-token utan multifaktorautentisering.  Användaren träffar sedan en knapp som kräver appen till begäran om data från ett webb-API.  Appen försöker att göra en `acquireToken()` anrop men misslyckas eftersom användaren inte har utfört Multi-Factor authentication ännu och måste vara kompatibel med principen för villkorlig åtkomst.

Azure AD skickar tillbaka följande HTTP-svaret: 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Vår app behöver fånga den `error=interaction_required`.  Programmet kan sedan använda antingen `acquireTokenPopup()` eller `acquireTokenRedirect()` på samma resurs.  Om användaren tvingas att göra en multifaktorautentisering. När användaren är klar multifaktorautentisering utfärdas appen en ny åtkomsttoken för den begärda resursen.

Om du vill testa det här scenariot finns i vår [JS SPA On-behalf-of kodexemplet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca).  Det här kodexemplet använder principen för villkorlig åtkomst och webb-API som du tidigare registrerad hos en JS SPA att visa det här scenariot. Den visar hur du korrekt hantera utmaningen anspråk och få en åtkomsttoken som kan användas för webb-API. Alternativt utcheckningen allmänna [Angular.js kodexemplet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) vägledning om en vinkel SPA


## <a name="see-also"></a>Se också

* Läs mer om funktionerna i [villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Läs mer Azure AD-kodexempel [Github-Repo över kodexempel](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory). 
* Mer information om ADAL SDK och åtkomst i referensdokumentationen finns [biblioteket guiden](active-directory-authentication-libraries.md).
* Mer information om flera innehavare scenarier finns [loggar in användare med flera innehavare mönstret](active-directory-devhowto-multi-tenant-overview.md).
