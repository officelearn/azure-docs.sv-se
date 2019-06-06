---
title: Klientkonfigurationen program (Microsoft Authentication Library) | Azure
description: Läs mer om konfigurationsalternativen för offentlig klient och konfidentiell klient program i Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430808"
---
# <a name="application-configuration-options"></a>Programmets konfigurationsalternativ

I koden du initiera en ny offentlig eller konfidentiell klient (eller användar-agent för MSAL.js) program för att autentisera och hämta token. Du kan ange ett antal alternativ när du initiera klientappen i Microsoft Authentication Library (MSAL). Dessa alternativ kan delas i två grupper:

- Registreringsalternativ, inklusive:
    - [Utfärdare av](#authority) (består av identitetsprovidern [instans](#cloud-instance) och logga in [målgrupp](#application-audience) för appen och eventuellt klient-ID).
    - [Klient-ID](#client-id).
    - [Omdirigerings-URI](#redirect-uri).
    - [klienthemlighet](#client-secret) (för appar som konfidentiell klient).
- [Alternativ för loggning](#logging), inklusive loggningsnivå, kontroll över personliga data och namnet på komponenten med hjälp av biblioteket.

## <a name="authority"></a>Myndighet
Behörigheten är en URL som anger en katalog som MSAL kan begära token från. Vanliga myndigheter är:

- https://login.microsoftonline.com/&lt; klient&gt;/ där &lt;klient&gt; är klient-ID för Azure Active Directory (Azure AD)-klient eller en domän som är associerade med den här Azure AD-klient. Används endast för att logga in användare av en viss organisation.
- https://login.microsoftonline.com/common/. Används för att logga in användare med arbets- och skolkonton eller personliga Microsoft-konton.
- https://login.microsoftonline.com/organizations/. Används för att logga in användare med arbets-och skolkonton.
- https://login.microsoftonline.com/consumers/. Används för att logga in användare med endast personliga Microsoft-konton (kallades tidigare Windows Live ID-konton).

Inställningen av måste stämma överens med vad som är deklarerad i portalen för registrering av programmet.

Utfärdarens URL består av instansen och målgruppen.

Behörighet kan vara:
- En utfärdare av cloud Azure AD.
- en utfärdare av Azure AD B2C. Se [B2C ärendets](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- En utfärdare av Active Directory Federation Services (ADFS). Se [ADFS stöder](https://aka.ms/msal-net-adfs-support).

Azure AD-utfärdare för molnet har två delar:
- identitetsprovidern *instans*
- Inloggningen *målgrupp* för appen

Instansen och målgrupp kan sammanfogas och anges som utfärdare av URL: en. I tidigare versioner av MSAL.NET än MSAL 3. *x*, du var tvungen att skapa behörighet själv, baserat på molnet du ville mål och logga in målgruppen.  Det här diagrammet visar hur utfärdarens URL består:

![Hur utfärdarens URL består](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Molninstans
Den *instans* används för att ange om din app loggar användare från det offentliga Azure-molnet eller nationella moln. Med MSAL i din kod, du kan ange Azure-molnet-instans med hjälp av en uppräkning eller genom att skicka Webbadressen till den [nationella molninstans](authentication-national-cloud.md#azure-ad-authentication-endpoints) som den `Instance` medlem (om du känner till den).

MSAL.NET ett undantagsfel explicit om både `Instance` och `AzureCloudInstance` har angetts.

Om du inte anger en instans, din app är inriktat mot den offentliga Azure-moln-instansen (instansen av URL: en `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Programmet målgrupp

Logga in målgruppen är beroende av affärsbehov för din app:
- Om du är en verksamhetsspecifik (LOB) utvecklare kan skapa du en enda klient-program som ska användas endast i din organisation. I så fall kan behöva du ange organisation, antingen genom dess klient-ID (ID för din Azure AD-instans) eller genom att ett domännamn som är associerade med Azure AD-instans.
- Om du är en oberoende Programvaruleverantör, kanske du vill logga in användare med sina arbets- och skolkonton konton i en organisation eller i vissa organisationer (fleranvändarapp). Men du kanske också behöver användare logga in med sina personliga Microsoft-konton.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Så här anger du målgruppen i din kod/konfiguration
Med MSAL i din kod kan ange du målgruppen genom att använda någon av följande värden:
- Azure AD-utfärdare målgrupp uppräkning
- Klient-ID som kan vara:
  - En GUID (ID för din Azure AD-instans) för enstaka klientorganisationer program
  - Ett domännamn som är associerade med din Azure AD-instans (även för enstaka klientorganisationer program)
- En av dessa platshållare som en klient-ID i stället för uppräkningen Azure AD-utfärdare målgrupp:
    - `organizations` för ett program för flera
    - `consumers` logga in användare endast med sina personliga konton
    - `common` logga in med sitt arbete och skola konton eller deras personliga Microsoft-konton

MSAL genereras en meningsfull undantagsfel om du anger både Azure AD-utfärdare målgrupp och klient-ID.

Om du inte anger en målgrupp kommer din app är inriktat mot Azure AD och personliga Microsoft-konton som en målgrupp. (Det vill säga den beter sig som `common` har angetts.)

### <a name="effective-audience"></a>Effektiva målgrupp
Effektiva målgruppen för ditt program kommer att vara minst (om det finns en skärningspunkt) den målgrupp som du anger i din app och målgruppen som har angetts i appregistreringen. I själva verket den [appregistreringar](https://aka.ms/appregistrations) upplevelsen kan du ange målgruppen (stöds kontotyper) för appen. Mer information finns i [ Snabbstart: Registrera ett program med Microsoft identity-plattformen](quickstart-register-app.md).

För närvarande är det enda sättet att få en app för att logga in användare med endast personliga Microsoft-konton att konfigurera båda dessa inställningar:
- Inställd app registrering målgruppen `Work and school accounts and personal accounts`.
- Ange målgruppen i din kod/konfiguration och `AadAuthorityAudience.PersonalMicrosoftAccount` (eller `TenantID` = ”användare”).

## <a name="client-id"></a>Klientorganisations-ID
Klient-ID är det unika program (klient)-ID som din app tilldelats av Azure AD när appen har registrerats.

## <a name="redirect-uri"></a>Omdirigerings-URI
Omdirigerings-URI är identitetsprovidern skickar säkerhetstoken tillbaka till URI: N.

### <a name="redirect-uri-for-public-client-apps"></a>Omdirigerings-URI för offentlig klient apps
Om du är en apputvecklare för offentlig klient som använder MSAL:
- Du behöver inte skicka `RedirectUri` eftersom den beräknas automatiskt av MSAL. Omdirigeringen URI är inställd på något av dessa värden, beroende på vilken plattform:
   - `urn:ietf:wg:oauth:2.0:oob` för alla Windows-plattformar
   - `msal{ClientId}://auth` för Xamarin Android och iOS

- Du behöver konfigurera omdirigeringen URI: N i [appregistreringar](https://aka.ms/appregistrations):

   ![Omdirigerings-URI i App-registreringar](media/msal-client-application-configuration/redirect-uri.png)

Du kan åsidosätta omdirigerings-URI genom att använda den `RedirectUri` egenskapen (till exempel, om du använder asynkrona meddelandeköer). Här följer några exempel på omdirigerings-URI: er för scenariot:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $”msauth. {Bundle.ID}://auth ”;

Mer information finns i [dokumentationen för Android och iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>Omdirigerings-URI för konfidentiell klient apps
Är den URI som Azure AD ska använda för att skicka token tillbaka till programmet för web apps omdirigerings-URI: N (eller svars-URI). Detta kan vara URL för web app/webb-API om konfidentiell appen är en av dessa. Omdirigerings-URI måste vara registrerade i appregistreringen. Denna registrering är särskilt viktigt när du distribuerar en app som du ursprungligen har testat lokalt. Du måste sedan lägga till svars-URL för den distribuerade appen i portalen för registrering av programmet.

För daemon-appar behöver du inte ange en omdirigerings-URI.

## <a name="client-secret"></a>Klienthemlighet
Det här alternativet anger klienthemlighet för appen konfidentiell klient. Den här hemligheten (applösenord) tillhandahålls av på programregistreringsportalen eller tillhandahålls till Azure AD under appregistrering med PowerShell AzureAD, PowerShell AzureRM eller Azure CLI.

## <a name="logging"></a>Loggning
Andra konfigurationsalternativ aktivera loggning och felsökning. Se den [loggning](msal-logging.md) artiklarna för information om hur de används.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [instansiera klientprogram med hjälp av MSAL.NET](msal-net-initializing-client-applications.md).

Lär dig mer om [instansiera klientprogram med hjälp av MSAL.js](msal-js-initializing-client-applications.md).
