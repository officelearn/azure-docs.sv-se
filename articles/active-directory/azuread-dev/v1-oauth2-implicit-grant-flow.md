---
title: Förstå det implicita bidragsflödet oauth2 i Azure AD | Microsoft-dokument
description: Läs mer om Implementeringen av OAuth2 implicita bidragsflödet i Azure Active Directory och om det är rätt för ditt program.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154516"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Förstå OAuth2 implicita bidragsflödet i Azure Active Directory (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth2 implicita bidrag är ökänd för att vara det bidrag med den längsta listan över säkerhetsfrågor i OAuth2 specifikationen. Och ändå är det den strategi som genomförs av ADAL JS och den vi rekommenderar när du skriver SPA-applikationer. Vad ger? Det är allt en fråga om kompromisser: och som det visar sig, är det implicita bidraget det bästa sättet du kan följa för program som förbrukar ett webb-API via JavaScript från en webbläsare.

## <a name="what-is-the-oauth2-implicit-grant"></a>Vad är OAuth2 implicit bidrag?

[Tilldelningskodsbidraget för verkliga OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) är auktoriseringsbidraget som använder två separata slutpunkter. Auktoriseringsslutpunkten används för användarinteraktionsfasen, vilket resulterar i en auktoriseringskod. Tokenslutpunkten används sedan av klienten för att byta ut koden för en åtkomsttoken och ofta en uppdateringstoken också. Webbprogram krävs för att presentera sina egna programautentiseringsuppgifter för tokenslutpunkten, så att auktoriseringsservern kan autentisera klienten.

