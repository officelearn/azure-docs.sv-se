---
title: Azure Active Directory referens för att bryta ändringar | Microsoft Docs
description: Lär dig mer om ändringar som gjorts i Azure AD-protokollen som kan påverka ditt program.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 747b279b38ec3b73f19194825195f1b3450d4514
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834852"
---
# <a name="whats-new-for-authentication"></a>Vad är nytt för autentisering? 

>Få meddelanden om uppdateringar på den här sidan. Lägg bara till [den här URL: en](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) i din RSS-feed läsare.

Autentiserings systemet ändrar och lägger till funktioner kontinuerligt för att förbättra kompatibiliteten för säkerhet och standarder. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om följande uppgifter:

- De senaste funktionerna
- Kända problem
- Protokoll ändringar
- Inaktuell funktion

> [!TIP] 
> Den här sidan uppdateras regelbundet, så besök ofta. Om inget annat anges placeras ändringarna bara på plats för nyligen registrerade program.  

## <a name="upcoming-changes"></a>Kommande ändringar

2019 augusti: Framtvinga POST-semantik enligt URL-parsningsfel – dubbla parametrar utlöser ett fel, citat tecken över parametrar kommer inte längre att ignoreras och [BOM](https://www.w3.org/International/questions/qa-byte-order-mark) ignoreras.

## <a name="july-2019"></a>Juli 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Endast app-token för program med en enda klient utfärdas endast om klient programmet finns i resurs klienten

**Start datum**: 26 juli 2019

**Påverkade slut punkter**: Både [v 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) och [v 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protokoll som påverkas**: [Klientautentiseringsuppgifter (endast app-token)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

En säkerhets ändring förblev 26 i juli som ändrar det sätt på vilket endast app-token (via tilldelning av klientautentiseringsuppgifter) utfärdas. Tidigare har program tillåtits att hämta token för att anropa alla andra appar, oavsett närvaro i klienten eller roller som meddelats för programmet.  Det här beteendet har uppdaterats så att resurser (ibland kallade webb-API: er) är en enskild klient (standard) och att klient programmet måste finnas i resurs klienten.  Observera att befintliga medgivande mellan klienten och API: n fortfarande inte behövs, och apparna bör fortfarande utföra sina egna verifierings kontroller för att säkerställa att `roles` det finns ett anspråk och innehåller det förväntade värdet för API: et.

Fel meddelandet för det här scenariot är för närvarande tillstånd: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Du kan åtgärda det här problemet genom att använda administratörs medgivande för att skapa klient program tjänstens huvud namn i din klient organisation eller skapa den manuellt.  Detta krav garanterar att klienten har gett programmet behörighet att köras i klienten.  

#### <a name="example-request"></a>Exempelbegäran

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`I det här exemplet är resurs klienten (auktoritet) contoso.com, Resource-appen är en app med en klient som anropas `gateway.contoso.com/api` för Contoso-klienten och klient programmet är. `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`  Om klient programmet har ett huvud namn för tjänsten i Contoso.com kan denna begäran fortsätta.  Om den inte gör det Miss känner dock begäran med ovanstående fel.  

Om contoso Gateway-appen var ett program för flera innehavare, skulle begäran fortsätta oavsett vilken klient app som har ett huvud namn för tjänsten i Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Omdirigerings-URI: er kan nu innehålla parametrar för frågesträng

**Start datum**: Den 22 juli 2019

**Påverkade slut punkter**: Både v 1.0 och v 2.0

**Protokoll som påverkas**: Alla flöden

Azure AD-program kan nu registrera och använda omdirigerings-URI: er med statiska frågeparametrar (till exempel https://contoso.com/oauth2?idp=microsoft) för OAuth 2,0-begäranden per [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2).  Dynamiska omdirigerings-URI: er är fortfarande förbjudna eftersom de utgör en säkerhets risk och det kan inte användas för att bevara tillståndsinformation i en autentiseringsbegäran – för den `state` , använder du parametern.

Den statiska Frågeparametern omfattas av sträng matchning för omdirigering av URI: er som andra delar av omdirigerings-URI: n – om ingen sträng har registrerats som matchar den URI-avkodade redirect_uri, kommer begäran att avvisas.  Om URI: n hittas i appens registrering, kommer hela strängen att användas för att omdirigera användaren, inklusive parametern för statisk fråga. 

Observera att i den här tiden (slutet av juli 2019), är app Registration-UX i Azure Portal fortfarande blockera frågeparametrar.  Du kan dock redigera applikations manifestet manuellt för att lägga till frågeparametrar och testa detta i appen.  


## <a name="march-2019"></a>Mars 2019

### <a name="looping-clients-will-be-interrupted"></a>Loopar till klienter avbryts

**Start datum**: 25 mars 2019

**Påverkade slut punkter**: Både v 1.0 och v 2.0

**Protokoll som påverkas**: Alla flöden

Klient program kan ibland vara felödande, vilket ger hundratals olika inloggnings begär Anden under en kort tids period.  Dessa förfrågningar kanske inte lyckas, men alla bidrar till dåliga användar upplevelser och förhöjda arbets belastningar för IDP, ökande svars tid för alla användare och minskad tillgänglighet för IDP.  Programmen körs utanför gränserna för normal användning och bör uppdateras så att de fungerar korrekt.  

Klienter som utfärdar dubbla förfrågningar flera gånger skickas ett `invalid_grant` fel:. `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` 

De flesta klienter behöver inte ändra beteende för att undvika det här felet.  Det här felet påverkas bara av felkonfigurerade klienter (de utan cachelagring av token eller de som uppvisar prompterna redan).  Klienter spåras baserat på varje instans lokalt (via cookie) på följande faktorer:

* Användar tips, om något

* Omfattningar eller resurs som begärs

* Klientorganisations-ID

* Omdirigerings-URI

* Svars typ och läge

Appar som gör flera förfrågningar (15 +) under en kort tids period (5 minuter) får ett `invalid_grant` fel som förklarar att de är slingor.  De token som begärs har tillräckligt lång livs längd per timme (minst 10 minuter, 60 minuter som standard), så upprepade begär Anden under den här tids perioden är onödigt.  

Alla appar bör hantera `invalid_grant` genom att visa en interaktiv fråga i stället för att begära en token i bakgrunden.  För att undvika det här felet bör klienterna se till att de cachelagrar de token som de tar emot korrekt.


## <a name="october-2018"></a>Oktober 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Auktoriseringsregler kan inte längre återanvändas

**Start datum**: 15 november 2018

**Påverkade slut punkter**: Både v 1.0 och v 2.0

**Protokoll som påverkas**: [Kod flöde](v2-oauth2-auth-code-flow.md)

Från och med 15 November 2018, att Azure AD sluta acceptera tidigare autentisering-koder för appar. Ändringen security hjälper dig att ta med Azure AD i enlighet med OAuth-specifikationen och tillämpas på både v1 och v2-slutpunkter.

Om din app återanvänder auktoriseringskoder för att hämta token för flera resurser, rekommenderar vi att du använda koden för att få en uppdateringstoken och sedan använda den uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger i flera resurser. Alla nya appar som försöker återanvända en autentiseringsmetod under OAuth-kodfragmentet får ett invalid_grant-fel.

Mer information om uppdateringstoken finns i [Uppdatera åtkomsttoken](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  Om du använder ADAL eller MSAL hanteras detta av biblioteket – Ersätt den andra instansen av "AcquireTokenByAuthorizationCodeAsync" med "AcquireTokenSilentAsync". 

## <a name="may-2018"></a>Maj 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Det går inte att använda ID-token för OBO-flödet

**Datum**: Den 1 maj 2018

**Påverkade slut punkter**: Både v 1.0 och v 2.0

**Protokoll som påverkas**: Implicit flöde och [OBO flöde](v1-oauth2-on-behalf-of-flow.md)

Efter den 1 maj 2018 går det inte att använda id_tokens som intyget i ett OBO-flöde för nya program. Åtkomsttoken ska användas i stället för att skydda API: er, även mellan en klient och mellan nivån för samma program. Appar som registrerats före den 1 maj 2018 kommer att fortsätta att fungera och kunna byta id_tokens för en åtkomsttoken. Detta mönster anses dock inte vara en bästa praxis.

För att undvika den här ändringen kan du göra följande:

1. Skapa ett webb-API för ditt program med ett eller flera omfång. Den här explicita start punkten ger bättre kornig kontroll och säkerhet.
1. I appens manifest, i [Azure Portal](https://portal.azure.com) eller i appens [registrerings Portal](https://apps.dev.microsoft.com), kontrollerar du att appen tillåts utfärda åtkomsttoken via det implicita flödet. Detta styrs via `oauth2AllowImplicitFlow` nyckeln.
1. När klient programmet begär en id_token via `response_type=id_token`, så begär även en åtkomsttoken (`response_type=token`) för webb-API: et som skapats ovan. När du använder v 2.0-slutpunkten `scope` bör parametern se ut ungefär `api://GUID/SCOPE`så här. På den v 1.0-slut punkten `resource` ska parametern vara app-URI: n för webb-API: et.
1. Skicka åtkomsttoken till den mittersta nivån i stället för id_token.  
