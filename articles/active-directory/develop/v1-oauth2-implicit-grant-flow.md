---
title: Förstå OAuth2 implicit flöde beviljat i Azure AD | Microsoft Docs
description: Lär dig mer om Azure Active Directory-implementering av OAuth2 implicit ge flow, och om det är bäst för ditt program.
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
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: acdc3417643484fa98b16c4be1b83a44a8b73fc6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963007"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Förstå implicit beviljande av OAuth2 flödet i Azure Active Directory (AD)

OAuth2-implicit beviljande är kända för att bevilja med den längsta listan över säkerhetsproblem i OAuth2-specifikationen. Och ännu, som är den metod som implementeras av ADAL JS och det rekommenderas när du skriver SPA-program. Vad får? Det handlar alla kompromisser: och eftersom det har visat sig implicit beviljande är det bästa sättet att försöka för program som använder ett webb-API via JavaScript från en webbläsare.

## <a name="what-is-the-oauth2-implicit-grant"></a>Vad är OAuth2-implicit beviljande?

Den quintessential [OAuth2-auktoriseringskodbeviljande](https://tools.ietf.org/html/rfc6749#section-1.3.1) är auktoriseringsbeviljande som använder två separata slutpunkter. Auktoriseringsslutpunkten används för användarens interaktion fas, vilket resulterar i en auktoriseringskod. Tokenslutpunkten används sedan av klienten för utbyte av koden för en åtkomsttoken och ofta en uppdateringstoken. Webbprogram krävs för att ge sina egna program autentiseringsuppgifter till tokenslutpunkten, så att auktoriseringsservern kan autentisera klienten.

Den [OAuth2-implicit beviljande](https://tools.ietf.org/html/rfc6749#section-1.3.2) är en variant av andra auktoriseringsbeviljanden. Det gör att en klient att hämta en åtkomsttoken (och id_token, när du använder [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html)) direkt från auktoriseringsslutpunkten utan att kontakta tokenslutpunkten eller autentisera klienten. Den här variant har utformats för JavaScript-baserade program som körs i en webbläsare: i den ursprungliga OAuth2-specifikationen token returneras i ett URI-fragment. Som tillgängliggör token bitarna till JavaScript-koden i klienten, men det garanterar att de inte inkluderas i omdirigeringar mot servern. Returnerar token via webbläsaren omdirigeras direkt från slutpunkten för auktorisering. Det har även fördelen med att ta bort eventuella krav för skriptkörning över flera ursprungsanrop, vilket är nödvändigt om JavaScript-program som krävs för att kontakta tokenslutpunkten.

En viktig egenskap för OAuth2-implicit beviljande är det faktum att till exempel flödar uppdateringstoken som aldrig gå tillbaka till klienten. Nästa avsnitt visar hur detta inte är nödvändigt och i själva verket är ett säkerhetsproblem.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Lämpliga scenarier för OAuth2-implicit beviljande

OAuth2-specifikationen deklarerar att implicit beviljande planeras för att aktivera användaragent program – det vill säga, JavaScript-program som körs i en webbläsare. Den definierar egenskap för sådana program är att JavaScript-kod används för att komma åt serverresurser (vanligtvis ett webb-API) och för att uppdatera programmet användarupplevelsen i enlighet med detta. Se program, t.ex. Gmail- eller Outlook Web Access: när du väljer ett meddelande från din inkorg, endast meddelandepanel för visualisering ändras för att visa ändringen, medan resten av sidan förblir oförändrat. Denna egenskap är till skillnad från traditionella omdirigerings-baserade webbappar, där varje användaråtgärd resulterar i en hel sida återanslående och en hel sida återgivningen av nya serversvaret.

Program som kan JavaScript-baserade metod till dess extreme kallas enkelsidigt program eller SPA. Tanken är att programmen bara ge en första HTML-sida och associerad JavaScript, med alla efterföljande interaktioner som drivs av webb-API-anrop utförs via JavaScript. Dock hybridmetoder, där programmet är i stort sett återanslående-drivna men utför tillfällig JS-anrop, är inte ovanliga – diskussion om implicit flödesanvändning som är relevant för dem också.

Omdirigerings-baserade program vanligtvis skydda sina begäranden via cookies, men att metoden inte fungerar även för JavaScript-program. Cookies fungerar endast mot domänen som har genererats för, medan JavaScript-anrop kan riktas mot andra domäner. I själva verket som är ofta fallet: Se program som anropar Microsoft Graph API, Office-API, Azure-API – alla som finns utanför domänen från där programmet har hämtats. En växande trend för JavaScript-program är att ha utan serverdel på alla förlitande 100% på från tredje part webb-API: er att implementera sina funktioner.

För närvarande är är den bästa metoden för att skydda ett webb-API-anrop att använda token metoden OAuth2 ägar där varje anrop åtföljs av en åtkomsttoken för OAuth2. Webb-API undersöker den inkommande åtkomst-token och om den hittar i den nödvändiga omfång, ger åtkomst till den begärda åtgärden. Implicit flöde är det lätt för JavaScript-program att hämta åtkomsttoken för ett webb-API, erbjuder flera fördelar i fråga om cookies:

* Token kan hämtas på ett tillförlitligt sätt utan något behov för olika plattformar ursprungsanrop – obligatorisk registrering av omdirigerings-URI som token är returnerade garanterar att token inte förskjuts
* JavaScript-program kan hämta så många åtkomsttoken som de behöver, för så många webb-API: er de är riktade mot – utan begränsning för domäner
* HTML5-funktioner som sessionen eller lokal lagring bevilja fullständig kontroll över tokencachelagring och livstidshantering cookies management är täckande för appen
* Åtkomsttoken är inte sårbara för attacker med förfalskning (CSRF) av skriptkörning begäran

Implicit beviljande av flödet utfärdar inte tokens för användaruppdatering huvudsakligen av säkerhetsskäl. En uppdateringstoken är inte som snävare begränsade som åtkomsttoken, beviljar mycket mer kraft inflicting därför mycket stor skada om det är läcker. I det implicita flödet token levereras i URL: en, därför risk för avlyssning är högre än i auktoriseringskodsbeviljandet.

Ett JavaScript-program har dock en annan mekanism tillgång för att förnya åtkomsttoken utan att fråga användaren om autentiseringsuppgifter flera gånger. Programmet kan använda en dold iframe för att utföra nya tokenbegäranden mot auktoriseringsslutpunkten av Azure AD: så länge som webbläsaren har fortfarande en aktiv session (Läs: har en sessions-cookie) mot Azure AD-domänen autentiseringsbegäran kan har ske utan interaktion från användaren.

Den här modellen ger JavaScript-program möjlighet att förnya åtkomsttoken oberoende av varandra och även skaffa nya för ett nytt API (förutsatt att du godkänt tidigare för dessa. På så sätt undviker har lagts till bördan med att hämta, hantera och skydda ett högt värde artefakter, till exempel en uppdateringstoken. Den artefakt som gör det möjligt, tyst förnyelsen sessions-cookie Azure AD hanteras utanför programmet. En annan fördel med den här metoden är en användare kan logga ut från Azure AD, med någon av de program som har loggat in på Azure AD, som körs i något av flikarna i webbläsaren. Detta leder till borttagning av Azure AD-sessions-cookie och JavaScript-programmet kommer automatiskt att förlora möjligheten att förnya token för den signerade ut användaren.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Lämpar sig implicit beviljande för min app?

Implicit beviljande får mer risker än andra stöd och de områden som du bör uppmärksamma till är väl dokumenterat. Till exempel [missbruk av åtkomsttoken att personifiera Resursägaren i Implicit flöde] [ OAuth2-Spec-Implicit-Misuse] och [Hotmodell för OAuth 2.0- och säkerhetsaspekter] [ OAuth2-Threat-Model-And-Security-Implications]). Högre risk profilen är dock i stort sett på grund av att den är avsedd att programmen som kör active kod som hanteras av en fjärransluten resurs till en webbläsare. Om du planerar en SPA-arkitektur har inga backend-komponenter eller avser att anropa ett webb-API via JavaScript, bör du använda det implicita flödet för tokenförvärv.

Om programmet är en intern klient, är det implicita flödet inte passade bra. Avsaknad av Azure AD-sessions-cookie i samband med en intern klient deprives ditt program från innebär att underhålla en standardlagringen av långlivade session. Vilket innebär att ditt program uppmanas upprepade gånger användaren vid hämtning av åtkomsttoken för nya resurser.

Om du utvecklar ett program som innehåller en serverdel och använda ett API från dess serverdelskoden är det implicita flödet inte heller ett bra alternativ. Andra stöd ger dig mycket mer kraft. Till exempel ger OAuth2-klientautentiseringsuppgifter möjlighet att hämta token som återspeglar de behörigheter som tilldelats programmet, till skillnad från användardelegeringar. Det innebär att klienten har möjligheten att ha programmässig åtkomst till resurser även när en användare inte har blivit engagerade i en session och så vidare. Inte nog med det, men sådana bidrag ger högre säkerhetsgarantier. Till exempel åtkomsttoken aldrig överföra via webbläsaren användare, inte de riskerar att sparas i webbläsarhistoriken och så vidare. Klientprogrammet kan också utföra stark autentisering när du begär en token.

## <a name="next-steps"></a>Nästa steg

* En fullständig lista över resurser för utvecklare, inklusive referensinformation för protokoll och OAuth2-auktorisering bevilja flöden support av Azure AD finns i den [utvecklarhandboken för Azure AD][AAD-Developers-Guide]
* Se [hur du integrerar ett program med Azure AD] [ ACOM-How-To-Integrate] för kompletterande information på processen för dataintegrering i programmet.

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]:azure-ad-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
