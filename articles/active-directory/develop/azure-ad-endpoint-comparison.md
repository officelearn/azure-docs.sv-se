---
title: Varför uppdatera till Microsoft Identity Platform (v 2.0) | Azure
description: Känn till skillnaderna mellan Microsoft Identity Platform (v 2.0)-slut punkten och Azure Active Directory (Azure AD) v 1.0-slutpunkten och lär dig fördelarna med att uppdatera till v 2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5a000d08afb3afba06d82aae4414e87b61e502f
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533043"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Varför ska du uppdatera till Microsoft Identity Platform (v 2.0)?

När du utvecklar ett nytt program är det viktigt att känna till skillnaderna mellan Microsoft Identity Platform (v 2.0) och Azure Active Directory (v 1.0) slut punkter. Den här artikeln beskriver de viktigaste skillnaderna mellan slut punkterna och några befintliga begränsningar för Microsoft Identity Platform.

> [!NOTE]
> Slut punkten för Microsoft Identity Platform stöder inte alla Azure AD-scenarier och-funktioner. För att avgöra om du ska använda Microsoft Identity Platform-slutpunkten läser du om [begränsningar för Microsoft Identity Platform](#limitations).

## <a name="who-can-sign-in"></a>Vem kan logga in

![Vem kan logga in med v 1.0-och v 2.0-slut punkter](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* V 1.0-slutpunkten tillåter endast arbets-och skol konton att logga in i ditt program (Azure AD)
* Med Microsoft Identity Platform-slutpunkten kan du logga in med arbets-och skol konton från Azure AD och personliga Microsoft-konton (MSA), till exempel hotmail.com, outlook.com och msn.com.
* Båda slut punkterna accepterar också inloggnings program för *[gäst användare](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* i en Azure AD-katalog för program som kon figurer ATS som *[en enskild klient](single-and-multi-tenant-apps.md)* eller för program *med flera klienter* som kon figurer ATS för att peka på den klient-/regionsspecifika slut punkten (`https://login.microsoftonline.com/{TenantId_or_Name}`).

Med slut punkten för Microsoft Identity Platform kan du skriva appar som accepterar inloggningar från personliga Microsoft-konton och arbets-och skol konton. Det ger dig möjlighet att skriva appens fullständigt konto-oberoende. Om din app till exempel anropar [Microsoft Graph](https://graph.microsoft.io), kommer vissa ytterligare funktioner och data att vara tillgängliga för arbets konton, till exempel SharePoint-webbplatser eller katalog data. Men för många åtgärder, till exempel [läsning av en användares e-post](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), kan samma kod komma åt e-postmeddelandet för både personliga konton och arbets-och skol konton.

För Microsoft Identity Platform-slutpunkten kan du använda Microsoft Authentication Library (MSAL) för att få till gång till konsument-, utbildnings-och företags världar. Azure AD v 1.0-slutpunkten accepterar bara inloggningar från arbets-och skol konton.

## <a name="incremental-and-dynamic-consent"></a>Det stegvisa och dynamiska godkännandet

Appar som använder Azure AD v 1.0-slutpunkten krävs för att ange deras obligatoriska OAuth 2,0-behörigheter i förväg, till exempel:

![Exempel som visar användar gränssnittet för behörighets registrering](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Behörigheterna som anges direkt på program registreringen är **statiska**. Även om statiska behörigheter för appen som definierats i Azure Portal låter koden vara Nice och enkel, visar den några möjliga problem för utvecklare:

* Appen måste begära alla behörigheter som den skulle behöva vid användarens första inloggning. Detta kan leda till en lång lista med behörigheter som förhindrar att slutanvändarna godkänner appens åtkomst vid första inloggningen.

* Appen måste känna till alla resurser som den skulle ha haft till gång till i förväg. Det var svårt att skapa appar som kunde komma åt ett godtyckligt antal resurser.

Med Microsoft Identity Platform-slutpunkten kan du ignorera de statiska behörigheter som definierats i appens registrerings information i Azure Portal och begär behörigheter stegvis i stället, vilket innebär att du frågar efter en minimal uppsättning behörigheter som är aktiva och växer mer med tiden när kunden använder ytterligare funktioner i appen. Om du vill göra det kan du ange de omfattningar som appen behöver när som helst genom att inkludera de nya omfattningarna i parametern `scope` när du begär en åtkomsttoken – utan att behöva definiera dem i program registrerings informationen. Om användaren ännu inte har samtyckt till nya omfattningar som lagts till i begäran, uppmanas de bara att godkänna de nya behörigheterna. Läs mer i [behörigheter, medgivande och omfång](v2-permissions-and-consent.md).

Genom att tillåta en app att begära behörigheter dynamiskt via `scope`-parametern ger utvecklare fullständig kontroll över din användar upplevelse. Du kan också läsa in din medgivande upplevelse och fråga efter alla behörigheter i en första auktoriseringsbegäran-begäran. Om din app kräver ett stort antal behörigheter kan du samla in dessa behörigheter från användaren stegvis när de försöker använda vissa funktioner i appen över tid.

Administratörs medgivande som gjorts för en organisations räkning kräver fortfarande de statiska behörigheter som registrerats för appen, så du bör ange dessa behörigheter för appar i appens registrerings portal om du behöver en administratör för att ge tillåtelse åt hela organisationen. Detta minskar de cykler som krävs av organisationens administratör för att konfigurera programmet.

## <a name="scopes-not-resources"></a>Omfattningar, inte resurser

För appar som använder v 1.0-slutpunkten kan en app beter sig som en **resurs**eller en mottagare av tokens. En resurs kan definiera ett antal **omfattningar** eller **oAuth2Permissions** som den förstår, vilket gör det möjligt för klient program att begära token från den resursen för en viss uppsättning omfång. Överväg Microsoft Graph-API: et som ett exempel på en resurs:

* Resurs-ID eller `AppID URI`: `https://graph.microsoft.com/`
* Omfattningar eller `oAuth2Permissions`: `Directory.Read`, `Directory.Write`och så vidare.

Detta gäller för Microsoft Identity Platform-slutpunkten. En app kan fortfarande fungera som en resurs, definiera omfång och identifieras av en URI. Klient program kan fortfarande begära åtkomst till dessa scope. Men det kan vara så att en klient begär dessa behörigheter.

För den v 1.0-slutpunkten kan en OAuth 2,0-auktorisera begäran till Azure AD ha tittat på:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Här indikerade **resurs** parametern vilken resurs som klient appen begär auktorisering för. Azure AD beräknade de behörigheter som krävs av appen baserat på statisk konfiguration i Azure Portal och utfärdade tokens.

För program som använder Microsoft Identity Platform-slutpunkten ser samma OAuth 2,0-auktoriserade begäran ut så här:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Här anger **omfattnings** parametern vilken resurs och vilka behörigheter appen begär auktorisering. Den önskade resursen finns fortfarande i begäran – den ingår i värdena för omfattnings parametern. Genom att använda omfattnings parametern på det här sättet kan Microsoft Identity Platform-slutpunkten bli mer kompatibel med OAuth 2,0-specifikationen och anpassa sig närmare med vanliga bransch praxis. Det gör det också möjligt för appar att göra ett [stegvist godkännande](#incremental-and-dynamic-consent) – endast begära behörighet när programmet kräver dem i stället för att placeras överst.

## <a name="well-known-scopes"></a>Välkända omfattningar

### <a name="offline-access"></a>Åtkomst offline

Appar som använder Microsoft Identity Platform-slutpunkten kan behöva använda en ny välkänd behörighet för appar – `offline_access` omfattningen. Alla appar måste begära den här behörigheten om de behöver åtkomst till resurser för en användares räkning under en längre tids period, även om användaren inte kan använda appen aktivt. `offline_access` omfattningen visas för användaren i medgivande dialog rutor som **åtkomst till dina data när som helst**, vilka användaren måste godkänna. Genom att begära `offline_access` behörighet kommer din webbapp att kunna ta emot OAuth 2,0-refresh_tokens från Microsoft Identity Platform-slutpunkten. Uppdaterade token är långa och kan bytas ut för nya OAuth 2,0-åtkomsttoken för utökade åtkomst perioder.

Om din app inte begär `offline_access` omfattningen får den inte några uppdaterade tokens. Det innebär att när du löser in en auktoriseringskod i OAuth 2,0-Authorization Code Flow får du bara tillbaka en åtkomsttoken från `/token` slut punkten. Denna åtkomsttoken är fortfarande giltig under en kort tids period (vanligt vis en timme), men kommer att upphöra att gälla. Vid den tidpunkten måste din app omdirigera användaren tillbaka till `/authorize` slut punkten för att hämta en ny auktoriseringskod. Under den här omdirigeringen kanske användaren inte behöver ange sina autentiseringsuppgifter igen eller medgivande till behörigheter, beroende på typ av app.

Om du vill veta mer om OAuth 2,0, `refresh_tokens`och `access_tokens`kan du titta på [referens för Microsoft Identity Platform-protokollet](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profil och e-post

Tidigare var det mest grundläggande OpenID Connect-inloggnings flödet med Microsoft Identity Platform en mycket information om användaren i den resulterande *id_token*. Anspråk i en id_token kan innehålla användarens namn, önskat användar namn, e-postadress, objekt-ID med mera.

Den information som `openid` omfattning ger åtkomst till appen är nu begränsad. `openid`s omfånget tillåter bara att din app loggar in användaren och tar emot en app-Specific Identifier för användaren. Om du vill hämta personliga data om användaren i din app måste appen begära ytterligare behörigheter från användaren. Med två nya omfattningar, `email` och `profile`, kan du begära ytterligare behörigheter.

* `email`s området ger appen åtkomst till användarens primära e-postadress via `email`-anspråk i id_token, förutsatt att användaren har en adresserad e-postadress.
* `profile` omfattningen ger appen åtkomst till all annan grundläggande information om användaren, t. ex. namn, önskat användar namn, objekt-ID och så vidare, i id_token.

Med de här omfången kan du koda din app på ett minimalt sätt så att du bara kan fråga användaren om den information som appen behöver för att utföra sitt jobb. Mer information om de här omfattningarna finns i [referens för Microsoft Identity Platform](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Token-anspråk

Slut punkten för Microsoft Identity Platform utfärdar en mindre uppsättning anspråk i sina tokens som standard för att bevara nytto laster små. Om du har appar och tjänster som har ett beroende på ett visst anspråk i en v 1.0-token som inte längre anges som standard i en Microsoft Identity Platform-token, bör du överväga att använda funktionen [valfria anspråk](active-directory-optional-claims.md) för att ta med detta anspråk.

> [!IMPORTANT]
> v 1.0-och v 2.0-token kan utfärdas av både v 1.0-och v 2.0-slutpunkter! id_tokens *alltid* matcha den slut punkt som de begär från, och åtkomsttoken matchar *alltid* det format som förväntas av webb-API: et som klienten anropar med denna token.  Så om din app använder v 2.0-slutpunkten för att hämta en token för att anropa Microsoft Graph, vilket förväntar sig ett token för v 1.0-format, får appen en token i formatet v 1.0.  

## <a name="limitations"></a>Begränsningar

Det finns några begränsningar som du bör känna till när du använder Microsoft Identity Platform.

När du skapar program som integreras med Microsoft Identity Platform måste du bestämma om slut punkten och autentiseringsprotokollen för Microsoft Identity Platform uppfyller dina behov. Slut punkten och plattformen för v 1.0 stöds fortfarande fullt ut, och i vissa hänseende är fler funktioner mer avancerade än Microsoft Identity Platform. Microsoft Identity Platform [introducerar emellertid betydande fördelar](azure-ad-endpoint-comparison.md) för utvecklare.

Här är en förenklad rekommendation för utvecklare nu:

* Om du vill ha eller behöver stöd för personliga Microsoft-konton i ditt program, eller om du skriver ett nytt program, använder du Microsoft Identity Platform. Men innan du gör det, se till att du förstår de begränsningar som beskrivs i den här artikeln.
* Om du migrerar eller uppdaterar ett program som förlitar sig på SAML kan du inte använda Microsoft Identity Platform. Se i stället till [Azure AD v 1.0-guiden](v1-overview.md).

Slut punkten för Microsoft Identity Platform kommer att utvecklas för att eliminera de begränsningar som anges här, så att du bara behöver använda Microsoft Identity Platform-slutpunkten. Under tiden kan du använda den här artikeln för att avgöra om slut punkten för Microsoft Identity Platform är rätt för dig. Vi fortsätter att uppdatera den här artikeln för att återspegla den aktuella statusen för Microsoft Identity Platform-slutpunkten. Kom tillbaka för att utvärdera dina krav mot Microsoft Identity Platform-funktioner.

### <a name="restrictions-on-app-registrations"></a>Begränsningar för app-registreringar

För varje app som du vill integrera med Microsoft Identity Platform-slutpunkten kan du skapa en app-registrering i den nya [ **Appregistreringars** miljön](https://aka.ms/appregistrations) i Azure Portal. Befintliga Microsoft-konto appar är inte kompatibla med portalen, men alla Azure AD-appar är, oavsett var eller när de registrerades.

Appregistreringar som har stöd för arbets-och skol konton och personliga konton har följande varningar:

* Endast två program hemligheter tillåts per program-ID.
* Ett program som inte har registrerats i en klient organisation kan bara hanteras av det konto som registrerade det. Den kan inte delas med andra utvecklare. Detta är fallet för de flesta appar som registrerades med en personlig Microsoft-konto i registrerings portalen för appen. Om du vill dela din app-registrering med flera utvecklare registrerar du programmet i en klient med hjälp av den nya **Appregistreringar** avsnittet av Azure Portal.
* Det finns flera begränsningar för formatet för den omdirigerings-URL som tillåts. Mer information om omdirigerings-URL finns i nästa avsnitt.

### <a name="restrictions-on-redirect-urls"></a>Begränsningar för omdirigerings-URL: er

Appar som har registrerats för Microsoft Identity Platform är begränsade till en begränsad uppsättning URL-värden för omdirigering. Omdirigerings-URL: en för webbappar och tjänster måste börja med schemat `https`, och alla omdirigerings-URL-värden måste dela en enda DNS-domän.  Registrerings systemet jämför hela DNS-namnet på den befintliga omdirigerings-URL: en till DNS-namnet för den omdirigerings-URL som du lägger till. `http://localhost` stöds också som en omdirigerings-URL.  

Begäran om att lägga till DNS-namnet kommer att misslyckas om något av följande villkor föreligger:  

* Hela DNS-namnet för den nya omdirigerings-URL: n matchar inte DNS-namnet på den befintliga omdirigerings-URL: en
* Hela DNS-namnet för den nya omdirigerings-URL: en är inte en under domän till den befintliga omdirigerings webb adressen.

#### <a name="example-1"></a>Exempel 1

Om appen har en omdirigerings-URL för `https://login.contoso.com`kan du lägga till en omdirigerings-URL där DNS-namnet matchar exakt, som du ser i följande exempel:

`https://login.contoso.com/new`

Eller så kan du referera till en DNS-underdomän för login.contoso.com, som du ser i följande exempel:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Exempel 2

Om du vill ha en app som har `login-east.contoso.com` och `login-west.contoso.com` som omdirigerings-URL: er måste du lägga till dessa omdirigerings-URL: er i följande ordning:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Du kan lägga till de senare två eftersom de är under domäner för den första omdirigerings-URL: en, contoso.com.

Du kan bara ha 20 svars-URL: er för ett visst program – den här gränsen gäller för alla typer av appar som registreringen stöder (SPA), ursprunglig klient, webbapp och tjänst).  

Information om hur du registrerar en app för användning med Microsoft Identity Platform finns i [Registrera en app med hjälp av den nya Appregistreringar-upplevelsen](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Begränsningar för bibliotek och SDK: er

Biblioteks stöd för Microsoft Identity Platform-slutpunkten är för närvarande begränsad. Om du vill använda Microsoft Identity Platform-slutpunkten i ett produktions program har du följande alternativ:

* Om du skapar ett webb program kan du på ett säkert sätt använda den allmänt tillgängliga mellanliggande server sidan för att utföra inloggning och verifiering av token. Det inkluderar OWIN OpenID Connect-programprogram för ASP.NET och Node. js Passport-plugin-programmet. Kod exempel som använder Microsoft mellanprogram finns i avsnittet [komma igång med Microsoft Identity Platform](v2-overview.md#getting-started) .
* Om du skapar ett skriv bord eller ett mobilt program kan du använda något av Microsoft-MSAL (Authentication Libraries). Dessa bibliotek är allmänt tillgängliga eller i en för hands version som stöds för produktion, så det är säkert att använda dem i produktions program. Du kan läsa mer om villkoren i för hands versionen och de tillgängliga biblioteken i [autentiserings biblioteks referensen](reference-v2-libraries.md).
* För plattformar som inte omfattas av Microsoft-bibliotek kan du integrera med Microsoft Identity Platform-slutpunkten genom att skicka och ta emot protokoll meddelanden direkt i program koden. OpenID Connect-och OAuth-protokollen [är uttryckligen dokumenterade](active-directory-v2-protocols.md) för att hjälpa dig att göra en sådan integrering.
* Slutligen kan du använda OpenID Connect och OAuth-bibliotek med öppen källkod för att integrera med Microsoft Identity Platform-slutpunkten. Microsoft Identity Platform-slutpunkten bör vara kompatibel med många protokoll bibliotek med öppen källkod utan ändringar. Tillgängligheten för de här typerna av bibliotek varierar beroende på språk och plattform. [OpenID Connect](https://openid.net/connect/) och [OAuth 2,0](https://oauth.net/2/) -webbplatser har en lista över populära implementeringar. Mer information finns i [Microsoft Identity Platform and Authentication libraries](reference-v2-libraries.md)och i listan över klient bibliotek med öppen källkod och exempel som har testats med Microsoft Identity Platform-slutpunkten.
* För referens är `.well-known` slut punkten för den vanliga slut punkten för Microsoft Identity Platform `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Ersätt `common` med klient-ID: t för att hämta data som är speciella för din klient.  

### <a name="protocol-changes"></a>Protokoll ändringar

Slut punkten för Microsoft Identity Platform stöder inte SAML eller WS-Federation; den stöder bara OpenID Connect och OAuth 2,0.  De viktiga ändringarna i OAuth 2,0-protokollen från v 1.0-slut punkten är: 

* `email`-anspråk returneras om ett valfritt anspråk har kon figurer ATS **eller** om scope = email angavs i begäran. 
* Parametern `scope` stöds nu i stället för parametern `resource`.  
* Många svar har ändrats för att göra dem mer kompatibla med OAuth 2,0-specifikationen, till exempel att returnera `expires_in` som en int i stället för en sträng.  

För att bättre förstå omfattningen av de protokoll funktioner som stöds i Microsoft Identity Platform-slutpunkten, se [OpenID Connect och OAuth 2,0-protokoll referens](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>SAML-begränsningar

Om du har använt Active Directory-autentiseringsbibliotek (ADAL) i Windows-program kan du ha nytta av Windows-integrerad autentisering, som använder den beviljade SAML-kontrollen (Security Assertion Markup Language). Med det här bidraget kan användare av federerade Azure AD-klienter obevakat autentisera med sina lokala Active Directory-instanser utan att ange autentiseringsuppgifter. SAML Assertion-tilldelningen stöds inte på Microsoft Identity Platform-slutpunkten.
