---
title: Azure Active Directory v2.0-slutpunkten begränsningar och restriktioner | Microsoft Docs
description: En lista över begränsningar och restriktioner för Azure AD v2.0-slutpunkten.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: 986440e5af2fb665f5d7e244f2df12edba09f022
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431308"
---
# <a name="should-i-use-the-v20-endpoint"></a>Ska jag använda v2.0-slutpunkten?

När du skapar program som integreras med Azure Active Directory (AD Azure), måste du bestämma om protokollen v2.0-slutpunkten och autentisering uppfyller dina behov. Azure Active Directorys ursprungliga slutpunkt stöds fortfarande fullt ut och på vissa sätt, har du fler funktioner än v2.0. Dock v2.0-slutpunkten [introducerar stora fördelar](active-directory-v2-compare.md) för utvecklare.

Här är en förenklad rekommendation för utvecklare som vid denna tidpunkt:

* Om du måste ha stöd för personliga Microsoft-konton i ditt program, använder du v2.0-slutpunkten. Men innan du gör det, måste du kontrollera att du förstår de begränsningar som beskrivs i den här artikeln.
* Om ditt program behöver bara stöder Microsoft arbets- och skolkonton, Använd inte v2.0-slutpunkten. I stället referera till den [utvecklarguide för Azure AD](azure-ad-developers-guide.md).

V2.0-slutpunkten kommer att utvecklas för att ta bort de begränsningar som anges här, så att du behöver bara använda v2.0-slutpunkten. Under tiden kan använda den här artikeln för att avgöra om v2.0-slutpunkten är rätt för dig. Vi fortsätter att uppdatera den här artikeln för att återspegla det aktuella tillståndet för v2.0-slutpunkten. Kontrollera att gå tillbaka till dina krav mot v2.0 funktioner för att omvärdera.

Om du har en befintlig Azure AD-app som inte använder v2.0-slutpunkten är det du behöver inte börja från början. I framtiden kommer tillhandahåller vi ett sätt som du kan använda dina befintliga Azure AD-program med v2.0-slutpunkten.

## <a name="restrictions-on-app-types"></a>Begränsningar för apptyper

