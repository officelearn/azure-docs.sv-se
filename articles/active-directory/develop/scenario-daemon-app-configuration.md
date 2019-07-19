---
title: 'Daemon-app som anropar webb-API: er (app-konfiguration) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en daemon-app som anropar webb-API: er (app-konfiguration)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 705545fd5167087be1a001c45f58907d6ff225e8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277829"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Daemon-app som anropar webb-API: er – kod konfiguration

Lär dig hur du konfigurerar koden för daemon-programmet som anropar webb-API: er.

## <a name="msal-libraries-supporting-daemon-apps"></a>MSAL-bibliotek med stöd för daemon-appar

Microsoft-bibliotek som stöder daemon-appar är:

  MSAL-bibliotek | Beskrivning
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Plattformar som stöds för att bygga ett daemon-program är .NET Framework och .NET Core-plattformar (inte UWP, Xamarin. iOS och Xamarin. Android som dessa plattformar används för att bygga offentliga klient program)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Utveckling pågår – i offentlig för hands version
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Utveckling pågår – i offentlig för hands version

## <a name="configuration-of-the-authority"></a>Behörighets konfiguration

Med tanke på att daemon-programmen inte använder delegerade behörigheter, men program behörigheter, kan deras *konto typ som stöds* inte vara *konton i någon organisations katalog och personliga Microsoft-konton (till exempel Skype, Xbox, Outlook.com)* . Det finns ingen innehavaradministratör för att ge ett medgivande till daemon-programmet för Microsoft-personliga konton. Du måste välja *konton i min organisation* eller *konton i alla organisationer*.

Den auktoritet som anges i program konfigurationen ska därför vara klient-Ed (ange ett klient-ID eller ett domän namn som är kopplat till din organisation).

Om du är en ISV och vill tillhandahålla ett verktyg för flera innehavare kan du använda `organizations`. Men tänk på att du även måste förklara för kunderna hur de ska bevilja administrativt medgivande. Mer information finns i [begära medgivande för en hel klient](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) . Det finns även en begränsning i MSAL som `organizations` endast tillåts när klientautentiseringsuppgifterna är en program hemlighet (inte ett certifikat). Se [MSAL.net-bugg #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Program konfiguration och instansiering

I MSAL-bibliotek skickas klientens autentiseringsuppgifter (hemlighet eller certifikat) som en parameter för den konfidentiella klient program konstruktionen.

> [!IMPORTANT]
> Även om ditt program är ett konsol program som körs som en tjänst, om det är ett daemon-program måste det vara ett konfidentiellt klient program.

### <a name="msalnet"></a>MSAL.NET

Lägg till [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-paketet i ditt program.

Använd MSAL.NET-namnrymd

```CSharp
using Microsoft.Identity.Client;
```

Daemon-appen visas av en`IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Här är koden för att bygga ett program med en program hemlighet:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Här är koden för att bygga ett program med ett certifikat:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

I stället för en klient hemlighet eller ett certifikat kan det konfidentiella klient programmet också bevisa sin identitet med hjälp av klientens intyg. Det här avancerade scenariot beskrivs i [klient kontroll](msal-net-client-assertions.md)


### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Daemon-app – hämtar token för appen](./scenario-daemon-acquire-token.md)
