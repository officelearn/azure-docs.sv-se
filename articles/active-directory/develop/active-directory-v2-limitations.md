---
title: Azure Active Directory v2.0-slutpunkten begränsningar och restriktioner | Microsoft Docs
description: En lista över begränsningar och restriktioner för Azure AD v2.0-slutpunkten.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a36f55c57a75f671b3e5eeae3d91ff60483afd37
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="should-i-use-the-v20-endpoint"></a>Bör jag använda v2.0-slutpunkten?
När du skapar program som integreras med Azure Active Directory, måste du bestämma om v2.0-slutpunkten och verifieringsprotokollen uppfyller dina behov. Azure Active Directorys ursprungliga slutpunkt stöds fortfarande helt och på vissa sätt, är fler funktioner än version 2.0. Dock v2.0-slutpunkten [introducerar betydande fördelar](active-directory-v2-compare.md) för utvecklare.

Här är förenklad rekommendationer för utvecklare vid denna tidpunkt:

* Om du måste stödja personliga Microsoft-konton i ditt program, Använd v2.0-slutpunkten. Men innan du gör det, måste du kontrollera att du förstår de begränsningar som diskuterar vi i den här artikeln.
* Om ditt program behöver bara stöder Microsoft work- och skolkonton, Använd inte v2.0-slutpunkten. I stället referera till vår [Utvecklarhandbok för Azure AD](active-directory-developers-guide.md).

Över tiden, kommer att växa v2.0-slutpunkten eliminera restriktioner som anges här, så att du bara behöver använda v2.0-slutpunkten. Under tiden kan är den här artikeln avsedd att hjälpa dig att avgöra om v2.0-slutpunkten är rätt för dig. Vi kommer att fortsätta att uppdatera den här artikeln för att återspegla det aktuella tillståndet för v2.0-slutpunkten. Kontrollera tillbaka omvärdera dina krav mot v2.0-funktioner.

Om du har en befintlig Azure AD-app som inte använder v2.0-slutpunkten, behövs det ingen börja från början. I framtiden kommer ger vi dig möjlighet att använda din befintliga Azure AD-program med v2.0-slutpunkten.

