---
title: Autentisering i Microsoft identity-plattformen | Azure
description: Lär dig mer om autentisering i Microsoft identity-plattformen, appen modellera, API, etablering, och har stöd för de vanligaste scenarierna för autentisering som Microsoft identity-plattformen.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1d54347b9a3ccc72cfd5b88400d699d93132fbf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266332"
---
# <a name="what-is-authentication"></a>Vad är autentisering?

*Autentisering* innebär att en part måste ange giltiga uppgifter och utgör grunden för att skapa ett säkerhetsobjekt som används för identitets- och åtkomstkontroll. Enklare sagt är det en process där du bevisar att du är den du säger att du är. Autentisering förkortas ibland AuthN.

*Auktorisering* innebär att bevilja ett autentiserat säkerhetsobjekt behörighet att göra något. Det anger vilka du data du får åtkomst till och vad du kan gör med dem. Auktorisering förkortas ibland AuthZ.

Microsoft identity-plattformen förenklar autentisering för programutvecklare genom att tillhandahålla identitet som en tjänst, med stöd för branschstandardprotokoll som OAuth 2.0 och OpenID Connect, samt bibliotek med öppen källkod för olika plattformar till hjälper dig att börja koda snabbt.

Det finns två huvudsakliga typer i Microsoft identity-plattformen programmeringsmodell:

* Under ett beviljande flöde för OAuth 2.0-auktorisering – när resursägaren beviljar behörighet för klientprogrammet och tillåter klienten åtkomst till resursägarens resurser.
* Under resursåtkomst av klienten – enligt implementering av resursservern, med anspråksvärden som finns i åtkomsttoken för att fatta beslut om åtkomstkontroll utifrån dem.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>Grunder i Microsoft identity-plattformen

Föreställ dig det enklaste scenariot där det krävs identitet: en användare i en webbläsare behöver autentiseras för ett webbprogram. Följande diagram visar det här scenariot:

