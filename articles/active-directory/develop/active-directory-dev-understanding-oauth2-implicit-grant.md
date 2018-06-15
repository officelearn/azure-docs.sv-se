---
title: Förstå OAuth2 implicit bevilja flödet i Azure AD | Microsoft Docs
description: Lär dig mer om Azure Active Directory-implementeringen av OAuth2 implicit bevilja flödet, och om det är bäst för ditt program.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 263a093d5cf4b48ed1dadd4a288e548065ddf282
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155787"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Förstå OAuth2 implicit bevilja flödet i Azure Active Directory (AD)
OAuth2 implicit bevilja är kända för att ge en längsta lista med säkerhetsfrågor i OAuth2-specifikationen. Och ännu, som är den metod som implementeras av ADAL JS och det rekommenderas när du skriver SPA-program. Vad ger? Det är en fråga kompromisser: och det visar sig implicit bevilja är det bästa sättet att försöka för program som använder ett Web API via JavaScript från en webbläsare.

## <a name="what-is-the-oauth2-implicit-grant"></a>Vad är OAuth2 implicit bevilja?
Den quintessential [OAuth2 auktorisering kod bevilja](https://tools.ietf.org/html/rfc6749#section-1.3.1) är att bevilja de tillstånd som använder två separata slutpunkter. Auktorisering slutpunkten används för användaren interaktion fasen, vilket resulterar i en Auktoriseringskoden. Tokenslutpunkten används sedan av klienten för utbyte av koden för en åtkomst-token och ofta en uppdateringstoken. Webbprogram krävs för att visa sina egna program autentiseringsuppgifter till tokenslutpunkten, så att servern auktorisering kan autentisera klienten.

Den [OAuth2 implicit bevilja](https://tools.ietf.org/html/rfc6749#section-1.3.2) är en variant av andra stöd för auktorisering. Det gör att en klient för att få en åtkomsttoken (och id_token när du använder [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html)) direkt från slutpunkten för auktorisering, utan att kontakta tokenslutpunkten eller autentisera klienten. Den här variant är särskilt utformat för JavaScript-baserade program som körs i en webbläsare: i den ursprungliga OAuth2-specifikationen token returneras i ett URI-fragment. Som tillgängliggör token bits för JavaScript-koden i klienten, men det garanterar att de inte inkluderas i omdirigerar till servern. Returnerar token via webbläsaren omdirigeras direkt från slutpunkten för auktorisering. Har fördelen med att ta bort eventuella krav för mellan ursprungsanrop, vilket är nödvändigt om JavaScript-programmet krävs för att kontakta den token för slutpunkten.

En viktig egenskap för OAuth2 implicit bevilja är det faktum att sådan flödar aldrig returnerade uppdaterings-tokens till klienten. Som vi kommer att se i nästa avsnitt, som är inte verkligen behövs och faktum är ett säkerhetsproblem.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Lämpliga scenarier för OAuth2 implicit bevilja
Enligt specifikationen OAuth2 deklarerar har implicit bevilja planeras för att program som användaragent – det vill säga, JavaScript-program som körs inom en webbläsare. Den definierande egenskapen för sådana program är att JavaScript-kod används för åtkomst till serverresurser (vanligtvis en webb-API) och för att uppdatera programmet UX därefter. Se program, t.ex. Gmail eller Outlook Web Access: när du väljer ett meddelande i Inkorgen endast meddelande visualiseringen panelen ändras för att visa den nya markeringen, medan resten av sidan förblir oförändrade. Detta är till skillnad från traditionella omdirigerings-baserade webbappar, där varje användarinteraktion resulterar i ett återanslående helsida och en helsida återgivningen av nya serversvaret.

Program som tar JavaScript-baserade metoden att dess extreme kallas den enda sidan program eller SPA: tanken är att programmen bara hantera ett inledande HTML-sidan och associerad JavaScript med alla efterföljande interaktioner som drivs av webb-API anrop utförs via JavaScript. Dock hybrid metoder, där programmet är främst återanslående-driven men utför tillfällig JS-anrop, är inte ovanliga – diskussion om implicita flödet för användning som är relevant för dem också.

Omdirigerings-baserade program skydda vanligtvis begäranden via cookies, men att metoden inte fungerar även för JavaScript-program. Cookies fungerar endast mot den domän som har genererats för, medan JavaScript-anrop kan riktas mot andra domäner. I själva verket som är ofta fallet: Se program anropar Microsoft Graph API, Office-API, Azure API – alla som finns utanför domänen från där programmet hanteras. En ökande trend för JavaScript-program är att inga backend på alla förlitande 100% på 3 part webb-API: er att genomföra sina funktioner.

Den bästa metoden för att skydda anrop till ett Web API är för närvarande Använd OAuth2 ägar-token metod, där varje anrop åtföljs av en OAuth2-åtkomsttoken. Webb-API undersöks inkommande åtkomst-token och om den hittar i den nödvändiga scope, ger åtkomst till den begärda åtgärden. Implicita flödet är det lätt för JavaScript-program för att erhålla åtkomsttoken för webb-API ger många fördelar i fråga om cookies:

* Token kan erhållas på ett tillförlitligt sätt utan mellan ursprungsanrop – obligatorisk registrering av omdirigerings-URI som token är returnerade garanterar att token inte förskjuts
* JavaScript-program kan hämta så många åtkomsttoken som de behöver för så många Web API: er de är riktade mot – utan begränsning för domäner
* HTML5-funktioner som session eller lokal lagring ger fullständig kontroll över tokencachelagring och livstidshantering cookies management är täckande till appen
* Åtkomsttoken inte är sårbar för angrepp med webbplatser begäran förfalskning (CSRF)

Implicit bevilja flödet utfärdar inte uppdaterings-tokens främst av säkerhetsskäl. En uppdateringstoken omfång inte som snävare som åtkomsttoken, bevilja mycket mer kraft inflicting därför mycket större skada om den är känd. I det implicita flödet token levereras i URL: en, därför risken för avlyssning är högre än i kod-bevilja tillstånd.

Observera dock att JavaScript-programmet har en annan funktion förfogande för att förnya åtkomsttoken utan att fråga användaren om autentiseringsuppgifter upprepade gånger. Programmet kan använda en dold iframe för att utföra nya token-förfrågningar mot slutpunkten för auktorisering av Azure AD: så länge webbläsaren fortfarande har en aktiv session (läsa: har en sessionscookie) mot Azure AD-domänen autentiseringsbegäran kan har uppstå utan interaktion från användaren.

Den här modellen ger JavaScript-programmet möjlighet att förnya oberoende åtkomst-token och även få nya för ett nytt API (förutsatt att användaren godkänt tidigare för dessa. Detta förhindrar tillagt belastningen för hämtning, hantera och skydda ett högt värde artefakt, till exempel en uppdateringstoken. Artefakt som medger tyst förnyelse, Azure AD sessions-cookie hanteras utanför programmet. En annan fördel med den här metoden är en användare kan logga ut från Azure AD, med hjälp av de program som har loggat in på Azure AD, som körs i något av flikarna i webbläsaren. Detta resulterar i att sessions-cookie Azure AD och JavaScript-programmet kommer automatiskt att förlora möjligheten att förnya säkerhetstoken för signerade ut användaren.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Passar implicit bevilja min app?
Implicit bevilja presenterar flera risker än andra stöd och de områden som måste du vara uppmärksam på är väl dokumenterat. Till exempel [missbruk av Access Token för att personifiera resurs-ägare i Implicit flöda] [ OAuth2-Spec-Implicit-Misuse] och [Hotmodell för OAuth 2.0- och säkerhetsaspekter] [ OAuth2-Threat-Model-And-Security-Implications]). Högre risk profilen är dock i stort sett på grund av att det är tänkt att programmen som kör active kod som hanteras av en fjärresurs till en webbläsare. Om du planerar en SPA-arkitektur har inga backend-komponenter eller planerar att anropa ett webb-API via JavaScript, bör du använda det implicita flödet för token.

Om ditt program är en intern klient, är det implicita flödet inte en bra passning. Avsaknad av Azure AD-sessions-cookie i kontexten för en intern klient deprives programmet från innebär att underhålla en lång livslängd session. Vilket innebär att ditt program uppmanar flera gånger användaren när åtkomsttoken för nya resurser.

Om du utvecklar ett program som innehåller en serverdel och använda ett API från dess serverdelskoden är implicita flödet inte heller passar bra. Andra stöd ger mycket mer. OAuth2 klienten autentiseringsuppgifter bevilja innehåller till exempel möjligheten att hämta token som motsvarar de behörigheter som tilldelats programmet, till skillnad från användardelegeringar. Det innebär att klienten har möjlighet att underhålla programmatisk åtkomst till resurser även när en användare inte aktivt används i en session och så vidare. Inte bara den men sådana ger högre säkerhetsgarantier. Till exempel åtkomsttoken aldrig transit via webbläsaren användare, de inte riskerar att sparas i webbläsarhistoriken och så vidare. Klientprogrammet kan också utföra stark autentisering när du begär en token.

## <a name="next-steps"></a>Nästa steg
* En fullständig lista över resurser för utvecklare, inklusive referensinformation för protokoll och OAuth2 auktorisering bevilja flöden stöd av Azure AD finns i [Azure AD-Guide för utvecklare][AAD-Developers-Guide]
* Se [att integrera ett program med Azure AD] [ ACOM-How-To-Integrate] för ytterligare djupet på integration programprocessen.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