För närvarande stöds följande typer av appar som inte av v2.0-slutpunkten. En beskrivning av apptyper som stöds finns i [apptyperna för Azure Active Directory v2.0-slutpunkten](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>Fristående webb-API: er

Du kan använda v2.0-slutpunkten till [bygga en webb-API som skyddas med OAuth 2.0](active-directory-v2-flows.md#web-apis). Men den webb-API kan ta emot token från ett program som har samma programmets ID. Du kan inte komma åt ett webb-API från en klient som har ett annat program-ID. Klienten kommer inte att kunna begära eller erhålla behörighet att ditt webb-API.

Om du vill se hur du skapar ett webb-API som accepterar token från en klient som har samma program-ID finns i v2.0-slutpunkten webb-API-exemplen i den [komma igång](active-directory-appmodel-v2-overview.md#getting-started) avsnittet.

## <a name="restrictions-on-app-registrations"></a>Begränsningar för app-registreringar

För närvarande för varje app som du vill integrera med v2.0-slutpunkten, måste du skapa en appregistrering i den nya [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Befintligt Azure AD eller Microsoft-konto-appar som inte är kompatibel med v2.0-slutpunkten. Appar som har registrerats i någon portal än på Programregistreringsportalen är inte kompatibla med v2.0-slutpunkten. I framtiden planerar vi att tillhandahålla ett sätt att använda ett befintligt program som en v2.0-app. Det finns för närvarande ingen migreringssökväg för en befintlig app att fungera med v2.0-slutpunkten.

Dessutom app-registreringar som du skapar i den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) har följande varningar:

* Bara två apphemligheter tillåts per program-ID.
* En appregistrering som registrerats av en användare med ett personligt microsoftkonto kan visas och endast hanteras av en enda developer-konto. Det kan inte delas mellan flera utvecklare. Om du vill dela din appregistrering av flera utvecklare kan du skapa programmet genom att logga in på portalen för registrering med en Azure AD-konto.
* Det finns flera begränsningar på formatet för omdirigerings-URI som tillåts. Mer information om hur omdirigerade URI: er finns i nästa avsnitt.

## <a name="restrictions-on-redirect-uris"></a>Begränsningar för omdirigerings-URI: er

Appar som har registrerats i på Programregistreringsportalen är begränsade till en begränsad uppsättning omdirigerings-URI-värden. Omdirigerings-URI för webbappar och tjänster måste börja med schemat `https`, och alla omdirigerings-URI-värden måste dela en enda DNS-domän. Exempelvis kan du registrera ett webbprogram som har en av de här omdirigerings-URI: er:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Registreringssystemet jämför hela DNS-namnet på den befintliga omdirigeringen-URI som DNS-namnet för omdirigerings-URI som du lägger till. Begäran om att lägga till DNS-namnet kommer att misslyckas om något av följande villkor föreligger:  

* Hela DNS-namnet på den nya omdirigeringen-URI matchar inte DNS-namnet på den befintliga omdirigeringen-URI.
* Hela DNS-namnet på den nya omdirigeringen-URI är inte en underdomän till den befintliga omdirigeringen-URI.

Exempel: om appen har den här omdirigerings-URI:

`https://login.contoso.com`

Du kan lägga till data så här:

`https://login.contoso.com/new`

I det här fallet matchar DNS-namnet exakt. Du kan också göra detta:

`https://new.login.contoso.com`

I så fall måste du referera till DNS-underdomänen login.contoso.com. Om du vill ha en app som har login-east.contoso.com och login-west.contoso.com som omdirigerings-URI: er, måste du lägga till de omdirigerings-URI: er i den här ordningen:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Du kan lägga till två senare eftersom de är underdomäner i den första omdirigeringen-URI, contoso.com. Den här begränsningen tas bort i en kommande version.

Observera också, du kan ha högst 20 svars-URL för ett visst program.

Läs hur du registrerar en app i portalen för registrering av program i [hur du registrerar en app med v2.0-slutpunkten](active-directory-v2-app-registration.md).

## <a name="restrictions-on-libraries-and-sdks"></a>Begränsningar för bibliotek och SDK: er

Stöd för v2.0-slutpunkten är för närvarande begränsad. Om du vill använda v2.0-slutpunkten i ett produktionsprogram finns följande alternativ:

* Om du skapar ett webbprogram, kan du på ett säkert sätt använda allmänt tillgängligt från serversidan mellanprogram för Microsoft för att utföra verifiering av inloggning och token. Dessa inkluderar OWIN öppna ID Connect-mellanprogram för ASP.NET och Node.js Passport-plugin-programmet. Kodexempel som använder Microsoft mellanprogram, finns det [komma igång](active-directory-appmodel-v2-overview.md#getting-started) avsnittet.
* Om du skapar ett skrivbord eller mobila program kan använda du en av förhandsversionen av Microsoft Authentication Libraries (MSAL). Dessa bibliotek finns i en förhandsversion av produktion som stöds, så det är säkert att använda dem i produktionsprogram. Du kan läsa mer om villkoren för förhandsversionen och tillgängliga bibliotek i [autentisering bibliotek referens](active-directory-v2-libraries.md).
* För plattformar som inte omfattas av Microsoft-bibliotek, kan du integrera med v2.0-slutpunkten genom att skicka och ta emot protokollmeddelanden i din programkod direkt. Protokollen v2.0 OpenID Connect och OAuth [dokumenteras uttryckligen](active-directory-v2-protocols.md) för att utföra en sådan integration.
* Slutligen kan du använda öppna ID Connect och OAuth bibliotek med öppen källkod för integrering med v2.0-slutpunkten. V2.0-protokollet ska vara kompatibel med många bibliotek för öppen källkod-protokollet utan större ändringar. Dessa typer av bibliotek varierar efter språk och plattform. Den [öppna ID Connect](http://openid.net/connect/) och [OAuth 2.0](http://oauth.net/2/) webbplatser underhålla en lista över populära implementeringar. Mer information finns i [Azure Active Directory v2.0 och autentisering bibliotek](active-directory-v2-libraries.md), och en lista över klientbibliotek med öppen källkod och exempel som har testats med v2.0-slutpunkten.

## <a name="restrictions-on-protocols"></a>Begränsningar för protokoll

V2.0-slutpunkten har inte stöd för SAML eller WS-Federation Det stöder bara öppna ID Connect och OAuth 2.0. Inte alla funktioner och egenskaper i OAuth-protokoll har införlivats i v2.0-slutpunkten.

Följande protokoll-funktionerna och egenskaperna för närvarande är *inte tillgänglig* i v2.0-slutpunkten:

* För närvarande den `email` anspråk returneras bara om ett valfritt anspråk har konfigurerats och är omfånget = e-post har angetts i begäran. Det här beteendet ändras när v2.0-slutpunkten uppdateras för att uppfylla ytterligare öppna ID Connect och OAuth2.0-standarder.
* OpenID Connect användarinformationen slutpunkten har inte implementerats på v2.0-slutpunkten. Men alla data för användarprofiler som potentiellt skulle visas i den här slutpunkten är tillgänglig från Microsoft Graph `/me` slutpunkt.
* V2.0-slutpunkten har inte stöd för utfärdande roll eller grupp anspråk i ID-token.
* Den [OAuth 2.0 Resource ägare lösenord klientautentiseringsuppgifter](https://tools.ietf.org/html/rfc6749#section-4.3) stöds inte av v2.0-slutpunkten.

V2.0-slutpunkten stöder dessutom inte någon form av SAML- eller WS-Federation-protokoll.

För att bättre förstå vilka protokoll-funktioner som stöds i v2.0-slutpunkten, Läs igenom vår [referens för OpenID Connect och OAuth 2.0-protokollet](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Begränsningar för arbets-och skolkonton

Om du har använt Active Directory Authentication Library (ADAL) i Windows-program, kanske du har dragit nytta av integrerad Windows-autentisering, som använder Security Assertion Markup Language (SAML) assertion bevilja. Med det här beviljandet federerade användare av Azure AD klienter tyst kan autentiseras med deras lokala Active Directory-instans utan att ange autentiseringsuppgifter. SAML assertion bevilja stöds för närvarande inte på v2.0-slutpunkten.