![Översikt över inloggning till webbprogram](./media/authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Det här behöver du känna till om olika komponenter som visas i diagrammet:

* Microsoft identity-plattformen är identitetsprovidern. Identitetsprovidern ansvarar för att verifiera identiteten för användare och program som finns i en organisations katalog och utfärdar säkerhetstoken vid utförd autentisering av de användarna och programmen.
* Ett program som vill flytta över autentisering till Microsoft identity-plattformen måste registreras i Azure Active Directory (AD Azure). Azure AD registrerar och identifierar unikt appen i katalogen.
* Utvecklare kan använda autentiseringsbibliotek för öppen källkod Microsoft identity-plattformen för att göra autentisering enkelt genom att hantera protocol-information för dig. Mer information finns i Microsofts identitetsplattform [v2.0 autentiseringsbibliotek](reference-v2-libraries.md) och [v1.0 autentiseringsbibliotek](active-directory-authentication-libraries.md).
* När en användare har autentiserats måste programmet validera användarens säkerhetstoken för att säkerställa att autentiseringen har utförts. Du hittar snabbstarter, självstudier och kodexempel på flera olika språk och ramverk som visar vad programmet måste göra.
  * Om du snabbt vill bygga en app och lägga till funktioner som att hämta tokens, uppdatera tokens, logga in en användare, visa användarinformation med mera läser du avsnittet **Snabbstarter** i dokumentationen.
  * Om du vill ha djupgående, scenariobaserade procedurer för vanliga uppgifter för auktoriseringsutveckling som att hämta åtkomsttoken och använda dem i anrop till Microsoft Graph API och andra API:er, implementera inloggning med Microsoft med en traditionell webbläsarbaserad app som använder OpenID Connect, med mera, läser du avsnittet **Självstudier** i dokumentationen.
  * Du kan ladda ned kodexempel på [GitHub](https://github.com/Azure-Samples?q=active-directory).
* Flödet för begäranden och svar för autentiseringsprocessen bestäms av autentiseringsprotokollet som du har använt, till exempel OAuth 2.0, OpenID Connect, WS-Federation eller SAML 2.0. Mer information om protokoll finns i avsnittet **Begrepp > Protokoll** i dokumentationen.

I exempelscenariot ovan kan du klassificera apparna enligt dessa två roller:

* Appar som behöver säker åtkomst till resurser
* Appar som har rollen av själva resursen

Nu när du har en översikt över grunderna, Läs vidare för att förstå app identitetsmodellen och API: et stöder hur etablering fungerar i Microsoft identity-plattformen och länkar till detaljerad information om vanliga scenarier som Microsoft identity-plattformen.

## <a name="application-model"></a>Programmodell

Microsoft identity-plattformen representerar program efter en viss modell som har utformats för att uppfylla två huvudfunktioner:

* **Identifiera appen enligt de autentiseringsprotokoll den stöder** – Detta innebär uppräkning av alla identifierare, URL:er, hemligheter och relaterad information som behövs vid identifieringen. Här kan Microsoft identity-plattformen:

    * Innehåller alla data som krävs för att stödja autentisering vid körning.
    * Innehåller alla data för att besluta vilka resurser en app kan behöva för åtkomst och om en viss begäran ska uppfyllas och under vilka omständigheter.
    * Tillhandahåller infrastrukturen för implementering av appetableringen i apputvecklarens klientorganisation och i andra Azure AD-klientorganisationer.

* **Hantera användargodkännande under tokenbegäran tid och underlättar dynamisk etablering av appar över klienter** – här kan Microsoft identity-plattformen:

    * Möjliggör för användare och administratörer att dynamiskt bevilja eller neka medgivande för appen att få åtkomst till resurser för deras räkning.
    * Möjliggör för administratörer att i slutänden bestämma vilka appar som tillåts göra vad och vilka användare som kan använda specifika appar samt hur åtkomsten till katalogresurserna går till.

I Microsoft identity-plattformen, en **programobjektet** beskriver ett program som en abstrakt entitet. Utvecklare kan arbeta med program. Vid tidpunkten för distribution, Microsofts identitetsplattform använder ett visst program-objekt som en skiss för att skapa en **tjänstens huvudnamn**, som representerar en konkret instans av ett program i en katalog eller klient. Det är tjänstens huvudnamn som definierar vad appen faktiskt kan göra i en specifik målkatalog, vilka som kan använda den, vilka resurser den har åtkomst till och så vidare. Microsoft identity-plattformen skapar ett huvudnamn för tjänsten från ett programobjekt via **godkänna**.

Följande diagram visar en förenklad Microsoft identity-plattformen etablering flöde som drivs av medgivande.  I den finns två klienter (A och B), där klient A äger programmet och klient B instansiera programmet via ett huvudnamn för tjänsten.  

![Förenklat etableringsflöde som drivs av medgivande](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

I det här etableringsflödet sker följande:

|   |   |
|---|---|
| 1 | En användare från klient B försöker logga in med appen |
| 2 | Autentiseringsuppgifterna hämtas och verifieras |
| 3 | Användaren uppmanas att godkänna att appen får åtkomst till klientorganisation B |
| 4 | Microsofts identitetsplattform använder programobjektet i A som underlag för att skapa ett tjänstens huvudnamn i klient B |
| 5 | Användaren får begärd token |
|   |   |

Du kan upprepa den här processen så många gånger du vill för andra klientorganisationer (C, D och så vidare). Klient A behåller skissen för appen (programobjektet). Användare och administratörer för alla andra klientorganisationer där appen får medgivande behåller kontrollen över vad programmet tillåts göra genom motsvarande tjänsthuvudobjekt i varje klientorganisation. Mer information finns i [program och tjänstobjekt i Microsoft identity-plattformen](app-objects-and-service-principals.md).

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>Anspråk i säkerhetstoken för Microsoft identity-plattformen

Säkerhetstoken (åtkomst och ID-token) utfärdats av Microsoft identity-plattformen innehåller anspråk eller intyg för information om ämnet som har autentiserats. Program kan använda anspråk för olika uppgifter, till exempel:

* Validera token
* Identifiera subjektets katalogklientorganisation
* Visa användarinformation
* Fastställa subjektets auktorisering

Anspråk som finns i alla angivna säkerhetstoken beror på typen av token, på typen av autentiseringsuppgift som används för att autentisera användaren och på programkonfigurationen.

En kort beskrivning av varje typ av anspråk som orsakats av Microsoft identity-plattformen finns i tabellen nedan. Mer information finns i den [åtkomsttoken](access-tokens.md) och [ID-token](id-tokens.md) utfärdats av Microsoft identity-plattformen.

| Begäran | Beskrivning |
| --- | --- |
| Program-ID:t | Identifierar programmet som använder token. |
| Målgrupp | Identifierar mottagarresursen som token är avsedd för. |
| Application Authentication Context Class Reference | Anger hur klienten har autentiserats (offentlig eller konfidentiell klient). |
| Autentiseringstillfälle | Registrerar datum och tid när autentiseringen inträffade. |
| Autentiseringsmetod | Anger hur subjektet för token har autentiserats (lösenord, certifikat osv.). |
| Förnamn | Innehåller förnamnet på användaren som det anges i Azure AD. |
| Grupper | Innehåller objekt-ID:n för Azure AD-grupper som användaren är medlem i. |
| Identitetsprovider | Registrerar den identitetsprovider som har autentiserat subjektet för token. |
| Utfärdad | Registrerar tiden då token utfärdats, används ofta för tokens uppdateringsbarhet. |
| Utfärdare | Identifierar STS som genererat token samt Azure AD-klientorganisationen. |
| Efternamn | Innehåller efternamnet på användaren som det anges i Azure AD. |
| Namn | Innehåller ett läsbart värde som identifierar subjektet för token. |
| Objekt-ID | Innehåller en oföränderlig, unik identifierare för subjektet i Azure AD. |
| Roller | Innehåller egna namn på Azure AD-programroller som användaren har beviljats. |
| Scope | Anger de behörigheter som beviljats för klientprogrammet. |
| Subjekt | Anger huvudnamnet som token kontrollerar information om. |
| Klient-ID:t | Innehåller ett oföränderlig, unik identifierare för katalogklientorganisationen som har utfärdat token. |
| Tokenlivstid | Definierar tidsintervallet då token är giltig. |
| User Principal Name | Innehåller UPN för subjektet. |
| Version | Innehåller versionsnumret för token. |

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om den [programtyper och scenarier som stöds i Microsoft identity-plattformen](app-types.md)
