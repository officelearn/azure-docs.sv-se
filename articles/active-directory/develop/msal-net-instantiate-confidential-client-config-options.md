---
title: Skapa en instans av en konfidentiell klient-app med alternativ (Microsoft Authentication Library för .NET) | Azure
description: Lär dig mer om att skapa en instans av ett konfidentiellt klientprogram konfigurationsalternativ med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60b3b2fa2cdb7808e45f9142f1c1a351f470af50
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158842"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Skapa en instans av ett konfidentiellt klientprogram konfigurationsalternativ med hjälp av MSAL.NET

Den här artikeln beskriver hur du skapa en instans av en [konfidentiell klientprogram](msal-client-applications.md) med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).  Programmet instantieras konfigurationsalternativ som definierats i en fil med inställningar.

Innan du initierar ett program, måste du först [registrera](quickstart-register-app.md) den så att din app kan integreras med Microsoft identity-plattformen. Efter registreringen behöver du följande information (som finns i Azure portal):

- Klient-ID (en sträng som representerar ett GUID)
- Den URL för identitetsprovider (med namnet instansen) och logga in-målgruppen för ditt program. Dessa två parametrar kallas sammantaget behörighet.
- Klient-ID om du skriver ett verksamhetsspecifikt program endast för din organisation (som också namngivna enda klient program).
- Programhemlighet (klientens hemliga sträng) eller certifikat (av typen X509Certificate2) om det är en konfidentiell klient-app.
- För web apps, och ibland för offentlig klient apps (i synnerhet när din app behöver använda en asynkron meddelandekö), måste du också ställa redirectUri där identitetsprovidern kommer att kontakta tillbaka ditt program med säkerhetstoken.

## <a name="configure-the-application-from-the-config-file"></a>Konfigurera programmet från konfigurationsfilen
Namnet på egenskaperna för alternativen i MSAL.NET matcha namnet på egenskaperna för den `AzureADOptions` i ASP.NET Core, så du behöver inte skriva sammanlänkande kod.

En ASP.NET Core-konfiguration beskrivs i en *appsettings.json* fil:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Med början i MSAL.NET v3.x kan konfigurera du konfidentiell klientprogrammet från konfigurationsfilen. De klasser som rör appkonfigurationen finns i den `Microsoft.Identity.Client.AppConfig` namnområde.

I klassen där du vill konfigurera och skapa en instans av programmet, måste du deklarera ett `ConfidentialClientApplicationOptions` objekt.  Bind konfigurationen läsa från källdatabasen (inklusive appconfig.json-filen) till instansen av programmet alternativ:

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

På så sätt kan innehållet i avsnittet ”AzureAD” i den *appsettings.json* fil som ska bindas till motsvarande egenskaper för den `ConfidentialClientApplicationOptions` objekt.  Skapa sedan en `ConfidentialClientApplication` objekt:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Lägg till runtime-konfiguration
I ett konfidentiellt klientprogram behöver du vanligtvis ett cacheminne per användare. Därför måste du skaffa cachen som hör till användarkontot och informera builder för program som du vill använda den. På samma sätt kan du ha en dynamiskt beräknade omdirigerings-URI. I det här fallet är koden följande:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

