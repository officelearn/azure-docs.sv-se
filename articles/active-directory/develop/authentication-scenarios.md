---
title: Autentisering i Azure Active Directory
description: Lär dig mer om autentisering i Azure Active Directory (Azure AD), appmodellen, API, etablering och de vanligaste autentiseringsscenarierna som Azure AD stöder.
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
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ms.openlocfilehash: db642fea6dbf1bd1ebc53f5066d01dc4f9a5490f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095641"
---
# <a name="what-is-authentication"></a>Vad är autentisering?

*Autentisering* innebär att en part måste ange giltiga uppgifter och utgör grunden för att skapa ett säkerhetsobjekt som används för identitets- och åtkomstkontroll. Enklare sagt är det en process där du bevisar att du är den du säger att du är. Autentisering förkortas ibland AuthN.

*Auktorisering* innebär att bevilja ett autentiserat säkerhetsobjekt behörighet att göra något. Det anger vilka du data du får åtkomst till och vad du kan gör med dem. Auktorisering förkortas ibland AuthZ.

Azure Active Directory (Azure AD) förenklar autentisering för programutvecklare genom att tillhandahålla identitet som en tjänst, med stöd för branschstandardprotokoll som OAuth 2.0 och OpenID Connect samt för bibliotek med öppen källkod för olika plattformar för att du ska kunna börja koda snabbt.

Det vinns två huvudsakliga typer i Azure AD-programmeringsmodellen:

* Under ett beviljande flöde för OAuth 2.0-auktorisering – när resursägaren beviljar behörighet för klientprogrammet och tillåter klienten åtkomst till resursägarens resurser.
* Under resursåtkomst av klienten – enligt implementering av resursservern, med anspråksvärden som finns i åtkomsttoken för att fatta beslut om åtkomstkontroll utifrån dem.

## <a name="authentication-basics-in-azure-active-directory"></a>Grundläggande om autentisering i Azure Active Directory

Föreställ dig det enklaste scenariot där det krävs identitet: en användare i en webbläsare behöver autentiseras för ett webbprogram. Följande diagram visar det här scenariot:

![Översikt över inloggning till webbprogram](./media/authentication-scenarios/basics_of_auth_in_aad.png)

Det här behöver du känna till om olika komponenter som visas i diagrammet:

