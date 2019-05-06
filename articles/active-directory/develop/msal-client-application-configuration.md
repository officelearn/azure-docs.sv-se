---
title: Klientkonfigurationen program (Microsoft Authentication Library) | Azure
description: Läs mer om konfigurationsalternativen för offentlig klient och konfidentiell klient program i Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 3a48eea9fedd2d82f44693d58b31ee0d5c8c288d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138542"
---
# <a name="application-configuration-options"></a>Programmets konfigurationsalternativ

I koden du initiera en ny offentlig eller konfidentiell klient (eller användar-agent för MSAL.js) program för att autentisera och hämta token.  Det finns ett antal olika konfigurationsalternativ som kan anges vid initiering av klientprogrammet i MSAL. Dessa alternativ kan delas upp i två grupper:

- Registreringsalternativ, inklusive:
    - [Utfärdare av](#authority) (består av identitetsprovidern [instans](#cloud-instance) och logga in [målgrupp](#application-audience) för programmet och eventuellt klient-ID).
    - [Klient-ID](#client-id)
    - [Omdirigerings-URI](#redirect-uri)
    - [klienthemlighet](#client-secret) (för appar som konfidentiell klient).
- [Alternativ för loggning](#logging), inklusive loggningsnivå, kontroll över personliga data och namnet på komponenten med hjälp av biblioteket.

## <a name="authority"></a>Myndighet
Behörigheten är en URL som anger en katalog som MSAL kan begära token från. Det är vanligt myndigheter:

- https://login.microsoftonline.com/&lt; klient&gt;/ där &lt;klient&gt; är klient-ID för Azure AD-klient eller en domän som är associerade med den här Azure AD-klient.  Används endast för att logga in användare av en viss organisation.
- https://login.microsoftonline.com/common/. Används för att logga in användare med arbets- och skolkonton eller ett personligt Microsoft-konto.
- https://login.microsoftonline.com/organizations/. Används för att logga in användare med arbets-och skolkonton.
- https://login.microsoftonline.com/consumers/. Används för att logga in användare med endast personligt Microsoft-konto (live).

Inställningen av måste stämma överens med vad som är deklarerad i portalen för registrering av programmet.

Utfärdarens URL består av instansen och målgruppen.

Behörighet kan vara:
- en Azure Active directory-utfärdare för molnet
- en utfärdare av Azure AD B2C. Se [B2C ärendets](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- en ADFS-utfärdare. Se [ADFS stöder](https://aka.ms/msal-net-adfs-support).

Azure AD-utfärdare för molnet har två delar:
- identitetsprovidern **instans**
- inloggningen **målgrupp** för programmet

Instansen och målgrupp kan sammanfogas och anges som utfärdare av URL: en. I versioner av MSAL.NET före MSAL 3.x, var du tvungen att skapa utfärdaren själv beroende på molnet som du ville mål och logga in målgruppen.  Följande diagram visar hur utfärdarens URL består.

![Myndighet](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Molninstans
Den **instans** används för att ange om ditt program loggar användare från det offentliga molnet för Microsoft Azure eller från nationella moln. Med MSAL i din kod, Azure-molnet-instans kan ställas in med hjälp av en uppräkning eller genom att skicka Webbadressen till den [nationella molninstans](authentication-national-cloud.md#azure-ad-authentication-endpoints) som den `Instance` medlem (om du känner till den).

MSAL.NET ett undantagsfel explicit om både `Instance` och `AzureCloudInstance` har angetts. 

Om du inte anger en instans, din app är inriktat mot den offentliga Azure-moln-instansen (instansen av URL: en `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Programmet målgrupp

Logga in målgruppen är beroende av affärsbehov för ditt program:
- Om du är en verksamhetsspecifik (LOB) utvecklare, kommer du förmodligen skapa en enskild klient-program som ska användas endast i din organisation. I så fall kan behöva du ange den här organisationen, antingen genom att dess klient-ID (ID för Azure Active Directory) eller ett domännamn som är associerade med den här Azure Active Directory.
- Om du är en oberoende Programvaruleverantör, kanske du vill logga in användare med sitt arbete och skola konton i alla organisationer eller vissa organisationer (app för flera klienter), men du kanske också vill ha användare logga in med ett personligt Microsoft-konto.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Så här anger du målgruppen i din kod/konfiguration
Med MSAL i din kod kan ange du publiken med hjälp av:
- antingen Azure AD-utfärdare målgrupp uppräkningen. 
- eller klient-ID, vilket kan vara:
  - ett GUID, (ID för Azure Active Directory) för enskild klient program
  - ett domännamn som är associerade med Azure Active Directory (för enskild klient program)
- eller någon av dessa platshållare som en klient-ID i stället för uppräkningen Azure AD-utfärdare målgrupp:
    - `organizations` för ett program med flera innehavare
    - `consumers` logga in användare endast med sina personliga konton
    - `common` logga in användare med sina arbets- och skolkonto eller personligt Microsoft-konto

MSAL genereras en meningsfull undantagsfel om du anger både Azure AD-utfärdare målgrupp och klient-ID. 

Om du inte anger en målgrupp din app är inriktat mot Azure AD och personliga Microsoft-konton som en målgrupp (det vill säga `common`).

### <a name="effective-audience"></a>Effektiva målgrupp
Effektiva målgruppen för ditt program kommer att vara minst (om det finns en skärningspunkt) den målgrupp som du angav i ditt program och målgruppen som angetts i registreringen programmet. Faktiskt programregistreringsupplevelsen ([Appregistrering](https://aka.ms/appregistrations)) kan du ange målgrupp (stöds kontotyper) för programmet. Gå till [Snabbstart: Registrera ett program med Microsoft identity-plattformen](quickstart-register-app.md) för mer information.

För närvarande är det enda sättet att få ett program för att logga in användare med endast personliga Microsoft-konton att ställa in:
- inställd ”arbete och skola konton och personliga konton” app registrering målgrupp och,
- och ange målgruppen i din kod / konfigurationen `AadAuthorityAudience.PersonalMicrosoftAccount` (eller `TenantID `= ”användare”)

## <a name="client-id"></a>Klientorganisations-ID
Det unika program (klient)-ID som din app tilldelats av Azure AD när appen har registrerats.

## <a name="redirect-uri"></a>Omdirigerings-URI
Omdirigerings-URI är URI: N där identitetsprovidern skickar säkerhetstoken tillbaka. 

### <a name="redirect-uri-for-public-client-applications"></a>Omdirigerings-URI för offentliga klientprogram
För offentlig klient programutvecklare med MSAL:
- Du behöver inte skicka den ``RedirectUri`` som beräknas automatiskt av MSAL. Omdirigeringen URI har angetts till följande värden beroende på vilken plattform:

- ``urn:ietf:wg:oauth:2.0:oob`` för alla Windows-plattformar
- ``msal{ClientId}://auth`` för Xamarin Android och iOS

Men att omdirigeras URI: N måste konfigureras i den [Appregistrering](https://aka.ms/appregistrations).

![Omdirigerings-URI i portalen](media/msal-client-application-configuration/redirect-uri.png)

Det är möjligt att åsidosätta omdirigerings-URI: N med den `RedirectUri` egenskap, till exempel om du använder asynkrona meddelandeköer. Här följer några exempel på omdirigerade URI: er i så fall:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $”msauth. {Bundle.ID}://auth ”;

Mer information finns i Android ärendets och [iOS ärendets](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)

### <a name="redirect-uri-for-confidential-client-applications"></a>Omdirigerings-URI för klientprogram som konfidentiellt
Är URI: N som Azure AD kommer att kontakta tillbaka programmet med token för web apps, den omdirigerings-URI: N (eller svars-URI). Detta kan vara URL: en för webbprogrammet / webb-API om den konfidentiella är en av dessa.  Den här omdirigerings-URI måste vara registrerade i appregistreringen. Detta är särskilt viktigt när du distribuerar ett program som du ursprungligen har testat lokalt. Du måste sedan lägga till svars-URL: en för det distribuerade programmet i portalen för registrering av programmet.

För daemon-appar behöver du inte ange en omdirigerings-URI.

## <a name="client-secret"></a>Klienthemlighet
Klienthemlighet för konfidentiell klientprogrammet. Den här hemligheten (programlösenord) tillhandahålls av på programregistreringsportalen eller tillhandahålls till Azure AD vid programregistrering med PowerShell AzureAD, PowerShell AzureRM eller Azure CLI.

## <a name="logging"></a>Loggning
De andra alternativen aktivera loggning och felsökning. Mer information finns i den [loggning](msal-logging.md) för mer information om hur de används.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [instansiera klientprogram med hjälp av MSAL.NET](msal-net-initializing-client-applications.md).

Lär dig mer om [instansiera klientprogram som använder MSAL.js](msal-js-initializing-client-applications.md).