## <a name="restrictions-on-app-types"></a>Begränsningar för appar
Följande typer av appar stöds för närvarande inte av v2.0-slutpunkten. En beskrivning av apptyper som stöds finns i [apptyper för Azure Active Directory v2.0-slutpunkten](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>Fristående webb-API: er
Du kan använda v2.0-slutpunkten för [bygga en webb-API som skyddas med OAuth 2.0](active-directory-v2-flows.md#web-apis). Dock att webb-API kan ta emot token från ett program som har den samma program-ID. Du kan inte komma åt en webb-API från en klient som har ett annat program-ID. Klienten kan inte begära eller hämta behörighet till ditt webb-API.

Information om hur du skapar ett webb-API som accepterar token från en klient som har samma program-ID finns v2.0-slutpunkten Web API-exempel i vår [komma igång](active-directory-appmodel-v2-overview.md#getting-started) avsnitt.

## <a name="restrictions-on-app-registrations"></a>Begränsningar för app-registreringar
För närvarande för varje app som du vill integrera med v2.0-slutpunkten måste du skapa en appregistrering i den nya [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Befintliga Azure AD eller Microsoft-konto-appar som inte är kompatibla med v2.0-slutpunkten. Appar som är registrerade i någon portal än Programregistreringsportalen är inte kompatibla med v2.0-slutpunkten. I framtiden kommer planerar vi att erbjuda ett sätt att använda ett befintligt program som en v2.0-app. För närvarande, men finns det ingen migreringssökväg för en befintlig app att fungera med v2.0-slutpunkten.

Dessutom app registreringar som du skapar i den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) har följande varningar:

* Bara två apphemligheter tillåts per program-ID.
* En appregistrering som registreras av en användare med ett personligt microsoftkonto kan visas och endast hanteras av en enda utvecklarkonto. Den kan inte delas mellan flera utvecklare.  Om du vill dela appen registreringen bland flera utvecklare kan du skapa programmet loggar in på portalen för registrering med Azure AD-kontot.
* Det finns flera begränsningar på formatet för omdirigerings-URI som är tillåtet. Mer information om omdirigerings-URI: er finns i nästa avsnitt.

## <a name="restrictions-on-redirect-uris"></a>Begränsningar för omdirigerings-URI: er
Appar som är registrerade i portalen för registrering av programmet för närvarande begränsad till en begränsad uppsättning omdirigerings-URI-värden. Omdirigerings-URI för webbprogram och tjänster måste börja med schemat `https`, och alla omdirigerings-URI-värden måste dela en enda DNS-domän. Exempelvis kan du registrera ett webbprogram som har en av dessa omdirigerings-URI: er:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Registrering jämförs hela DNS-namnet på den befintliga omdirigeringen-URI för omdirigerings-URI som du lägger till DNS-namn. Begäran om att lägga till DNS-namnet kommer att misslyckas om något av följande villkor föreligger:  

* Hela DNS-namnet på den nya omdirigeringen-URI matchar inte DNS-namnet på den befintliga omdirigeringen-URI.
* Hela DNS-namnet på den nya omdirigeringen-URI är inte en underdomän till befintliga omdirigerings-URI.

Exempelvis har den här omdirigerings-URI för appen:

`https://login.contoso.com`

Du kan lägga till data så här:

`https://login.contoso.com/new`

I det här fallet matchar DNS-namnet exakt. Du kan också göra detta:

`https://new.login.contoso.com`

I så fall måste du referera till DNS-underdomänen login.contoso.com. Om du vill ha en app som har inloggningen east.contoso.com och inloggnings-west.contoso.com som omdirigerings-URI: er måste du lägga till de omdirigerings-URI: er i den här ordningen:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Du kan lägga till två senare eftersom de är underdomäner i första omdirigerings-URI, contoso.com. Den här begränsningen tas bort i en kommande version.

Tänk också på, du kan ha högst 20 svars-URL: er för ett visst program.

Information om hur du registrerar en app i portalen för registrering av program finns [hur du registrerar en app med v2.0-slutpunkten](active-directory-v2-app-registration.md).

## <a name="restrictions-on-libraries-and-sdks"></a>Begränsningar för bibliotek och SDK
Stöd för v2.0-slutpunkten är för närvarande begränsad. Om du vill använda v2.0-slutpunkten i ett produktionsprogram finns följande alternativ:

* Du kan på ett säkert sätt använda Microsoft allmänt tillgänglig serversidan mellanprogram för inloggning och token verifieringen om du skapar ett webbprogram. Dessa inkluderar OWIN öppna ID Connect mellanprogram för ASP.NET och Node.js Passport plugin-programmet. Kodexempel som använder Microsoft mellanprogram finns i vår [komma igång](active-directory-appmodel-v2-overview.md#getting-started) avsnitt.
* Om du skapar en desktop eller mobile-programmet, kan du använda en av vår förhandsgranskning bibliotek för Microsoft-autentisering (MSAL).  Dessa bibliotek finns i en förhandsgranskning för stöd för produktion, så det är säkert att använda dem i program i produktion. Du kan läsa mer om villkoren för förhandsversionen och tillgängliga bibliotek i vår [autentisering bibliotek referens](active-directory-v2-libraries.md).
* För plattformar som inte omfattas av Microsoft bibliotek kan du integrera med v2.0-slutpunkten genom att skicka och ta emot protokollmeddelanden i din programkod direkt. Protokollen v2.0 OpenID Connect och OAuth [dokumenteras explicit](active-directory-v2-protocols.md) som hjälper dig att utföra denna integration.
* Slutligen kan du använda bibliotek med öppen källkod öppna ID Connect och OAuth för att integrera med v2.0-slutpunkten. V2.0-protokollet måste vara kompatibel med många öppen källkod protokollet bibliotek utan större ändringar. Dessa typer av bibliotek varierar efter språk och plattformar. Den [öppna ID Connect](http://openid.net/connect/) och [OAuth 2.0](http://oauth.net/2/) webbplatser underhålla en lista över populära implementeringar. Mer information finns i [Azure Active Directory v2.0 och autentisering bibliotek](active-directory-v2-libraries.md), och en lista med öppen källkod klientbibliotek och exempel som har testats med v2.0-slutpunkten.

## <a name="restrictions-on-protocols"></a>Begränsningar för protokoll
V2.0-slutpunkten har inte stöd för SAML eller WS-Federation; den stöder endast öppna ID Connect och OAuth 2.0.  Inte alla funktioner och möjligheter för OAuth-protokoll har införts i v2.0-slutpunkten. Dessa protokollfunktioner som finns för närvarande *inte tillgänglig* i v2.0-slutpunkten:

* ID-token som utfärdas av v2.0-slutpunkten saknar ett `email` anspråk för användaren, även om du skaffar behörigheten för användaren att visa sin e-post.
* OpenID Connect användarinformationen slutpunkten har inte implementerats på v2.0-slutpunkten. Men alla data för användarprofiler som potentiellt skulle visas i den här slutpunkten är tillgängliga från Microsoft Graph `/me` slutpunkt.
* V2.0-slutpunkten stöder inte utfärdande roll eller grupp anspråk i ID-token.
* Den [OAuth 2.0 resurs ägare lösenord autentiseringsuppgifter Grant](https://tools.ietf.org/html/rfc6749#section-4.3) stöds inte av v2.0-slutpunkten.

V2.0-slutpunkten stöder dessutom inte någon form av SAML- eller WS-Federation-protokoll.

För att bättre förstå omfånget för protokollet funktioner som stöds i v2.0-slutpunkten kan du läsa igenom våra [referens för OpenID Connect och OAuth 2.0-protokollet](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Begränsningar för arbets-och skolkonton
Om du har använt Active Directory Authentication Library (ADAL) i Windows-program, kan du har vidtagit nytta av Windows-integrerad autentisering, som använder Security Assertion Markup Language (SAML) assertion bevilja. Med denna tilldelning federerade användare av Azure AD klienter kan autentisera tyst med sina lokala Active Directory-instans utan att ange autentiseringsuppgifter. SAML assertion bevilja stöds för närvarande inte på v2.0-slutpunkten.
