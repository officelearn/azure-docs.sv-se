---
title: Initiera klientprogram (Microsoft Authentication Library för .NET) | Azure
description: Läs mer om att initiera offentlig klient- och konfidentiella klientprogram som använder Microsoft Authentication Library för .NET (MSAL.NET).
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
ms.openlocfilehash: c6197d472bdfaf03c9f99baa7691354e735cc91e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075812"
---
# <a name="initialize-client-applications-using-msalnet"></a>Initiera klientprogram med hjälp av MSAL.NET
Den här artikeln beskriver initierar offentlig klient och konfidentiell klient-program med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).  Mer information om klienten programtyper och programmets konfigurationsalternativ i [översikt](msal-client-applications.md).

Med MSAL.NET 3.x, är det rekommenderade sättet att skapa en instans av ett program med hjälp av programmet builders: `PublicClientApplicationBuilder` och `ConfidentialClientApplicationBuilder`. De ger en kraftfull mekanism för att konfigurera programmet antingen från kod, eller från en konfigurationsfil eller genom att även blanda båda metoderna.

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du initierar ett program, måste du först [registrera den](quickstart-register-app.md) så att din app kan integreras med Microsoft identity-plattformen.  Efter registreringen behöver du följande information (som finns i Azure portal):

- Klient-ID (en sträng som representerar ett GUID)
- Den URL för identitetsprovider (med namnet instansen) och logga in-målgruppen för ditt program. Dessa två parametrar kallas sammantaget behörighet.
- Klient-ID om du skriver ett verksamhetsspecifikt program endast för din organisation (som också namngivna enda klient program).
- Programhemlighet (klientens hemliga sträng) eller certifikat (av typen X509Certificate2) om det är en konfidentiell klient-app.
- För web apps, och ibland för offentlig klient apps (i synnerhet när din app behöver använda en asynkron meddelandekö), måste du också ställa redirectUri där identitetsprovidern kommer att kontakta tillbaka ditt program med säkerhetstoken.

## <a name="ways-to-initialize-applications"></a>Sätt att initiera program
Det finns många olika sätt att skapa en instans av klientprogram.

### <a name="initializing-a-public-client-application-from-code"></a>Initierar en offentlig klientprogram från kod

Följande kod skapar en instans av en offentlig klientprogrammet, logga in användare i det offentliga molnet i Microsoft Azure, med sitt arbete och skola konton eller deras personliga Microsoft-konton.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Initierar ett konfidentiellt klientprogram från kod

På samma sätt följande kod skapar en instans av ett konfidentiellt program (en webbapp finns på `https://myapp.azurewebsites.net`) hanterar token från användare i det offentliga molnet i Microsoft Azure, med sitt arbete och skola konton eller deras personliga Microsoft-konton. Programmet identifieras med identitetsprovidern genom att dela en klienthemlighet:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Som du kanske vet i produktion i stället för att använda en klienthemlighet, kanske du vill dela ett certifikat med Azure AD. Koden blir följande:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Initierar en offentlig klientprogram från konfigurationsalternativ

Följande kod skapar en instans av en offentlig klientprogram från ett konfigurationsobjekt som kan vara ifyllda programmässigt eller läsa från en konfigurationsfil:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Initierar ett konfidentiellt klientprogram från konfigurationsalternativ

Samma typ av mönster gäller konfidentiell klientprogram. Du kan också lägga till andra parametrar med `.WithXXX` modifierare (här ett certifikat).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Builder modifierare

I kodfragment med hjälp av programmet builders, ett antal `.With` metoder kan användas som modifierare (till exempel `.WithCertificate` och `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modifierare som är gemensamma för offentliga och konfidentiella klientprogram

Modifierare som du kan ställa in på en offentlig klient eller en konfidentiell klient programmet builder är:

|Parameter | Beskrivning|
|--------- | --------- |
|`.WithAuthority()` 7 åsidosättningar | Anger standard-utfärdare program till en Azure AD-auktoritet, med möjlighet att välja den Azure-molnet målgrupp, klient (klient-ID eller domain name), eller att tillhandahålla direkt utfärdar-URI.|
|`.WithAdfsAuthority(string)` | Anger standard-utfärdare för programmet ska vara en ADFS-utfärdare.|
|`.WithB2CAuthority(string)` | Anger standard-utfärdare för programmet ska vara en Azure AD B2C-utfärdare.|
|`.WithClientId(string)` | Åsidosättningar av klient-ID.|
|`.WithComponent(string)` | Anger namnet på biblioteket med hjälp av MSAL.NET (för telemetri orsaker). |
|`.WithDebugLoggingCallback()` | Om den anropas programmet anropar `Debug.Write` bara aktivera felsökning spårningar. Se [loggning](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) för mer information.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Ange program på extra frågeparametrar som ska skickas i alla autentiseringsbegäran. Det här är åsidosättningsbar på varje nivå för tokenförvärv-metoden (med samma `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Möjliggör avancerade scenarier, till exempel konfigurera för en HTTP-proxy eller för att framtvinga MSAL för att använda en viss HttpClient (till exempel i ASP.NET Core web apps/API: er).|
|`.WithLogging()` | Om den anropas anropar programmet en motringning med felsökning av spår. Se [loggning](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) för mer information.|
|`.WithRedirectUri(string redirectUri)` | Åsidosätter standard omdirigerings-URI. När det gäller offentliga klientprogram är det här användbart för scenarier som omfattar den asynkrona meddelandekön.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Anger delegaten används för att skicka telemetri.|
|`.WithTenantId(string tenantId)` | Åsidosätter klient-ID eller beskrivning för klienten.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modifierare som är specifika för Xamarin.iOS-program

Modifierare som du kan ange en offentlig klient programmet builder på Xamarin.iOS är:

|Parameter | Beskrivning|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.iOS endast**: Anger gruppen iOS nyckelring (för cachepersistence).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modifierare som är specifika för konfidentiell klientprogram

Modifierare som du kan ange en konfidentiell klient programmet builder är:

|Parameter | Beskrivning|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Anger det certifikat som identifierar programmet med Azure AD.|
|`.WithClientSecret(string clientSecret)` | Anger klienthemlighet (lösenord för app) identifierar programmet med Azure AD.|

Dessa modifierare kan inte anges samtidigt. Om du anger både genereras MSAL en meningsfull undantagsfel.

### <a name="example-of-usage-of-modifiers"></a>Exempel på användning av modifierare

Anta att dina program är en line-of-business-program, som är endast för din organisation.  Du kan sedan skriva:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Där blir det intressanta är att programmering för nationella moln har förenklad nu. Om du vill att programmet ska vara ett program med flera innehavare i ett nationella moln, som du kan skriva, till exempel:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Det finns också en åsidosättning för AD FS (AD FS 2019 är för närvarande stöds inte):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Slutligen kan du ange dina klient så här om du är en Azure AD B2C-utvecklare:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