[OAuth2 implicita bidrag](https://tools.ietf.org/html/rfc6749#section-1.3.2) är en variant av andra tillståndsbidrag. Det gör det möjligt för en klient att hämta en åtkomsttoken (och id_token, när du använder [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)) direkt från auktoriseringsslutpunkten, utan att kontakta tokenslutpunkten eller autentisera klienten. Den här varianten har utformats för JavaScript-baserade program som körs i en webbläsare: i den ursprungliga OAuth2-specifikationen returneras token i ett URI-fragment. Det gör tokenbitarna tillgängliga för JavaScript-koden i klienten, men det garanterar att de inte inkluderas i omdirigeringar mot servern. I OAuth2 implicit beviljande utfärdar auktoriseringsslutpunkten åtkomsttoken till klienten med hjälp av en omdirigerad URI som tidigare levererades. Det har också fördelen att eliminera alla krav för cross origin-samtal, vilket är nödvändigt om JavaScript-programmet krävs för att kontakta tokenslutpunkten.

En viktig egenskap hos OAuth2 implicita bidrag är det faktum att sådana flöden aldrig returnerar uppdateringstoken till klienten. Nästa avsnitt visar hur detta inte är nödvändigt och skulle i själva verket vara en säkerhetsfråga.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Lämpliga scenarier för OAuth2 implicita bidrag

OAuth2-specifikationen förklarar att det implicita bidraget har utformats för att aktivera användaragentprogram – det vill säga JavaScript-program som körs i en webbläsare. Den definierande egenskapen hos sådana program är att JavaScript-kod används för åtkomst till serverresurser (vanligtvis ett webb-API) och för att uppdatera programanvändarupplevelsen därefter. Tänk på program som Gmail eller Outlook Web Access: När du väljer ett meddelande från inkorgen ändras bara panelen för meddelandevisualisering för att visa det nya valet, medan resten av sidan förblir oförändrad. Den här egenskapen står i kontrast till traditionella omdirigeringsbaserade webbappar, där varje användarinteraktion resulterar i en helsidesreback och en helsidesrendering av det nya serversvaret.

Program som använder javascript-baserad metod för sin extrema kallas ensidiga program eller SPA. Tanken är att dessa program endast ska tjäna en första HTML-sida och tillhörande JavaScript, där alla efterföljande interaktioner drivs av webb-API-anrop som utförs via JavaScript. Men hybrid metoder, där ansökan är mestadels postback-driven men utför enstaka JS-samtal, är inte ovanligt - diskussionen om implicit flödesanvändning är relevant för dem också.

Omdirigera-baserade program säkrar vanligtvis sina förfrågningar via cookies, men den metoden fungerar inte lika bra för JavaScript-program. Cookies fungerar bara mot den domän de har genererats för, medan JavaScript-samtal kan riktas mot andra domäner. Faktum är att det ofta kommer att vara fallet: tänk på program som anropar Microsoft Graph API, Office API, Azure API – alla som finns utanför domänen från där programmet visas. En växande trend för JavaScript-program är att inte ha någon backend alls, förlitar sig 100% på tredje part webb-API: er för att genomföra sin verksamhet funktion.

För närvarande är den metod som föredras för att skydda anrop till ett webb-API att använda metoden OAuth2-innehavartoken, där varje anrop åtföljs av en OAuth2-åtkomsttoken. Webb-API:et undersöker den inkommande åtkomsttoken och, om den hittar de nödvändiga scope, ger den åtkomst till den begärda åtgärden. Det implicita flödet ger en bekväm mekanism för JavaScript-program för att få åtkomsttoken för ett webb-API, vilket ger många fördelar när det gäller cookies:

* Tokens kan erhållas på ett tillförlitligt sätt utan behov av samtal med korsursprung – obligatorisk registrering av den omdirigera URI som token är returgarantier för att token inte förskjuts
* JavaScript-program kan hämta så många åtkomsttoken som de behöver, för så många webb-API:er de riktar sig till – utan begränsning på domäner
* HTML5-funktioner som session eller lokal lagring ger full kontroll över tokencacheing och livstidshantering, medan hantering av cookies är ogenomskinlig för appen
* Åtkomsttoken är inte mottagliga för CSRF-attacker (Cross-site request forgery)

Det implicita beviljandeflödet utfärdar inte uppdateringstoken, främst av säkerhetsskäl. En uppdateringstoken är inte lika snävt begränsad som åtkomsttoken, vilket ger mycket mer kraft och orsakar därmed mycket mer skada om den läcker ut. I det implicita flödet levereras token i URL:en, därav risken för avlyssning är högre än i auktoriseringskodsbidraget.

Ett JavaScript-program har dock en annan mekanism till sitt förfogande för att förnya åtkomsttoken utan att upprepade gånger fråga användaren om autentiseringsuppgifter. Programmet kan använda en dold iframe för att utföra nya tokenbegäranden mot auktoriseringsslutpunkten för Azure AD: så länge webbläsaren fortfarande har en aktiv session (läs: har en sessionscookie) mot Azure AD-domänen kan autentiseringsbegäran utan att användaren behöver interagera.

Den här modellen ger JavaScript-programmet möjlighet att självständigt förnya åtkomsttoken och till och med hämta nya för ett nytt API (förutsatt att användaren tidigare samtyckt till dem). På så sätt undviker du den extra bördan av att förvärva, underhålla och skydda en artefakt med högt värde, till exempel en uppdateringstoken. Artefakten som gör den tysta förnyelsen möjlig, Azure AD-sessionscookien, hanteras utanför programmet. En annan fördel med den här metoden är att en användare kan logga ut från Azure AD, med hjälp av något av de program som är inloggade på Azure AD och som körs på någon av webbläsarflikarna. Detta resulterar i borttagning av Azure AD-sessionscookien och JavaScript-programmet förlorar automatiskt möjligheten att förnya token för den utloggade användaren.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Är det implicita bidraget lämpligt för min app?

Det implicita bidraget innebär fler risker än andra bidrag, och de områden du måste uppmärksamma är väl dokumenterade (till exempel [missbruk av åtkomsttoken för att personifiera resursägare i Implicit flöde][OAuth2-Spec-Implicit-Misuse] och [OAuth 2.0 Hot model and Security Considerations][OAuth2-Threat-Model-And-Security-Implications]). Den högre riskprofilen beror dock till stor del på att den är avsedd att aktivera program som kör aktiv kod, som betjänas av en fjärrresurs till en webbläsare. Om du planerar en SPA-arkitektur, inte har några serverdelskomponenter eller avser att anropa ett webb-API via JavaScript, rekommenderas användning av det implicita flödet för tokeninhämtning.

Om ditt program är en inbyggd klient passar det implicita flödet inte bra. Frånvaron av Azure AD-sessionscookien i samband med en inbyggd klient berövar ditt program från hur du upprätthåller en långlivad session. Vilket innebär att ditt program upprepade gånger kommer att uppmana användaren när du får åtkomsttoken för nya resurser.

Om du utvecklar ett webbprogram som innehåller en serverdel och förbrukar ett API från dess serverdelskod, passar inte heller det implicita flödet. Andra bidrag ger dig mycket mer makt. Beviljandet av OAuth2-klientautentiseringsuppgifter ger till exempel möjlighet att hämta token som återspeglar de behörigheter som tilldelats själva programmet, i motsats till användardelegering. Detta innebär att klienten har möjlighet att upprätthålla programmatisk åtkomst till resurser även när en användare inte är aktivt engagerad i en session, och så vidare. Inte bara det, men sådana bidrag ger högre säkerhetsgarantier. Åtkomsttokens överförs till exempel aldrig via användarbläddraren, de riskerar inte att sparas i webbläsarhistoriken och så vidare. Klientprogrammet kan också utföra stark autentisering när du begär en token.

## <a name="next-steps"></a>Nästa steg

* Se [Så här integrerar du ett program med Azure AD][ACOM-How-To-Integrate] för ytterligare djup i programintegreringsprocessen.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
