---
title: Initiera MSAL.NET klientprogram | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att initiera offentliga klient- och konfidentiella klientprogram med hjälp av Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083983"
---
# <a name="initialize-client-applications-using-msalnet"></a>Initiera klientprogram med MSAL.NET
I den här artikeln beskrivs initiering av offentliga klientprogram och konfidentiella klientprogram med hjälp av Microsoft Authentication Library for .NET (MSAL.NET).  Mer information om klientprogramtyper och programkonfigurationsalternativ finns i [översikten](msal-client-applications.md).

Med MSAL.NET 3.x är det rekommenderade sättet att instansiera ett program `PublicClientApplicationBuilder` genom `ConfidentialClientApplicationBuilder`att använda programbyggarna: och . De erbjuder en kraftfull mekanism för att konfigurera programmet antingen från koden eller från en konfigurationsfil, eller till och med genom att blanda båda metoderna.

## <a name="prerequisites"></a>Krav
Innan du initierar ett program måste du först [registrera det](quickstart-register-app.md) så att din app kan integreras med Microsofts identitetsplattform.  Efter registreringen kan du behöva följande information (som finns i Azure-portalen):

- Klient-ID (en sträng som representerar ett GUID)
- Identitetsleverantörens URL (namngiven instans) och inloggningsmålgruppen för ditt program. Dessa två parametrar kallas gemensamt myndigheten.
- Klient-ID om du skriver en branschprogram endast för din organisation (även namnet single-tenant application).
- Programhemligheten (klienthemlig sträng) eller certifikat (av typen X509Certificate2) om det är en konfidentiell klientapp.
- För webbappar och ibland för offentliga klientappar (särskilt när appen behöver använda en mäklare) har du också angett omdirigeuri där identitetsleverantören kontaktar tillbaka ditt program med säkerhetstoken.

## <a name="ways-to-initialize-applications"></a>Olika sätt att initiera program
Det finns många olika sätt att instansiera klientprogram.

### <a name="initializing-a-public-client-application-from-code"></a>Initiera ett offentligt klientprogram från kod

Följande kod instansierar ett offentligt klientprogram, inloggade användare i det offentliga Microsoft Azure-molnet, med deras arbets- och skolkonton eller deras personliga Microsoft-konton.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Initiera ett konfidentiellt klientprogram från kod

På samma sätt instansierar följande kod ett konfidentiellt program `https://myapp.azurewebsites.net`(en webbapp som finns på) som hanterar token från användare i det offentliga Microsoft Azure-molnet, med deras arbets- och skolkonton eller deras personliga Microsoft-konton. Programmet identifieras med identitetsprovidern genom att dela en klienthemlighet:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Som du kanske vet kanske du vill dela ett certifikat med Azure AD i produktionen i stället för att använda en klienthemlighet. Koden skulle då vara följande:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Initiera ett offentligt klientprogram från konfigurationsalternativ

Följande kod instansierar ett offentligt klientprogram från ett konfigurationsobjekt, som kan fyllas i programmässigt eller läsas från en konfigurationsfil:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Initiera ett konfidentiellt klientprogram från konfigurationsalternativ

Samma typ av mönster gäller för konfidentiella klientprogram. Du kan också lägga `.WithXXX` till andra parametrar med hjälp av modifierare (här ett certifikat).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Byggare modifierare

I kodavsnitten med hjälp av programbyggare `.With` kan ett antal metoder användas som `.WithCertificate` modifierare (till exempel ). `.WithRedirectUri` 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modifierare som är gemensamma för offentliga och konfidentiella klientprogram

De modifierare som du kan ställa in på en offentlig klient eller konfidentiell klientprogrambyggare är:

|Modifierare | Beskrivning|
|--------- | --------- |
|`.WithAuthority()`7 åsidosättningar | Anger standardbehörigheten för programmet till en Azure AD-myndighet, med möjlighet att välja Azure Cloud, målgruppen, klienten (klient-ID eller domännamn) eller direkt tillhandahålla uri-myndigheten.|
|`.WithAdfsAuthority(string)` | Anger att standardmyndigheten för programmet ska vara ADFS-behörighet.|
|`.WithB2CAuthority(string)` | Anger att programstandardutfärdaren ska vara en Azure AD B2C-behörighet.|
|`.WithClientId(string)` | Åsidosätter klient-ID.|
|`.WithComponent(string)` | Anger namnet på biblioteket med MSAL.NET (av telemetriskäl). |
|`.WithDebugLoggingCallback()` | Om det anropas `Debug.Write` anropas anropar programmet helt enkelt aktivera felsökningsspårningar. Se [Loggning](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) för mer information.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Ange extra frågeparametrar på programnivå som ska skickas i all autentiseringsbegäran. Detta är åsidosättbart på varje token förvärvsmetod `.WithExtraQueryParameters pattern`nivå (med samma ).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Aktiverar avancerade scenarier som att konfigurera för en HTTP-proxy eller tvinga MSAL att använda en viss HttpClient (till exempel i ASP.NET Core-webbappar/API:er).|
|`.WithLogging()` | Om det anropas anropar programmet en motringning med felsökningsspårningar. Se [Loggning](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) för mer information.|
|`.WithRedirectUri(string redirectUri)` | Åsidosätter standardomdirigerings-URI:n. När det gäller offentliga klientprogram kommer detta att vara användbart för scenarier som involverar mäklaren.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Anger det ombud som används för att skicka telemetri.|
|`.WithTenantId(string tenantId)` | Åsidosätter klient-ID:t eller klientbeskrivningen.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modifierare som är specifika för Xamarin.iOS-program

De modifierare som du kan ställa in på en offentlig klientprogrambyggare på Xamarin.iOS är:

|Modifierare | Beskrivning|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Endast Xamarin.iOS**: Ställer in säkerhetsgruppen för iOS-nyckelring (för cachebeständighet).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modifierare som är specifika för konfidentiella klientprogram

De modifierare som du kan ställa in på en konfidentiell klientprogrambyggare är:

|Modifierare | Beskrivning|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Anger certifikatet som identifierar programmet med Azure AD.|
|`.WithClientSecret(string clientSecret)` | Anger klienthemligheten (applösenord) som identifierar programmet med Azure AD.|

Dessa modifierare utesluter varandra. Om du tillhandahåller båda, kommer MSAL kasta ett meningsfullt undantag.

### <a name="example-of-usage-of-modifiers"></a>Exempel på användning av modifierare

Anta att ditt program är ett affärsprogram som bara är för din organisation.  Då kan du skriva:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Där det blir intressant är att programmeringen för nationella moln nu har förenklats. Om du vill att ditt program ska vara ett program med flera innehavare i ett nationellt moln kan du skriva, till exempel:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Det finns också en åsidosättning för ADFS (ADFS 2019 stöds för närvarande inte):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Slutligen, om du är en Azure AD B2C-utvecklare, kan du ange din klient organisation så här:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
