---
title: "Azure Active Directory B2C: Översikt | Microsoft Docs"
description: Utveckla konsumentinriktade program med Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: parja
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: ca9dc5a2c0e7de46ebc29cce0ea7e667b7aa3093
ms.contentlocale: sv-se
ms.lasthandoff: 05/11/2017


---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure AD B2C: Fokusera på din app och låt oss ta hand om registrering och inloggning

Azure AD-B2C är en molntjänst för att hantera identiteter för webbappar och mobilappar. Det är en global tjänst med hög tillgänglighet som kan skalas till flera hundra miljoner identiteter. Azure AD B2C bygger på en säker plattform i företagsklass och ser till att dina program, ditt företag och dina användare är skyddade.

Med Azure AD B2C kan du använda autentisering i din app med minimal konfiguration:

* **Sociala konton** (till exempel Facebook, Google, LinkedIn med mera)
* **Företagskonton** (med öppna standardprotokoll, OpenID Connect eller SAML)
* **Lokala konton** (e-postadress och lösenord eller användarnamn och lösenord)

## <a name="get-started"></a>Kom igång

Skaffa först en egen klient genom att följa stegen i [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md).

Välj ditt apputvecklingsscenario:

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobilappar och skrivbordsappar](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />Mobilappar och skrivbordsappar</center> | [Översikt](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Web Apps](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [Översikt](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.js](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![Appar med en sida](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />Appar med en sida</center> | [Översikt](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![Webb-API:er](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />Webb-API:er</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br />[Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)<br /><br />[Anropa ett .NET-webb-API](active-directory-b2c-devquickstarts-web-api-dotnet.md) | &nbsp; |

## <a name="whats-new"></a>Nyheter

Kom tillbaka ofta om du är nyfiken på framtida förändringar i Azure Active Directory B2C. Vi kommer även att twittra om eventuella uppdateringar med @AzureAD.

* Förutom Inbyggda principer (allmän tillgänglighet) är nu funktionen [Anpassade principer](active-directory-b2c-overview-custom.md) tillgänglig i den allmänt tillgängliga förhandsversionen.  Anpassade principer är avsedda för experter som behöver mer kontroll över identitetsfunktionen.
* Funktionen [Åtkomsttoken](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview) är nu tillgänglig i den allmänt tillgängliga förhandsversionen.
* [Allmän tillgänglighet för Europabaserade Azure AD B2C](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/)-kataloger har tillkännagetts.
* Kolla in vårt ständigt växande bibliotek med [kodexempel på Github](https://github.com/Azure-Samples?q=b2c)!

## <a name="how-to-articles"></a>Instruktionsartiklar

Lär dig hur du använder specifika funktioner i Azure Active Directory B2C:

* Konfigurera [Facebook-](active-directory-b2c-setup-fb-app.md), [Google+-](active-directory-b2c-setup-goog-app.md), [Microsoft-](active-directory-b2c-setup-msa-app.md), [Amazon-](active-directory-b2c-setup-amzn-app.md) och [LinkedIn-](active-directory-b2c-setup-li-app.md)konton för användning i dina konsumentinriktade program.
* [Använd anpassade attribut för att samla in information om dina användare](active-directory-b2c-reference-custom-attr.md).
* [Aktivera Azure Multi-Factor Authentication i dina konsumentinriktade program](active-directory-b2c-reference-mfa.md).
* [Konfigurera lösenordsåterställning via självbetjäning för dina användare](active-directory-b2c-reference-sspr.md).
* [Anpassa utseendet på registrerings- och inloggningssidorna och på andra konsumentinriktade sidor](active-directory-b2c-reference-ui-customization.md) som hanteras av Azure Active Directory B2C.
* [Använd Azure Active Directory Graph API för att skapa, läsa, uppdatera och ta bort användare via programmering](active-directory-b2c-devquickstarts-graph-dotnet.md) i din Azure Active Directory B2C-klient.

## <a name="next-steps"></a>Nästa steg

Använd dessa länkar om du vill utforska tjänsten mer i detalj:

* Ta del av [Azure Active Directory B2C-prisinformation](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Granska våra [kodexempel](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c) för Azure Active Directory B2C. 
* Få hjälp på Stack Overflow genom att använda taggen [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c).
* Lämna feedback via [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) – vi vill veta vad du tycker!
* Granska [protokollreferens för Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Granska [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md).
* Läs [vanliga frågor och svar om Azure Active Directory B2C](active-directory-b2c-faqs.md).
* [Skapa supportförfrågningar för Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Hämta säkerhetsuppdateringar för våra produkter

Vi rekommenderar att du aktiverar aviseringar om säkerhetsincidenter genom att gå till [den här sidan](https://technet.microsoft.com/security/dd252948) och prenumerera på Microsoft Security Advisory-aviseringar.


