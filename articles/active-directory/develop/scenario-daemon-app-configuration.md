---
title: 'Daemon för app anropande webb API: er (konfiguration) - Microsoft identity-plattformen'
description: 'Lär dig hur du skapar en daemon-app att anrop webb-API: er (konfiguration)'
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8d377db827a6548c380128624c21f4ae7896aff
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075332"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Daemon för appen att anrop webb-API: er – kod konfiguration

Lär dig hur du konfigurerar koden för daemon för programmet att anrop webb-API: er.

## <a name="msal-libraries-supporting-daemon-apps"></a>MSAL bibliotek stödjande daemon-appar

Microsoft-bibliotek som stöd för daemon-appar är:

  MSAL bibliotek | Beskrivning
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Plattformar som stöds kan skapa en daemon-program är .NET Framework och .NET Core-plattformar (inte UWP, Xamarin.iOS och Xamarin.Android som dessa plattformar används för att skapa offentliga klientprogram)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Utveckling i förlopp – förhandsversion
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Utveckling i förlopp – förhandsversion

## <a name="configuration-of-the-authority"></a>Konfiguration av behörighet

Tanke på att programmen daemon inte använda delegerade behörigheter men programbehörigheter, deras *stöds kontotyp* får inte vara *konton i en organisations katalog och personliga Microsoft-konton () till exempel Skype, Xbox, Outlook.com)*. Det finns faktiskt inga klientadministratör för att ge medgivande till daemon-program för personliga Microsoft-konton. Du måste välja *konton i min organisation* eller *konton i en organisation*.

Behörighet som anges i programkonfigurationen bör därför klient-ed (att ange ett klient-ID eller ett domännamn som är associerade med din organisation). Om du är en ISV och vill ge ett verktyg för flera innehavare kan du använda `organizations`. Men tänk på att du måste också att förklara hur du ger administratörens godkännande till dina kunder. Se [begär godkännande för en hel klient](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) information

## <a name="application-configuration-and-instantiation"></a>Programkonfiguration och instansiering

I MSAL bibliotek skickas med klientens autentiseringsuppgifter (hemlighet eller certifikat) som en parameter för program-konstruktion konfidentiell klient.

> [!IMPORTANT]
> Även om ditt program är ett konsolprogram måste som körs som en tjänst, om det är ett daemonprogram det vara ett konfidentiellt klientprogram.

### <a name="msalnet"></a>MSAL.NET

Lägg till den [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-paket i ditt program.

Användning av MSAL.NET namnområdet

```CSharp
using Microsoft.Identity.Client;
```

Daemon för programmet ska visas en `IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Här är koden för att skapa ett program med en programhemlighet:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Här är koden för att skapa ett program med ett certifikat:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

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
> [Daemon för app - hämta token för appen](./scenario-daemon-acquire-token.md)