* Azure AD är identitetsprovidern. Identitetsprovidern ansvarar för att verifiera identiteten för användare och program som finns i en organisations katalog och utfärdar säkerhetstoken vid utförd autentisering av de användarna och programmen.
* Ett program som vill flytta över autentiseringen till Azure AD måste registreras i Azure AD. Azure AD registrerar och identifierar unikt appen i katalogen.
* Utvecklare kan använda Azure AD-autentiseringsbiblioteken med öppen källkod för att göra autentiseringen enkel genom att hantera protokollinformationen åt dig. Mer information finns i Azure AD [v2.0-autentiseringsbibliotek](reference-v2-libraries.md) och [v1.0-autentiseringsbibliotek](active-directory-authentication-libraries.md).
* När en användare har autentiserats måste programmet validera användarens säkerhetstoken för att säkerställa att autentiseringen har utförts. Du hittar snabbstarter, självstudier och kodexempel på flera olika språk och ramverk som visar vad programmet måste göra.
  * Om du snabbt vill bygga en app och lägga till funktioner som att hämta tokens, uppdatera tokens, logga in en användare, visa användarinformation med mera läser du avsnittet **Snabbstarter** i dokumentationen.
  * Om du vill ha djupgående, scenariobaserade procedurer för vanliga uppgifter för auktoriseringsutveckling som att hämta åtkomsttoken och använda dem i anrop till Microsoft Graph API och andra API:er, implementera inloggning med Microsoft med en traditionell webbläsarbaserad app som använder OpenID Connect, med mera, läser du avsnittet **Självstudier** i dokumentationen.
  * Du kan ladda ned kodexempel på [GitHub](https://github.com/Azure-Samples?q=active-directory).
* Flödet för begäranden och svar för autentiseringsprocessen bestäms av autentiseringsprotokollet som du har använt, till exempel OAuth 2.0, OpenID Connect, WS-Federation eller SAML 2.0. Mer information om protokoll finns i avsnittet **Begrepp > Protokoll** i dokumentationen.

I exempelscenariot ovan kan du klassificera apparna enligt dessa två roller:

* Appar som behöver säker åtkomst till resurser
* Appar som har rollen av själva resursen

Nu när du har en översikt över grunderna kan du läsa vidare för att förstå identitetens appmodell och API, hur etablering fungerar i Azure AD och länkar till detaljerad information om vanliga scenarier som Azure AD stöder.

## <a name="application-model"></a>Programmodell

Azure AD representerar program enligt en specifik modell som har utformats för att uppfylla två huvudfunktioner:

* **Identifiera appen enligt de autentiseringsprotokoll den stöder** – Detta innebär uppräkning av alla identifierare, URL:er, hemligheter och relaterad information som behövs vid identifieringen. Här gör Azure AD följande:

    * Innehåller alla data som krävs för att stödja autentisering vid körning.
    * Innehåller alla data för att besluta vilka resurser en app kan behöva för åtkomst och om en viss begäran ska uppfyllas och under vilka omständigheter.
    * Tillhandahåller infrastrukturen för implementering av appetableringen i apputvecklarens klientorganisation och i andra Azure AD-klientorganisationer.

* **Hantera användarmedgivande vid tokenbegäran och underlätta dynamisk etablering av appar över klientorganisationer** – här gör Azure AD följande:

    * Möjliggör för användare och administratörer att dynamiskt bevilja eller neka medgivande för appen att få åtkomst till resurser för deras räkning.
    * Möjliggör för administratörer att i slutänden bestämma vilka appar som tillåts göra vad och vilka användare som kan använda specifika appar samt hur åtkomsten till katalogresurserna går till.

I Azure AD beskriver ett **programobjekt** ett program som en abstrakt entitet. Utvecklare kan arbeta med program. I utvecklingsarbetet använder Azure AD ett angivet programobjekt som en skiss för att skapa ett **huvudnamn för tjänsten**, som representerar en konkret instans av ett program i en katalog eller klientorganisation. Det är tjänstens huvudnamn som definierar vad appen faktiskt kan göra i en specifik målkatalog, vilka som kan använda den, vilka resurser den har åtkomst till och så vidare. Azure AD skapar ett huvudnamn för tjänsten utifrån ett programobjekt genom **medgivande**.

I följande diagram visas ett förenklat etableringsflöde för Azure AD som drivs av medgivande.

![Förenklat etableringsflöde som drivs av medgivande](./media/authentication-scenarios/simplified-provisioning-flow-consent.png)

I det här etableringsflödet sker följande:

|   |   |
|---|---|
| 1 | En användare från B försöker logga in med appen |
| 2 | Autentiseringsuppgifterna hämtas och verifieras |
| 3 | Användaren uppmanas att godkänna att appen får åtkomst till klientorganisation B |
| 4 | Azure AD använder programobjektet i A som skiss för att skapa ett huvudnamn för tjänstens i B |
| 5 | Användaren får begärd token |
|   |   |

Du kan upprepa den här processen så många gånger du vill för andra klientorganisationer (C, D och så vidare). Katalog A behåller skissen för appen (programobjektet). Användare och administratörer för alla andra klientorganisationer där appen får medgivande behåller kontrollen över vad programmet tillåts göra genom motsvarande tjänsthuvudobjekt i varje klientorganisation. Mer information finns i [Program- och tjänsthuvudobjekt i Azure AD](app-objects-and-service-principals.md).

## <a name="claims-in-azure-ad-security-tokens"></a>Anspråk i säkerhetstoken för Azure AD

Säkerhetstoken (åtkomst- och ID-token) som utfärdas av Azure AD innehåller anspråk, eller försäkringar för information om subjektet som har autentiserats. Program kan använda anspråk för olika uppgifter, till exempel:

* Validera token
* Identifiera subjektets katalogklientorganisation
* Visa användarinformation
* Fastställa subjektets auktorisering

Anspråk som finns i alla angivna säkerhetstoken beror på typen av token, på typen av autentiseringsuppgift som används för att autentisera användaren och på programkonfigurationen.

En kort beskrivning av varje typ av anspråk som genereras av Azure AD finns i tabellen nedan. Mer detaljerad information finns i [åtkomsttoken](access-tokens.md) och [ID-token](id-tokens.md) som utfärdas av Azure AD.

| Begär | Beskrivning |
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
| Omfång | Anger de behörigheter som beviljats för klientprogrammet. |
| Subjekt | Anger huvudnamnet som token kontrollerar information om. |
| Klient-ID:t | Innehåller ett oföränderlig, unik identifierare för katalogklientorganisationen som har utfärdat token. |
| Tokenlivstid | Definierar tidsintervallet då token är giltig. |
| User Principal Name | Innehåller UPN för subjektet. |
| Version | Innehåller versionsnumret för token. |

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om de [programtyper och scenarier i Azure Active Directory](app-types.md)
